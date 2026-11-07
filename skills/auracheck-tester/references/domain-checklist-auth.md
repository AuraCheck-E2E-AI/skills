# Domain checklist: authentication

Signup, login, password reset, OTP, session — the flow nearly every suite
touches first, and the one most often tested only as a happy path.

## Signup

- Valid signup → account created, confirmation/verification state is clear.
- Duplicate email/phone → clear "already registered" error, no duplicate
  account, no silent overwrite.
- Password below the stated minimum/complexity rule → rejected inline,
  rule text visible (not a generic "invalid password").
- Required field missing (name, email, phone, ToS checkbox) → rejected,
  specific field flagged.
- Email/phone format edge cases: `user+tag@domain.com` (valid, often
  mishandled), `user@sub.domain.co.id` (valid), missing TLD (invalid).
- Indonesian phone formats: `+6281234567890` and `081234567890` should both
  be accepted if the form claims to support Indonesian numbers — see
  [[indonesian-market-edge-cases]].
- Very long name/email at the stated max length → accepted; one over →
  rejected with a clear message, not a silent truncation.

## Login

- Valid credentials → signed in, lands on the expected page.
- Wrong password, right email → generic "invalid credentials" (never reveal
  which field was wrong — that's a user-enumeration leak).
- Non-existent email → the SAME generic message as wrong password (if it
  differs, that's a real finding: user enumeration).
- Locked/suspended/disabled account → a clear, specific message (not the
  generic invalid-credentials one — the user needs to know it's not a typo).
- Case sensitivity: does the email field accept `User@Example.com` the same
  as `user@example.com`? (Usually should.) Does the password field, if
  tested, correctly reject a case-different password? (Usually should.)
- Rate limiting / lockout after N failed attempts, if the product has it —
  confirm the lockout message, not just that attempt N+1 fails.
- Double-submit: click "Log in" twice fast → one session, not an error or
  a race.

## Password reset

- Request reset for a valid email → confirmation message, doesn't reveal
  whether the email exists if the product deliberately doesn't (compare
  against request for a non-existent email: identical response?).
- Reset link/OTP used twice → second use is rejected.
- Reset link/OTP after expiry → rejected with a clear "expired, request a
  new one" message, not a generic error.
- New password same as the old one → check the product's own rule (some
  explicitly reject this, some don't — don't assume; check behaviour and
  report accordingly without calling a real decision a "bug").
- New password violating the complexity rule → rejected inline, same as
  signup.

## OTP

- Correct OTP within the window → success.
- Wrong OTP → clear error, remaining-attempts messaging if the product has
  it.
- Expired OTP → clear "expired" message.
- Resend OTP → old OTP invalidated (test by trying the old one after
  resending — it should now fail) and a cooldown before another resend is
  allowed.
- OTP delivery channel (SMS to an Indonesian number, WhatsApp, email) —
  AuraCheck's test accounts handle this via stored OTP inboxes; never ask a
  person to read a real OTP into chat.

## Session

- Session persists across a page refresh.
- Session times out after the stated inactivity window — test the message
  the user gets when they act after timeout (clear re-login prompt, not a
  silent failure or a confusing error on an unrelated action).
- Logout actually invalidates the session — hitting back/forward after
  logout doesn't show cached authenticated content, and reusing the old
  session token server-side (if testable) is rejected.
- Logging in from a second device/tab: does the product allow concurrent
  sessions, or invalidate the first? Test and report the actual behaviour —
  don't assume which is "correct" for this product.

## Roles after login

- Each role (guest, member, admin, locked) sees only what it should — see
  the Permission/role technique in [[test-design-techniques]].
- Direct URL access to a role-gated page while logged in as a lower role →
  blocked server-side (not just a hidden nav item that's still reachable by
  URL).
