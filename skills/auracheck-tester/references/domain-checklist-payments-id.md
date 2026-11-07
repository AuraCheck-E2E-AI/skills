# Domain checklist: Indonesian payment methods

QRIS, virtual accounts, and e-wallets each fail in their own specific ways
— generic "test the payment flow" misses most of these.

## QRIS

- The QR code renders and is scannable (visually present, not a broken
  image) — AuraCheck can confirm the code element appears and contains
  data; it cannot itself scan a real QR with a banking app, so a full
  payment confirmation needs either a sandbox/test QRIS flow the merchant
  provides, or a human step. Say this plainly rather than claiming an
  automated scan happened.
- The displayed amount on the QR page matches the cart/order total exactly
  (a very common real bug: a stale total shown after a late discount/promo
  application).
- QRIS expiry countdown (most QRIS flows expire in ~15 minutes): expired
  code → clear "expired, generate a new one" state, not a silently stuck
  "waiting for payment" screen.
- Checking payment status before the real payment lands (if the test
  environment allows a provider webhook/sandbox trigger) → status updates
  correctly rather than staying stuck.

## Virtual Account (VA)

- VA number displayed matches the expected bank/channel format for that
  provider (BCA/Mandiri/BNI/BRI/Permata VA numbers have provider-specific
  length/prefix conventions) — flag a VA number that looks malformed.
- The amount tied to the VA matches the order total exactly, and — if the
  provider requires an exact-amount transfer — the flow actually states
  that requirement (a real failure mode: user pays a rounded amount and
  the VA never confirms).
- VA expiry: same idea as QRIS — expired VA shows a clear "expired, start
  again" state.
- Copy-to-clipboard button for the VA number (if present) actually copies
  the full, correct number.

## E-wallets (GoPay, OVO, DANA, ShopeePay, LinkAja)

- Selecting an e-wallet method redirects/deep-links to the correct
  provider flow (or shows the correct provider's QR/instructions) — not a
  generic fallback that doesn't match the selected provider.
- The amount passed to the e-wallet matches the order total.
- Insufficient balance in the sandbox/test account (if testable) → a clear
  decline message, order not marked paid.
- Returning to the merchant site after a cancelled e-wallet payment (user
  backs out of the provider's app/page) → the order is left in a
  recoverable "not paid, try again" state, never silently marked paid.
- Payment method logos/branding shown actually match what's selectable —
  a mismatch (showing GoPay's logo but routing to a different provider) is
  a real, reportable bug even though it's cosmetic, since it affects trust.

## General Indonesian payment notes

- Rupiah has no commonly-used subunit in consumer UI — a payment form that
  accepts/displays `Rp 10.500,25` (fractional Rupiah) is almost certainly
  a formatting bug, not a feature; flag it.
- Processing fees (admin fees) for certain channels (some VA/e-wallet
  providers charge one) should be shown BEFORE the user commits to a
  method, not sprung on them at the final confirmation.
- Never attempt to actually complete a real payment with real money or
  real provider credentials during a test — this is a destructive, costly
  action and is out of scope even with security/owner confirmation. Stop
  at "payment initiated, correct amount/method shown" unless the merchant
  has an explicit sandbox/test-mode toggle AuraCheck is told to use.
