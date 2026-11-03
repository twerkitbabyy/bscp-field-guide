# Business Logic Vulnerabilities

Logic flaws are mistakes in the application's design and assumptions. The
individual requests are "valid", but the sequence or values let a user do
something they shouldn't. They can't be found by signatures; they require
understanding the intended workflow.

## How to identify it

Map what the application assumes the user will do, then deliberately break those
assumptions: supply values outside the expected range, skip or reorder steps,
remove parameters, and feed exceptional input. Watch for anything that trusts
the client to behave.

## Techniques and variants

- **Excessive trust in client-side values.** A price, discount, or quantity sent
  by the client and not re-validated server-side can be altered.
- **Unbounded numeric input.** Negative or zero quantities/amounts can
  manipulate totals.
- **Inconsistent controls.** A privileged attribute (such as an email domain)
  that can be changed after registration without re-verification can grant
  access.
- **Flawed rule enforcement.** Single-use constraints that only check the last
  action (e.g. a coupon) can be defeated by alternating inputs.
- **Exceptional input handling.** Truncation or overflow of a long value can
  produce a privileged result the designer didn't expect.
- **Weak isolation / optional parameters.** Removing a field (like the current
  password) or changing a `username` the request shouldn't need can reach other
  accounts.
- **Insufficient workflow validation.** Jumping straight to a final step (order
  confirmation) can skip required ones (payment).
- **Flawed state machine.** Dropping an intermediate step after login can land
  on a privileged default.

## Prevention

- Validate all critical values and state transitions server-side; never trust
  client-supplied data for prices, quantities, roles, or identity.
- Model the workflow explicitly and enforce that steps happen in order and only
  once where required.
- Handle exceptional and boundary input deliberately, define limits and reject
  what falls outside them rather than truncating silently.
- Review business rules with an adversarial mindset and test them, since they're
  invisible to automated scanners.

> Reference: PortSwigger Web Security Academy, [Business logic vulnerabilities](https://portswigger.net/web-security/logic-flaws)
