# 06. Technique reference

A quick reference to jog your memory once you've already recognised the vulnerability (that part is the [playbook](./03-recognition-playbook.md)). This is deliberately not a payload encyclopedia. For the full catalogue, [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) and [HackTricks](https://book.hacktricks.xyz) are the references and they do it better than I ever would. What's here is the essentials, plus the parts my own prep actually leaned on.

Examples use generic goals (read a file, exfiltrate a cookie to a server you control). Swap in whatever the task in front of you asks for.

---

## SQL injection

- **Probe:** a single quote in any parameter, header or cookie that might reach a query. Watch for an error, a different result set, or changed behaviour.
- **In-band:** `' OR 1=1-- ` to break logic; `UNION SELECT` to pull other tables (find the column count with `ORDER BY n` or `UNION SELECT NULL,NULL...`, then which columns take text).
- **Blind boolean/error/time:** `' AND 1=1` vs `' AND 1=2`; conditional errors or a conditional `SLEEP`/`pg_sleep` to extract data character by character.
- **Automate the blind stuff with sqlmap** against the captured request. Doing it by hand is a time sink.
- **WAF:** encode keywords (XML entity encoding, comment breaks). This is where Hackvertor earns its place.

## OS command injection

- **Probe:** a shell metacharacter plus a benign observable command (`;`, `&`, `|`, newline, backticks, `$()`). If input sits in quotes, close them first.
- **In-band:** output reflected in the response.
- **Blind:** confirm with a timed command (a ping/sleep), redirect output to a web-readable file, or go out-of-band to a Collaborator host.

## SSTI

- **Probe:** `${7*7}`, `{{7*7}}`, `<%= 7*7 %>`. If you get `49`, the server is evaluating it.
- **Identify the engine first** (a polyglot fuzz string usually errors out with the engine name). The escalation path is engine-specific, so confirm whether it's ERB, Jinja/Tornado, Freemarker, Handlebars, Django, etc. before reaching for RCE or secret disclosure.

## XXE

- **Probe:** any XML body, SOAP API, or XML-backed format (SVG, DOCX).
- **File read:** external entity with `SYSTEM "file:///etc/passwd"` referenced in a reflected field.
- **SSRF:** point the entity at an internal URL instead of a file.
- **Blind:** error-based with an external DTD, or OOB with parameter entities (`%xxe;`) when normal entities are filtered; exfiltrate via a malicious external DTD.
- **No DOCTYPE control:** XInclude. **Upload path:** a malicious SVG.

## Insecure deserialization

- **Spot it:** serialized blobs in cookies/params. PHP decodes to `O:`/`a:`; Java is Base64 `rO0AB`. Corrupting the blob often leaks the framework in the error. Freddy flags these.
- **Tamper** attributes or types for quick wins; use a gadget-chain generator for RCE: **ysoserial** (Java), **phpggc** (PHP). The error naming the library tells you which chain family to use.
- Signed blobs fall to a leaked secret key.

---

## Access control

- **Probe:** change IDs, roles and HTTP methods; hit admin paths directly; check each step of multi-step flows on its own.
- Look for role flags in cookies/JSON, IDOR on ID/GUID params (GUIDs often leak elsewhere), data in a 302 body, `X-Original-URL`/`X-Rewrite-URL` to bypass a path filter, method swaps, and `Referer`-trusting sub-actions.

## Authentication

- Username enumeration via different responses, subtle text differences, response timing, or account-lock behaviour.
- Reset flows that carry a `username` you can change; host-header-poisoned reset links to capture the token.
- MFA that's a UI-only step, or tied to a cookie you can retarget and brute-force.
- Predictable "remember me" cookies (reversible encoding of user + unsalted hash) you can forge or crack offline.

## JWT

- Three dot-separated Base64url parts. Decode the header, read `alg`.
- **No verification:** change a claim, send. **`alg: none`:** strip the signature, keep the trailing dot.
- **HS256 weak key:** crack the HMAC offline, forge. **RS256:** `jwk` header injection (embed your key) or `jku` (host your key). **`kid` path traversal** to a predictable/empty file.
- JWT Editor automates all of this.

