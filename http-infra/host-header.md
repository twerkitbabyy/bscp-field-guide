# HTTP Host Header Attacks

These attacks abuse an application's trust in the `Host` header (or related
forwarding headers), which can influence links, access decisions, routing, and
caching.

## How to identify it

Test how the application reacts when the `Host` header is changed, duplicated,
or supplemented with forwarding headers (`X-Forwarded-Host` and similar). Clues
include password-reset links built from the host, admin panels "only available
locally", and responses that route or cache based on the host.

## Techniques and variants

| Behaviour | Approach |
| --- | --- |
| Reset link built from the host | Poison it (via the Host or `X-Forwarded-Host`) so the token is sent to a server you control. |
| Access gated on "local" host | Set `Host: localhost` to satisfy the check. |
| Host reflected into a cached script URL | Use an ambiguous/duplicate Host to point an imported script at your server and poison the cache. |
| Front-end routes by Host | Routing-based SSRF: set the Host to an internal address to reach internal services. |
| Direct Host validated | Bypass with an absolute URL in the request line, or a connection-state attack where only the first request on a connection is validated. |

## Prevention

- Don't use the `Host` header (or `X-Forwarded-Host`) to build absolute URLs,
  links, or security decisions. Use a trusted, server-configured domain instead.
- Validate the incoming `Host` against an allowlist of expected values and
  reject anything else; disable support for arbitrary override/forwarding
  headers unless explicitly needed.
- Don't trust the host for access control; enforce real authentication for
  internal/admin functionality.
- Ensure consistent request parsing across the stack to avoid ambiguous-host
  and connection-state discrepancies.

> Reference: PortSwigger Web Security Academy, [HTTP Host header attacks](https://portswigger.net/web-security/host-header)
