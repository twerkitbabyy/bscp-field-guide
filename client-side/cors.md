# Cross-Origin Resource Sharing (CORS)

CORS misconfigurations can let a malicious site read responses from a target
that it shouldn't, exposing sensitive data when the target both reflects
untrusted origins and allows credentials.

## How to identify it

Find an endpoint that returns sensitive data and responds with
`Access-Control-Allow-Credentials: true`. Then test how it decides
`Access-Control-Allow-Origin` by sending different `Origin` values and watching
what it echoes back.

## Techniques and variants

| Server behaviour | Why it's exploitable |
| --- | --- |
| Reflects any `Origin` | Any site can make a credentialed cross-origin read of the data. |
| Trusts the `null` origin | A sandboxed iframe can produce `Origin: null` to satisfy the check. |
| Trusts all subdomains / insecure protocols | An XSS (or HTTP) subdomain becomes a trusted origin to run the read from. |

The impact is reading authenticated data (API keys, account details) from the
victim's session via a cross-origin request; keep any proof-of-concept limited
to demonstrating the read.

## Prevention

- Reflect an `Origin` back only after checking it against a strict allowlist of
  exact, trusted origins.
- Never combine `Access-Control-Allow-Credentials: true` with a wildcard or a
  reflected arbitrary origin, and don't trust the `null` origin.
- Scope trust narrowly, avoid blanket trust of all subdomains, and don't allow
  insecure (HTTP) origins for sensitive endpoints.
- Remember CORS governs reading cross-origin responses; it is not a substitute
  for CSRF protection on state-changing requests.

> Reference: PortSwigger Web Security Academy, [CORS](https://portswigger.net/web-security/cors)