## OAuth

- Tamper identity fields posted alongside the token (implicit flow).
- Account-linking endpoints with no `state` are CSRF-prone.
- Weak `redirect_uri` validation leaks the code/token; chain an open redirect when the domain is locked but a redirect exists.

## CSRF

- Find a state-changing request and check the token: present? validated? tied to the session? `SameSite`?
- Bypasses: no token at all, token only checked per-method, token only checked when present, token not tied to session, token tied to a non-session cookie (inject it), double-submit (invent the value), `Referer`-only checks (suppress or satisfy the Referer).

---

## XSS

- **Probe:** a harmless marker, see how it comes back (HTML body, attribute, JS string, URL). For DOM, trace a source (`location`, `postMessage`) into a sink (`innerHTML`, `document.write`).
- Context decides the payload: break out of an attribute or JS string, or work within an allowed subset (custom tags, some SVG). When quotes/backslashes are escaped but the surrounding block isn't, break the block.
- **Impact:** show execution in the victim's context (a request as them, or exfiltrating a non-HttpOnly cookie to a server you control). Keep proofs benign.
- **WAF/obfuscation is where the real exam time goes.** Hackvertor for encoding chains, array notation instead of `(...)`, operators to chain expressions without `;`.

## CORS

- Find a credentialed, sensitive endpoint (`Access-Control-Allow-Credentials: true`), then test what origins it reflects: any origin, the `null` origin (sandboxed iframe), or any subdomain / insecure protocol (chain an XSS subdomain).

---

## SSRF

- **Probe:** a param carrying a URL/hostname. Point it at loopback or an internal range; confirm blind with OOB.
- Bypasses: alternative loopback forms (`127.1`, decimal, octal), double URL-encode blocked keywords, or chain an open redirect when there's an allowlist. Cloud metadata endpoints are the classic internal target.

## Path traversal

- **Probe:** a param carrying a filename/path; request a known file.
- Bypasses by filter: absolute path, non-recursive stripping (`....//`), double URL-encode the slash (`%252f`), keep a required base prefix then traverse, null byte before a required extension (legacy).

## File upload

- Find where uploads are served and whether that path executes scripts.
- Bypasses by validation: spoof the Content-Type, path-traversal the filename into an executable dir, map a custom extension via `.htaccess` (blocklist), obfuscate the extension (allowlist), or a polyglot that's a valid image carrying code.

---

## HTTP request smuggling

- Arises from front-end/back-end disagreement on message length (`Content-Length` vs `Transfer-Encoding`, or HTTP/2 downgrades). Confirm with a crafted request and a delayed/anomalous response.
- Families: CL.TE, TE.CL, TE obfuscation, and the HTTP/2 variants (CL.0, H2.CL/H2.TE, CRLF injection). Used to bypass front-end controls, capture other users' requests, or deliver reflected payloads.

## HTTP Host header attacks

- Test changing, duplicating, or supplementing the `Host` (`X-Forwarded-Host`).
- Uses: poison a reset link, satisfy a "local only" check with `localhost`, reflect a duplicate Host into a cached script, or routing-based SSRF to internal hosts (bypass direct validation with an absolute-URL request line or a connection-state attack).

## Web cache poisoning

- Find input the response reflects but the cache ignores (unkeyed header, cookie, or param). Param Miner finds them. Use a cache buster while testing, read `X-Cache`/`Age`, drop the buster to poison.
- Angles: unkeyed header into a resource URL, unkeyed cookie reflected inline, header combinations, `Vary: User-Agent` targeting, unkeyed query params (`utm_*`), parameter cloaking, fat GET, URL normalisation.

## Information disclosure

- Recon first: `robots.txt`, `sitemap.xml`, HTML comments, error messages (type-mismatch to leak the stack), debug pages, backup/temp files (`.bak`, `~`, `.old`, `.swp`), exposed `.git`, and `TRACE` to reveal proxy-injected headers. A leaked secret key or credential is usually the real prize.
