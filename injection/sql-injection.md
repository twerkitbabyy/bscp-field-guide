# SQL Injection

SQL injection occurs when user input is incorporated into a database query
without proper separation of code and data, letting an attacker alter the
query's logic.

## How to identify it

Submit a single quote in any parameter that might reach a query (URL filters,
search fields, cookies such as a tracking ID, login fields) and watch for a
changed response: an error, a different result set, or a change in behaviour.
From there, determine whether results are visible (in-band), inferable from
true/false behaviour or errors (blind), or only observable via timing or an
external interaction (out-of-band).

## Techniques and variants

| Situation | Approach |
| --- | --- |
| Results shown, logic alterable | Boolean condition to return hidden rows, or comment out the rest of the query. |
| Login form | Inject into the username and comment out the password check. |
| Results shown, want other tables | UNION-based retrieval: first determine column count and which columns hold text, then select the target columns. |
| Need DB type/version | Query the version; Oracle requires `FROM dual` and uses `all_tables`/`all_tab_columns` instead of `information_schema`. |
| One text column only | Concatenate multiple values into it (syntax differs per engine). |
| No visible output, behaviour changes | Blind boolean: extract data character by character via conditional responses. |
| No visible output, no behaviour change, errors possible | Trigger a conditional error (e.g. a cast or divide-by-zero) to leak data in the message. |
| No observable difference at all | Time-based: use a conditional sleep to infer each character. |
| Input filtered by a WAF | Encode keywords (e.g. XML character-entity encoding) to slip past signature filters. |

For blind extraction, automate with a tool such as sqlmap rather than doing it
by hand, give it the captured request, the injectable parameter, and the
matching condition or DBMS.

## Prevention

- Use parameterised queries (prepared statements) for any query that includes
  user input. This is the single most important control.
- Treat every input as untrusted, including headers and cookies, not just form
  fields.
- Apply least privilege to the database account the application uses, so a
  successful injection yields as little as possible.
- Keep detailed errors out of responses so they cannot be used as an oracle.

> Reference: PortSwigger Web Security Academy, [SQL injection](https://portswigger.net/web-security/sql-injection)
