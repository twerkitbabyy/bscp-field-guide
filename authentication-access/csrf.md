# Cross-Site Request Forgery (CSRF)

CSRF tricks a logged-in user's browser into sending a state-changing request
they didn't intend, succeeding when the server relies only on automatically-sent
credentials (cookies) to authorise an action.

## How to identify it

Find a state-changing request (change email, change password) and check how it
is protected: is there a CSRF token, is it validated, is it tied to the session,
and does the cookie use `SameSite`? Then probe the robustness of whatever
defence exists.

## Techniques and variants

| Defence weakness | Bypass |
| --- | --- |
| No token / no SameSite / no Referer check | Auto-submitting cross-site form. |
| Token only checked on one method | Switch the method (e.g. POST→GET). |
| Token only checked when present | Omit the token parameter entirely. |
| Token not tied to the session | Use a valid token from your own account. |
| Token tied to a non-session cookie | Set that cookie in the victim via header injection, then submit with a matching token. |
| Duplicated token (cookie == param) | Inject a cookie value and reuse it in the form ("double submit"). |
| Referer checked only if present | Suppress the Referer with a referrer-policy meta tag. |
| Referer substring-matched | Place the trusted domain in your URL so it appears in the Referer. |

## Prevention

- Use anti-CSRF tokens that are unpredictable, tied to the user's session, and
  validated server-side on every state-changing request regardless of method.
- Set `SameSite` (Lax or Strict) on session cookies as defence in depth.
- Don't rely on Referer checks as the primary control; validate it strictly if
  used at all.
- For sensitive actions, re-authenticate or require the current password.

> Reference: PortSwigger Web Security Academy, [CSRF](https://portswigger.net/web-security/csrf)
