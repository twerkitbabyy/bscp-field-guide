# XML External Entity (XXE) Injection

XXE arises when an application parses XML that defines external entities and the
parser resolves them, letting an attacker read files, reach internal services,
or exfiltrate data.

## How to identify it

Look anywhere XML is processed: requests with an XML body (stock checks, SOAP
APIs, import features) and file formats that are XML under the hood (SVG, DOCX).
Even forms that don't appear to use XML may be parsed as XML server-side.
Errors mentioning an XML parser are a strong hint.

## Techniques and variants

| Situation | Approach |
| --- | --- |
| XML body, a value is reflected | Declare an external entity pointing at a local file and reference it in the reflected field. |
| Goal is an internal service | Point the entity at an internal URL (e.g. a cloud metadata endpoint) instead of a file, XXE as SSRF. |
| Blind, parser errors visible | Use an external DTD that forces a parse error containing the file contents. |
| You don't control the DOCTYPE | Use XInclude to pull in a file via an injected element. |
| File upload accepts XML-based formats | Upload a malicious SVG whose content renders the targeted file back to you. |
| Fully blind | Confirm with an out-of-band interaction to a collaborator; if normal entities are blocked, use XML parameter entities; to exfiltrate, host a malicious external DTD that sends file contents out-of-band. |

## Prevention

- Disable DTDs (document type definitions) entirely in the XML parser. If that
  is not possible, disable external entities and external DTD loading
  specifically. This is the core fix and stops nearly all variants.
- Prefer a less complex data format such as JSON where feasible.
- Keep parser libraries patched, and apply the hardening guidance for your
  specific parser.

> Reference: PortSwigger Web Security Academy, [XXE injection](https://portswigger.net/web-security/xxe)
