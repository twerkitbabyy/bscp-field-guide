# HTTP Request Smuggling

Request smuggling exploits disagreement between a front-end and back-end server
about where one request ends and the next begins, letting part of an attacker's
request be prepended to another user's request.

## How to identify it

Smuggling arises when two servers in a chain parse message length differently,
typically from conflicting `Content-Length` and `Transfer-Encoding` headers, or
from HTTP/2 downgrade handling. It is usually confirmed by sending a crafted
request and observing a delayed or anomalous response that indicates the
back-end treated trailing bytes as the start of the next request.

## Techniques and variants

- **CL.TE / TE.CL.** The two servers disagree over which of `Content-Length` and
  `Transfer-Encoding` to honour; the ignored header leaves a prefix that merges
  into the following request.
- **TE obfuscation.** Slightly malformed `Transfer-Encoding` headers can be
  honoured by one server and ignored by the other.
- **HTTP/2 related.** CRLF injection in HTTP/2, request splitting, and H2.CL /
  H2.TE issues appear where HTTP/2 is downgraded to HTTP/1 without strict
  revalidation. CL.0 covers cases where the back-end ignores the body entirely.

Typical impact, once a desync exists, includes bypassing front-end access
controls to reach restricted paths, capturing parts of other users' requests,
and delivering reflected payloads to other users. Testing this is destructive to
other connections, so it belongs only in authorised environments.

## Prevention

- Use HTTP/2 end-to-end and don't downgrade to HTTP/1 at the back-end; if you
  must, revalidate message framing strictly on downgrade.
- Make the front-end normalise ambiguous requests and reject any with both
  `Content-Length` and `Transfer-Encoding`, or malformed framing headers.
- Ensure front-end and back-end use the same, strict parsing, and use the same
  server software where possible.
- Disable connection reuse between front-end and back-end if the risk can't
  otherwise be eliminated.

> Reference: PortSwigger Web Security Academy, [HTTP request smuggling](https://portswigger.net/web-security/request-smuggling)
