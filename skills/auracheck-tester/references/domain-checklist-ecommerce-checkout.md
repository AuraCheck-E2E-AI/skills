# Domain checklist: e-commerce cart & checkout

The highest-stakes flow on most sites AuraCheck tests — real money, real
inventory, real race conditions.

## Cart

- Add one item → cart count/total updates correctly.
- Add the same item twice → quantity increments, doesn't create a second
  line (unless the product deliberately allows duplicate lines — check
  actual behaviour).
- Increase quantity past available stock → blocked or clamped with a clear
  message, never silently allowed past stock then failing at checkout.
- Remove an item → cart total recalculates correctly, cart-empty state
  renders correctly when it was the last item.
- Cart persists across a refresh (and, if the product claims it, across a
  logged-out → logged-in transition — does a guest cart merge into the
  account cart, or get lost?).
- Price/quantity edits reflected immediately in the displayed total — not
  just on the next page.

## Promo codes / discounts

- Valid, active code → correct discount applied, visible before payment.
- Expired code → rejected with a clear message.
- Code below its minimum-spend threshold → rejected with the threshold
  stated, not a generic error.
- Code already used (if single-use) → rejected on second use.
- Invalid/typo'd code → rejected, doesn't silently apply 0% as if it
  "worked."
- Stacking two codes, if the UI allows entering more than one → check the
  actual behaviour (reject the second, or combine) and report what happens,
  not what "should" happen.
- Boundary: a code that would discount the order to exactly Rp 0, or below
  it — never allow a negative total.

## Shipping

- Each supported destination/method computes a sane cost and ETA.
- An unsupported/unserviceable destination → clear rejection, not a
  silent Rp 0 shipping cost or a crash.
- Changing address after selecting a shipping method → cost recalculates
  (or the method resets with a clear notice) — it should never keep a
  stale cost for a new address.

## Out-of-stock races

- Add an item to cart, have stock drop to zero before checkout (simulate by
  checking a genuinely low-stock/limited item if the site has one) →
  checkout blocks the item with a clear message, doesn't charge for
  something it can't fulfill.
- Last unit: two near-simultaneous checkouts for the same last unit — not
  always practically testable in a single AuraCheck run, but worth a note
  in the suite if the product is known to have this risk (flash sales,
  limited drops).

## Checkout form

- All the generic form checklist items apply — see
  [[domain-checklist-forms]] — plus:
- Required fields (name, address, phone) enforced before "Place order" is
  enabled/accepted.
- Address field accepts realistic Indonesian addresses (RT/RW, kelurahan/
  kecamatan, postal code format) without being forced into a US-shaped
  address form — see [[indonesian-market-edge-cases]].
- Guest checkout (if offered) actually completes without forcing signup.

## Payment

- See [[domain-checklist-payments-id]] for Indonesian payment-method
  specifics (QRIS, VA, e-wallets).
- A declined/failed payment → the cart/order is NOT marked paid, the user
  sees a clear retry path, and the item isn't released from the user's cart
  before they've had a chance to retry (unless intentionally).
- Payment timeout (simulated or via a method known to be slow) → no
  double-charge if the user retries; order ends in a sane state, not stuck
  "processing" forever with no user-visible recovery.
- Double-submit on "Pay now" → exactly one order/charge — this is the
  state-transition technique applied to the highest-stakes button on the
  site; treat it as close to mandatory for any checkout suite.

## Order confirmation

- Confirmation page/email reflects the ACTUAL order (items, quantities,
  total, shipping) — cross-check against what was in the cart, not just
  that "a" confirmation appeared.
- Order appears in order history (if the account has one) with a status
  that matches what just happened.
