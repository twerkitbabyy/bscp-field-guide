# File Upload Vulnerabilities

File upload flaws arise when an application accepts files without adequately
validating their type, content, or how they are stored and served, in the worst
case allowing server-side code execution.

## How to identify it

Upload a benign file and find where it is served from (for example by inspecting
the resulting link in your profile). That tells you the upload path and whether
files there are executed or served as static content. Then test what validation
is enforced and how it can be subverted.

## Techniques and variants

| Validation | Bypass concept |
| --- | --- |
| Checks only the declared content type | Set a permitted MIME type on the upload part while keeping an executable extension. |
| Upload directory doesn't execute scripts | Store the file elsewhere via path traversal in the filename. |
| Extension blocklist | Map a custom extension to the interpreter via a config file (e.g. `.htaccess`) where permitted. |
| Extension allowlist | Obfuscate the extension (historic null-byte truncation, mixed case, double extensions, trailing characters) depending on the stack. |
| Validates real file content | Use a polyglot: a genuinely valid image carrying payload in metadata. |

Reading the server's response (how it reports the stored filename) tells you
whether a given bypass actually took effect.

## Prevention

- Validate the file by content, not just extension or declared type, and
  generate a new random filename server-side; don't use the user-supplied name.
- Store uploads outside the web root (or in storage that never executes), and
  serve them through a handler that sets a safe content type and
  `Content-Disposition`.
- Don't allow server config files (like `.htaccess`) to be uploaded, and
  disable script execution in the upload directory.
- Enforce size limits and scan where appropriate.

> Reference: PortSwigger Web Security Academy, [File upload vulnerabilities](https://portswigger.net/web-security/file-upload)
