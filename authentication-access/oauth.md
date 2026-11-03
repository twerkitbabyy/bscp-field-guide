# OAuth

OAuth misconfigurations in the client application or authorisation server can
lead to account takeover, usually through weak validation of redirect targets,
missing CSRF protection on linking, or trusting client-supplied identity data.

## How to identify it

Map the flow: note whether it is authorization code or implicit, and capture
the key requests: the initial request to the authorisation server, the
callback, and any request where the client application posts identity data
(email, username) alongside a token. Each of these is a candidate for tampering.

## Techniques and variants

- **Unverified identity data (implicit).** If the client posts user-identifying
  fields with the token and doesn't bind them to it, changing those fields can
  log you in as another user.
- **Forced profile linking.** An account-linking endpoint without a `state`
  parameter is CSRF-prone: a victim can be made to link your social profile to
  their account.
- **`redirect_uri` takeover.** If the authorisation server doesn't strictly
  validate `redirect_uri`, pointing it at a server you control can leak the
  authorization code or token.
- **Chained open redirect.** Where `redirect_uri` is domain-restricted but the
  app has an open redirect, the two can be chained to exfiltrate the code or
  token (in implicit flow the token is in the URL fragment, so capture needs
  script).

## Prevention

- Strictly validate `redirect_uri` against an exact allowlist on the
  authorisation server.
- Always use and verify the `state` parameter to protect the flow (and linking)
  from CSRF.
- Bind the identity established by the token to the session server-side; never
  trust client-supplied identity fields.
- Prefer the authorization code flow (with PKCE) over implicit, and keep tokens
  out of URLs where possible.

> Reference: PortSwigger Web Security Academy, [OAuth authentication](https://portswigger.net/web-security/oauth)
