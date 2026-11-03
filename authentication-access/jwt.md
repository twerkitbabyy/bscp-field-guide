# JSON Web Tokens (JWT)

JWT attacks exploit weak or absent signature verification, letting an attacker
tamper with token claims (such as the subject) to impersonate another user.

## How to identify it

A session value with three dot-separated, Base64URL-decodable parts is a JWT.
Decode the header to read the algorithm and the payload to see the claims. The
algorithm (`alg`) largely determines which attacks are worth trying.

## Techniques and variants

| Finding | Approach |
| --- | --- |
| Signature not checked | Change a claim (e.g. the subject) and send; the signature is ignored. |
| `alg: none` accepted | Set the algorithm to none and strip the signature, keeping the trailing dot. |
| HS256 with a weak key | Crack the HMAC key offline against a wordlist, then sign a forged token. |
| RS256, key taken from token | Inject your own public key via the `jwk` header and sign with your private key. |
| RS256, key fetched from a URL | Host your public key and point the `jku` header at it (matching the `kid`). |
| `kid` builds a file path | Path-traverse `kid` to a predictable/empty file and sign with the matching key. |

A JWT editor extension automates key generation, header injection, and signing.

## Prevention

- Use a vetted JWT library and verify the signature on every request before
  trusting any claim.
- Pin the expected algorithm server-side; reject `none` and reject tokens whose
  `alg` doesn't match what you expect (don't let the token choose).
- Never load verification keys from token-controlled sources (`jwk`, `jku`,
  `kid` as a path). Use a fixed, server-side key set.
- Use strong secrets for HMAC, set short expiries, and validate issuer and
  audience claims.

> Reference: PortSwigger Web Security Academy, [JWT attacks](https://portswigger.net/web-security/jwt)
