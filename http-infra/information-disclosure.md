# Information Disclosure

Information disclosure is the unintentional exposure of sensitive data
(credentials, source code, internal paths, framework details) that often enables
further attacks.

## How to identify it

Treat reconnaissance as the first step on any target. Review `robots.txt` and
`sitemap.xml`, search HTML comments, provoke errors with unexpected input, and
probe for common debug and backup artefacts.

## Techniques and variants

| Source | What it leaks |
| --- | --- |
| Verbose error messages | Framework, version, and stack details from a type-mismatch or malformed input. |
| Debug pages | Environment variables and secrets (e.g. a framework secret key) via an exposed debug endpoint. |
| Backup / temp files | Source code and hard-coded credentials from `.bak`, `~`, `.old`, `.swp` files. |
| Version control (`/.git`) | History diffs that still contain removed secrets. |
| Proxy-injected headers | Internal authorization headers discoverable via the `TRACE` method. |

These findings are usually a means to an end: a leaked secret key enables token
forgery or deserialization attacks; leaked credentials enable direct access.

## Prevention

- Return generic error messages to users and log the detail server-side; never
  expose stack traces in production.
- Disable debug endpoints and directory listing in production, and keep backup,
  temp, and version-control files out of the web root.
- Don't hard-code secrets in source; manage them via a secrets store, and rotate
  anything that has ever been committed.
- Review what reconnaissance surfaces (robots, comments, headers) and remove
  anything sensitive.

> Reference: PortSwigger Web Security Academy, [Information disclosure](https://portswigger.net/web-security/information-disclosure)
