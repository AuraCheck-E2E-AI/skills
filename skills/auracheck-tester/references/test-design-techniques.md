# Test design techniques

The catalogue `design_test_suite` applies to every discovered flow. Each
technique below: what it is, when it matters, a worked example against a
generic "sign up" flow so the pattern transfers to any form/flow you test.

## Positive (happy path)

The flow works end to end with valid, typical input — the case every suite
already has. Keep it realistic: typical values a real user would enter, not
edge-of-spec ones.

> Sign up with a valid email, a password meeting the stated rules, and a
> real-looking name → account created, redirected to the dashboard or a
> verification notice.

## Negative

Invalid input, wrong credentials, and states the system should reject.
Enumerate the ways to be *wrong*, not just one:

- Invalid format (malformed email, letters in a phone field).
- Missing required field.
- Wrong credentials (right username, wrong password; right password, wrong
  username).
- Expired/locked/disabled account or session.
- Duplicate submission (signing up with an email that already exists).

> Sign up with an email missing an `@` → inline validation error, no account
> created, no redirect.
> Log in with a correct email and a one-character-off password → generic
> "invalid credentials," never "wrong password" (don't leak which field was
> wrong).

## Boundary values

Test at the edges of what's accepted, not just inside the range. For every
input with a stated or implied limit: minimum, minimum−1 (should fail),
maximum, maximum+1 (should fail), and zero/empty where relevant.

- String length: 1 char, the stated max, max+1.
- Numeric: 0, 1, the max allowed, max+1, negative.
- Dates: today, the earliest/latest allowed, one day outside that range.
- Currency: Rp 0, Rp 1, the largest allowed amount, a fractional Rupiah
  amount (Rupiah has no subunits in practice — does the UI even allow one?).
- File upload: the stated max size, one byte over, zero-byte file.

> Password field stated as "8–64 characters": test 7 (reject), 8 (accept),
> 64 (accept), 65 (reject).

## Equivalence classes

When exhaustively testing every possible value is wasteless, pick ONE
representative value per class of input instead of testing every value in
that class — classes behave identically, so one sample per class gets the
same coverage as testing all of them.

> Phone number field: classes are {valid +62 mobile}, {valid 08 mobile},
> {valid landline}, {too short}, {letters instead of digits}, {valid foreign
> number if supported}. Test one value from each class, not fifty phone
> numbers that are all "valid +62 mobile."

## State transitions

What happens when the user doesn't follow the straight path:

- **Back button** mid-flow (does a multi-step form lose data? does checkout
  let you "go back" into an already-paid order?).
- **Refresh** mid-flow (is state preserved, or does the user silently lose
  progress with no warning?).
- **Double-submit** (double-click "Pay," or submit twice via two tabs — does
  it charge/create twice?).
- **Session timeout** mid-flow (does the system fail gracefully with a
  re-login prompt, or silently drop the action?).
- **Navigate away and back** (via a link, not back button — same data-loss
  question).

> Fill a checkout form, click Pay, then immediately click Pay again before
> the page responds → exactly one order/charge, not two.

## Error handling

Not "does it crash" — what does the USER actually see:

- A network error (request times out, server unreachable) — a clear
  message, not a frozen spinner forever.
- A 500/server error — a message that doesn't leak a stack trace, and
  doesn't silently look like success.
- A validation error from the server (not caught client-side) — surfaced
  clearly, not swallowed.
- A third-party failure (payment gateway down, SMS/OTP provider down) — a
  clear "try again" path, not a dead end.

> Submit a form while the API is simulated as returning 500 → the user sees
> an error message and can retry, the form doesn't silently "succeed" with
> no data saved.

## Permission / role

Run the SAME flow as every relevant role via AuraCheck's Test Accounts, and
check each one sees/does only what it should — not just that each role "can
log in."

- A guest attempting an action that requires login → redirected/blocked, not
  partial access.
- A regular member attempting an admin-only action (by URL, not just
  hidden UI) → blocked server-side, not just hidden in the nav.
- A locked/suspended account → a clear message, not a generic error.
- Cross-tenant/cross-account access (if the user asks and it applies): does
  user A's URL/ID let them see user B's data?

> As a non-admin member, navigate directly to `/admin/users` → redirected or
> a clear "not authorized," never the admin page rendering with real data.

## Security (opt-in, non-destructive, confirmed-owner only)

Only when the user explicitly asked for security checks AND confirmed they
own the target. Every probe here is designed to be **safe to run on a real
production site** — it never deletes data, never completes a real purchase,
never sends a real message to a third party.

- **XSS probe**: type `<script>alert(1)</script>` or `"><img src=x
  onerror=alert(1)>` into a text input that gets displayed back (profile
  name, comment, search query) → the string renders as literal text, never
  executes. Never actually execute injected script against a page other
  users will see (don't do this in a public-facing comment that persists).
- **Basic SQL-injection probe**: a value like `' OR '1'='1` or `'; DROP
  TABLE users; --` in a login/search field → a normal validation error or
  empty result, never a 500 that looks like a raw DB error, never a
  logged-in session from a probe string.
- **Open redirect**: a `?redirect=`/`?next=`/`?returnUrl=` parameter pointed
  at an external domain → the app refuses or warns, doesn't silently
  redirect there.
- Never: brute-force/credential-stuffing attempts, load/DoS testing, real
  destructive writes, probing a target the user hasn't confirmed they own.

## Basic accessibility

Lightweight, not a full WCAG audit:

- Every input has a label (visible text or `aria-label`) a screen reader
  would announce.
- The primary flow (e.g. sign-up, checkout) is completable with keyboard
  only — Tab/Enter, no mouse.
- Focus is visible on every interactive element (not `outline: none` with no
  replacement).
- Images that carry meaning have alt text; decorative ones don't need it.

> Tab through the sign-up form start to finish, submit with Enter, never
> touching the mouse → the account is created, exactly like the mouse path.

## Putting it together: coverage rule of thumb

A flow's suite is incomplete if:
- It has zero negative cases (only happy paths were tested).
- It takes any input and has zero boundary cases.
- It's reachable by more than one role and only one role was tested.
- It involves money/state changes and zero state-transition cases (no
  double-submit/back-button check).

`design_test_suite` enforces exactly this as its coverage-rule validator —
if a generated suite is missing one of these, it gets a repair pass before
it's returned.
