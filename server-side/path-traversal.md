# Path Traversal

Path traversal (directory traversal) lets an attacker read files outside the
intended directory when user input is used to build a file path without proper
validation.

## How to identify it

Look for a parameter that loads a file (product images, document downloads,
avatars). The parameter value looks like a filename or a path
(`?filename=photo.jpg`). If the application serves that file back, it is a
candidate. Probe by requesting a known system file through the parameter and
watch whether the contents come back.

## Techniques and variants

The right technique depends on what filtering the application applies. Work
through them from simplest to most involved:

| Situation | Approach |
| --- | --- |
| No filtering | Plain traversal sequence to reach a file above the base directory. |
| Traversal sequences blocked | Try an absolute path instead, if the parameter is treated as relative to a working directory. |
| Sequences stripped once (non-recursively) | Nest the sequence so that stripping the inner part leaves a valid one behind. |
| Sequences blocked after a URL-decode step | Double-encode the slash so the filter misses it but a later decode restores it. |
| Path must start with an expected directory | Keep the expected prefix, then traverse from there. |
| A specific file extension is required | Historically a null byte could terminate the path before the required extension; patched in modern PHP but may apply to other stacks. |

Reading the response tells you whether a bypass worked, the application either
returns the target file's contents or an error that reveals how the input was
processed.

## Prevention

- Avoid passing user input to filesystem APIs at all. Prefer an index or
  identifier that maps to a file server-side, so the user never supplies a path.
- If a filename must be accepted, validate it against an allowlist of permitted
  values rather than trying to blacklist bad sequences.
- After building the path, resolve it to its canonical absolute form and verify
  it still sits inside the intended base directory before opening it.
- Run the application with least privilege so sensitive files are not readable
  even if traversal occurs.

> Reference: PortSwigger Web Security Academy, [File path traversal](https://portswigger.net/web-security/file-path-traversal)
