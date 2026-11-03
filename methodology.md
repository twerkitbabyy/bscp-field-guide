# Black-box web application triage

A repeatable approach for working through an unfamiliar web application. The
point is to **recognise what to test quickly** rather than poke at everything
at random. The order is always the same: **map → scan → test by category →
confirm before you commit**.

## 1. Map the application (2–3 min)

Walk through the app as a normal user and note:

- Can you register and log in? Do it, and note the roles that exist.
- Where does your input reach the server? Search fields, forms, profile data,
  uploads, cookies, headers.
- What endpoints are visible? Browse the app and review the site map in your
  proxy.

Mapping first narrows the set of plausible vulnerabilities before you spend any
time testing.

## 2. Targeted scanning first

Run an automated scan against **each endpoint that takes your input**, not the
whole site (slower and noisier). Review the findings, if something flags,
jump to the relevant note and verify it by hand. If nothing is conclusive,
move to manual testing.

Measuring before guessing saves most of the blind trial-and-error.

## 3. Manual testing by category

Cheap confirmation before expensive exploitation: send a minimal probe first
(a single quote, a template expression, a timing test). Only once it confirms
do you build the full technique.

Group your checks by where the input lands and what it can affect:

- **Input reflected in the page** → XSS (reflected, stored, DOM), client-side
  template handling.
- **Authentication and session logic** → login flows, password reset, 2FA,
  "remember me" cookies, token handling (JWT, OAuth).
- **Authorisation** → object references and IDs, role parameters, direct access
  to privileged URLs or methods.
- **Server-side data handling** → SQL/NoSQL queries, serialized objects, XML
  parsing, template rendering.
- **Server-side requests and files** → parameters carrying URLs or hostnames
  (SSRF), parameters loading files (path traversal), uploads.
- **Request/response handling** → ambiguous framing (request smuggling), host
  header trust, caching behaviour.

## 4. Confirm, then exploit

Once a vulnerability is confirmed, open the relevant note, review the variants,
and apply the matching technique. Keeping a short log of what each signal
turned out to be, and which ones you were slow to spot, is the fastest way to
sharpen recognition over time.
