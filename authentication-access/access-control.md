# Access Control

Broken access control lets a user act outside their intended permissions:
reaching admin functionality (vertical) or another user's data (horizontal).

## How to identify it

Enumerate admin and per-user functionality, then test whether the controls
actually hold: change identifiers, roles, and HTTP methods; access privileged
URLs directly; and check each step of multi-step flows independently. Review
`robots.txt`, `sitemap.xml`, and front-end JavaScript for paths that are hidden
rather than protected.

## Techniques and variants

| Weakness | Test |
| --- | --- |
| Admin only hidden, not protected | Visit the path revealed in `robots.txt` or JS directly. |
| Role from a request value | Flip a role cookie or inject a role field the response reveals. |
| IDOR (ID in a parameter) | Change the user ID/GUID to another user's; GUIDs often leak elsewhere in the app. |
| Data leaked despite redirect | Read the body of a 302, the data may be present before the redirect. |
| Platform-level URL filter | Try `X-Original-URL` / `X-Rewrite-URL` to reach a blocked path. |
| Method-based control | Change the HTTP method (e.g. POST→GET) where only one method is checked. |
| Referer-based control | Supply a `Referer` pointing at the admin area where sub-actions trust it. |
| Multi-step process | Replay only the final confirmation step with a lower-privileged session. |
| Static file IDOR | Alter sequential filenames (transcripts, reports) to reach others'. |

## Prevention

- Deny by default; require an explicit, server-side authorisation check on every
  request, for every step, resource, and HTTP method.
- Base decisions on the authenticated session, never on a client-supplied value
  such as a role field, Referer, or custom URL-override header.
- Use unpredictable identifiers, but never rely on obscurity alone, still
  enforce ownership checks.
- Centralise access-control logic and apply it consistently rather than
  per-endpoint.

> Reference: PortSwigger Web Security Academy, [Access control](https://portswigger.net/web-security/access-control)
