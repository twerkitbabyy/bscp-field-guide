# Web Cache Poisoning

Cache poisoning stores a harmful response in a shared cache so it is served to
other users. It works when input that affects the response is not part of the
cache key ("unkeyed"), so a poisoned response is cached under a normal key.

## How to identify it

Find inputs the response reflects but the cache ignores: unkeyed headers,
cookies, or query parameters. A parameter-discovery tool (such as Param Miner)
helps find them. Use a cache buster while probing, read caching headers
(`X-Cache`, `Age`) to tell hits from misses, and only remove the buster once
you're ready to poison.

## Techniques and variants

| Unkeyed input | Mechanism |
| --- | --- |
| Header (e.g. `X-Forwarded-Host`) | Reflected into an absolute resource URL, redirecting an imported script. |
| Cookie (e.g. a front-end host) | Reflected inline into JavaScript, allowing a breakout. |
| Multiple headers combined | Headers that do nothing alone combine to force a redirect you control. |
| Unknown header with `Vary` | Target the victim's exact `User-Agent` when it's part of the key. |
| Unkeyed query string / parameter | Reflected into a tag (e.g. canonical link); tracking params like `utm_content` are often excluded. |
| Parser discrepancies | Parameter cloaking (`;` handling) and fat GET (body ignored by cache) to smuggle a value past the key. |
| URL normalisation | The cache normalises an encoded path so a Repeater-sent unencoded payload matches the victim's encoded request. |

## Prevention

- Treat the cache key as a security boundary: ensure every input that changes
  the response is included in the key, or don't let such inputs affect cached
  responses.
- Don't reflect unkeyed headers, cookies, or parameters into cached content.
- Avoid caching responses that are personalised or depend on request headers;
  mark them appropriately so they aren't shared.
- Configure the cache and application to parse requests consistently to remove
  cloaking and fat-GET discrepancies.

> Reference: PortSwigger Web Security Academy, [Web cache poisoning](https://portswigger.net/web-security/web-cache-poisoning)
