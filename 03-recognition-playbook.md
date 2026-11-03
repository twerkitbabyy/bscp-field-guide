# 03. Recognition playbook

The exam isn't really a knowledge test. By the time you sit it, you already know how to exploit every one of these bugs from the labs. The skill it actually rewards, and the one the mystery labs train, is recognising *which* vulnerability you're looking at, fast, when nobody tells you. This is the method I built grinding around 100 mystery labs.

## The loop

It's always the same four steps. Don't improvise the order.

1. **Map (2 to 3 minutes).** Walk the app as a normal user before touching anything. Register, log in, note the roles. Find every place your input reaches the server: search boxes, forms, profile fields, uploads, cookies, headers. Pull the endpoints from the site map. Knowing the shape first cuts the list of plausible bugs before you test a thing.
2. **Targeted scan.** Run an automated scan on each entry point that takes your input, not the whole site. If something flags, jump to it and verify by hand. Measuring beats guessing and saves you most of the blind poking.
3. **Test by category.** If the scan is quiet, test manually, cheapest signal first (table below).
4. **Confirm, then exploit.** Cheap probe before expensive exploit. A single quote, a template expression, a timed command. Only once it confirms do you open the reference and build the full thing.

## What you're trying to do narrows what to look at

Web attacks tend to fall into three goals. Knowing which one you're on cuts the candidate list fast.

| Goal | Prime suspects |
| --- | --- |
| Get access as a user | reflected / stored / DOM XSS, auth and password-reset logic, cache poisoning, host header, request smuggling |
| Escalate to admin | access control / IDOR, SQLi, JWT, OAuth, CSRF, deserialization |
| Reach server-side data or files | path traversal, SSRF, OS command injection, SSTI, XXE |

## Signal to suspect

This is the part that matters. Train yourself to read the signal, not to memorise payloads.

| You see or notice | Suspect | Cheap confirm |
| --- | --- | --- |
| Your input reflected unencoded in the page | XSS (reflected / stored) | harmless marker, check how it comes back |
| Client-side JS passing a source into a sink | DOM XSS | trace `location` / `postMessage` to `innerHTML` and friends |
| Input reflected into a template-looking context | SSTI | `${7*7}` or `{{7*7}}`, look for 49 |
| A parameter carrying a filename or path | path traversal | request a known file through the param |
| A parameter carrying a URL or hostname | SSRF | point it at loopback or an internal host |
| A field that plausibly shells out | OS command injection | a separator plus a timed command |
| An XML body, or an SVG / DOCX upload | XXE | external entity to a local file, or OOB |
| A serialized blob in a cookie or param (`O:`, `a:`, `rO0AB`) | deserialization | corrupt it, read the error for the framework |
| A session value in three dot-separated parts | JWT | decode the header, check `alg` |
| An ID or role in a request you can change | access control / IDOR | swap the ID or role, re-request |
| A state-changing request with a weak or missing token | CSRF | drop or swap the token, replay |
| A quote changing the response or causing an error | SQLi | boolean, error, or timing probe |
| Caching headers plus your input in a cached response | cache poisoning | find the unkeyed input, cache-bust while testing |

Once you've recognised it, the actual payloads live in the per-technique references. Recognition first, mechanics second.

## One mindset note

The stages are more connected than they look. A function or a token you picked up earlier can be exactly what unlocks a later one, even when it seems to belong somewhere else. Landing a session as your target user isn't the finish line, it's the start of the next question: now what do you do with it? Keep a running map of every function and every credential you come across, and don't write anything off just because it feels like it belongs to a different part of the app.

## Keep a log

The single best habit, same as a machine log for the OSCP: after each mystery lab, write down the vulnerability, the signal that gave it away, how long you took to *recognise* it (not to exploit it), and whether you were slow. A week of that teaches you your own blind spots better than any guide.
