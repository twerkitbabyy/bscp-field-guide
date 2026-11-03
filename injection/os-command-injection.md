# OS Command Injection

OS command injection happens when an application passes user-controlled data
into a system shell command without proper sanitisation, allowing arbitrary
commands to run on the server.

## How to identify it

Look for features that plausibly shell out: stock checkers, ping/DNS tools,
file conversions, feedback forms. Introduce a shell metacharacter and a benign,
observable command. If output appears in the response it is in-band; if not,
confirm blindly with a time delay or an out-of-band interaction.

## Techniques and variants

- **Separators**: chaining characters (`;`, `&`, `|`, newline, and inline
  execution with backticks or `$()`) let you append a command. Which ones work
  depends on the OS and shell. If the input sits inside quotes in the original
  command, close the quote first.
- **In-band**: when the command output is reflected, read it directly from the
  response.
- **Blind, time-based**: trigger a measurable delay to confirm execution when
  there is no output.
- **Blind, output redirection**: write command output to a file within a
  web-accessible directory, then retrieve it.
- **Blind, out-of-band**: have the server make a DNS/HTTP request to a
  collaborator host to confirm execution and, where possible, exfiltrate data
  through the subdomain.

## Prevention

- Avoid calling out to a shell entirely. Use language-native APIs for the task
  (for example, built-in file or network functions) instead of building a
  command string.
- If a system command is unavoidable, use an API that passes arguments as an
  array without a shell, and validate inputs against a strict allowlist.
- Never attempt to sanitise by escaping shell metacharacters, it is
  error-prone and routinely bypassed.
- Run the application with least privilege to limit the impact of any lapse.

> Reference: PortSwigger Web Security Academy, [OS command injection](https://portswigger.net/web-security/os-command-injection)
