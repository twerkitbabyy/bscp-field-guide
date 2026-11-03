# Server-Side Template Injection (SSTI)

SSTI occurs when user input is embedded into a server-side template in an unsafe
way, so the template engine evaluates it as code rather than treating it as
data. Impact ranges from information disclosure to remote code execution.

## How to identify it

Probe inputs that are rendered back into a page with a mathematical template
expression in the common syntaxes (for example `{{7*7}}`, `${7*7}`,
`<%= 7*7 %>`). If the response shows the evaluated result, the server is
executing it, distinguishing SSTI from a purely client-side reflection. A
polyglot fuzz string that mixes template metacharacters will often trigger an
error that names the engine.

## Techniques and variants

- **Identify the engine first.** The exploitation path is entirely
  engine-specific, so use the error message or the syntax that evaluates to pin
  down whether it is ERB, Jinja/Tornado, Freemarker, Handlebars, Django, etc.
- **Plain-text context vs code context.** If your input lands directly in the
  template, inject straight away. If it is already inside an expression, close
  that expression before adding your own.
- **Escalation.** Consult the documented capabilities of the identified engine:
  some expose object navigation that leads to code execution, others are more
  constrained and may only permit reading configuration or secrets (for example
  a framework secret key via a debug directive).

## Prevention

- Don't let users supply or edit templates. Keep user input as data passed to a
  template, never as part of the template itself.
- If users must have some templating ability, use a logic-less or sandboxed
  engine, and keep in mind sandboxes are regularly escaped.
- Run template rendering in a locked-down, low-privilege environment as
  defence in depth.

> Reference: PortSwigger Web Security Academy, [Server-side template injection](https://portswigger.net/web-security/server-side-template-injection)
