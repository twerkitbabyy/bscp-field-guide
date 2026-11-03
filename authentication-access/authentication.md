# Authentication

Authentication flaws let an attacker log in as another user or bypass identity
checks, through weaknesses in login, multi-factor, "remember me", and password
reset flows.

## How to identify it

Exercise every authentication-related flow and watch for tells: subtly
different responses or timing between valid and invalid usernames, status-code
differences on a correct password, user-controllable fields (such as a
`username`) in reset or change-password requests, and any cookie that looks
decodable rather than opaque.

## Techniques and variants

- **Username enumeration.** Differences in error text, response length,
  response timing, or account-lock behaviour can reveal which usernames exist.
  Grep-extract the error text when differences are subtle.
- **Flawed brute-force protection.** IP-based blocks can sometimes be reset by
  interleaving valid logins, or bypassed by spoofing the client IP header where
  the application trusts it.
- **Password reset logic.** If the reset request carries a `username` or similar
  field, it may let you reset another account. Reset links built from a host
  header you control can be poisoned to capture the token.
- **Multi-factor bypass.** The second factor can be a UI-only step if a session
  is granted before it is verified; verification tied to a user-controllable
  cookie can be retargeted and the code brute-forced.
- **Predictable "remember me" cookies.** If the token is a reversible encoding
  of username plus an unsalted hash, equivalent cookies can be generated
  offline, or stolen and cracked.

## Prevention

- Use a single, generic error message for all failed logins, and keep timing
  constant regardless of whether the username exists.
- Enforce robust, server-side rate limiting and lockout that cannot be reset by
  the client, with CAPTCHA on repeated failures.
- Make session tokens and reset tokens long, random, single-use, and
  time-limited; tie reset actions to the authenticated token, not a supplied
  username.
- Verify the second factor server-side before granting any authenticated
  session, and rate-limit code attempts.
- Build the host used in emails from a trusted server-side value, not an
  incoming header.

> Reference: PortSwigger Web Security Academy, [Authentication](https://portswigger.net/web-security/authentication)
