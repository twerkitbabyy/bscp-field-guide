# Server-Side Request Forgery (SSRF)

SSRF makes the server issue requests to a destination the attacker chooses,
reaching internal services, cloud metadata endpoints, or the loopback interface
that the attacker couldn't reach directly.

## How to identify it

Look for parameters that carry a URL, hostname, or path that the server fetches, such as stock checkers,
webhooks, URL previews or imports. Point them at the loopback
address or an internal range and watch the response; if nothing is reflected,
confirm blindly with an out-of-band interaction.

## Techniques and variants

| Situation | Approach |
| --- | --- |
| No filtering | Target `http://localhost/…` or an internal admin path directly. |
| Another internal host | Brute-force the internal range (e.g. last octet) and filter for a live admin service. |
| Blocklist of hosts/keywords | Use alternative loopback representations (shortened, decimal, octal) and encode blocked keywords, e.g. double URL-encoding. |
| Strict allowlist on the URL | Chain an open redirect in the app so the allowed URL redirects to the internal target. |
| Fully blind | Confirm with an out-of-band callback to a collaborator host. |

Cloud metadata endpoints are a common high-value target; navigate the path
level by level as each response reveals the next segment.

## Prevention

- Validate the destination against a strict allowlist of permitted hosts and
  schemes; prefer allowlisting over blocklisting, which is easily bypassed.
- Resolve and check the target address, and block requests to private,
  loopback, and link-local ranges, including after redirects (don't blindly
  follow them).
- Don't reflect raw responses from the fetched resource back to the user.
- Segment the network and require authentication on internal services so they
  don't implicitly trust requests from the application server.

> Reference: PortSwigger Web Security Academy, [SSRF](https://portswigger.net/web-security/ssrf)
