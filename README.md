# Web Security Notes

Reference notes on web application vulnerability classes, how to identify each
one, the common variants, and how to prevent it. Written while working through
the [PortSwigger Web Security Academy](https://portswigger.net/web-security)
and preparing for the BSCP.

These are study and reference notes. They focus on recognising a vulnerability
quickly and understanding both how it is exploited and how it is fixed.

## Scope and ethics

Everything here applies to systems you own or are explicitly authorised to
test, such as intentionally vulnerable labs. Testing systems without permission
is illegal. The goal of these notes is to build the knowledge needed to find
and remediate these issues: defensive understanding through offensive testing.

## Structure

Grouped by vulnerability family:

| Folder | Covers |
| --- | --- |
| `injection/` | SQL injection, OS command injection, SSTI, XXE |
| `authentication-access/` | Authentication, access control, JWT, OAuth, CSRF, business logic |
| `client-side/` | XSS, CORS |
| `server-side/` | SSRF, path traversal, file upload |
| `http-infra/` | Request smuggling, host header attacks, cache poisoning, information disclosure |

Each note follows the same shape: **how to identify it → techniques and
variants → prevention**.

See also [`methodology.md`](./methodology.md) for the black-box triage approach
I use to work through an unknown application.

## Attribution

The vulnerability classes and lab scenarios referenced here are documented in
the free [PortSwigger Web Security Academy](https://portswigger.net/web-security).
Explanations are my own; where you want the full theory, follow the links to the
original material.
