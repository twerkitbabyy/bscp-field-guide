# Insecure Deserialization

Insecure deserialization occurs when an application deserializes
attacker-controllable data without validation, allowing object state to be
tampered with and, with a suitable gadget chain, remote code execution.

## How to identify it

Inspect cookies and parameters for serialized blobs. Common fingerprints:
Base64 that decodes to a PHP object (`O:` or `a:` prefixes) or to Java serialized
data (begins with the magic bytes, Base64 `rO0AB`). Deliberately corrupting the
blob often yields an error that names the framework and version. Burp's Freddy
extension flags these passively.

## Techniques and variants

- **Attribute tampering.** Flip a boolean or change a value in a serialized
  object (for example an admin flag) and re-encode.
- **Type juggling.** In languages with loose comparison, changing a value's type
  can bypass weak equality checks on a token.
- **Object injection via magic methods.** When source code is available (for
  example via exposed backup files), craft an object of a class whose lifecycle
  method performs a dangerous action.
- **Gadget chains.** Use an established chain generator for the platform and
  library in use (ysoserial for Java, phpggc for PHP). The error naming the
  library tells you which chain family applies.
- **Signed blobs.** If the serialized data is signed, a leaked secret key (for
  example from an exposed debug page) lets you forge a valid signature.

## Prevention

- Do not deserialize data from untrusted sources. This is the only robust
  defence.
- If unavoidable, use a data-only format (such as JSON) with explicit schemas
  rather than native object serialization.
- Enforce integrity with a strong signature or HMAC over the data, with the key
  kept secret, and reject anything that fails verification before deserializing.
- Keep libraries patched and, where supported, restrict which classes may be
  deserialized.

> Reference: PortSwigger Web Security Academy, [Insecure deserialization](https://portswigger.net/web-security/deserialization)
