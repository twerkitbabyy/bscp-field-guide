# Cross-Site Scripting (XSS)

XSS lets an attacker run script in another user's browser in the context of the
vulnerable site, enabling session hijacking, credential capture, and actions on
the victim's behalf.

## How to identify it

Trace where your input is reflected or stored and in what context it lands:
HTML body, an attribute, a JavaScript string, a URL. Insert a harmless marker
and inspect how it is encoded in the response or written into the DOM. For
DOM-based issues, read the client-side JavaScript and follow data from a source
(such as `location`) to a dangerous sink (such as `innerHTML`).

## Types and variants

- **Reflected.** Input echoed straight back in the response; the payload rides
  in the request.
- **Stored.** Input saved and later served to other users (comments, profiles),
  which is higher impact.
- **DOM-based.** A client-side script passes a source into a sink without
  sanitisation; the issue is entirely in the browser.
- **Context and filter bypass.** The working payload depends on context, you
  may need to break out of an attribute or a JS string, or work within an
  allowed subset (for example limited tags/attributes or some SVG markup). When
  characters are escaped, the escape scheme determines the approach: if quotes
  are handled but the surrounding block isn't, break the block; if everything is
  encoded, look for a context where encoding doesn't apply.

**Impact.** Demonstrating impact typically means showing that script executes
in the victim's context, for example performing a request as them or
exfiltrating a non-`HttpOnly` cookie or submitted credentials to a server you
control. Keep proof-of-concept actions benign.

## Prevention

- Context-aware output encoding: encode data for the exact context it's placed
  in (HTML, attribute, JS, URL) when writing it to a page.
- Prefer safe sink APIs (e.g. `textContent` over `innerHTML`); avoid building
  markup from untrusted data in client-side code.
- Deploy a strong Content-Security-Policy as defence in depth.
- Set `HttpOnly` on session cookies so script can't read them, and validate
  input on the server as well.

> Reference: PortSwigger Web Security Academy, [Cross-site scripting](https://portswigger.net/web-security/cross-site-scripting)
