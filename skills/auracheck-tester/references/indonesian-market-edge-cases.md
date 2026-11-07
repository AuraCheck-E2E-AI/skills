# Indonesian-market edge cases

Edge cases specific to products targeting or operating in Indonesia — apply
these across whichever domain checklist you're using, not as a separate
flow.

## Phone numbers

- Both common formats should work if the form claims Indonesian support:
  `+6281234567890` (international) and `081234567890` (local, leading 0).
- A form that silently strips or mishandles the leading `0` vs `+62` is a
  real, common bug — test both forms with the SAME underlying number and
  confirm they're treated as equivalent if the product says they should be.
- Length varies by provider (typically 10–13 digits after the country/
  trunk prefix) — don't hardcode an exact length assumption; test a short
  valid number and a long valid number, and one that's clearly too short/
  long to be any real Indonesian number.
- Landline numbers (with area codes, e.g. `021...` for Jakarta) if the form
  doesn't explicitly say "mobile only."

## NIK (national ID number)

- 16 digits exactly — test 15 (reject) and 17 (reject) if the form
  validates length.
- NIK embeds a birth date and region code; some forms cross-validate against
  a separately-entered birth date — if so, test a NIK/birth-date mismatch
  and confirm it's caught (if the product claims to do this check at all;
  don't assume it does).
- NIK is sensitive PII — never generate or submit a real person's NIK in a
  test; use an obviously-synthetic value (AuraCheck's own test-data
  conventions, or a clearly fake pattern) and say so if asked.

## Rupiah formatting

- Thousands separator is a period in Indonesian locale (`Rp 1.500.000`),
  not a comma — a form/display using `Rp 1,500,000` for an ID-locale user
  is a real formatting bug worth flagging.
- No commonly-used subunit in consumer-facing UI — Rupiah amounts are
  whole numbers; a field accepting `Rp 10.500,25` is very likely a bug.
- Currency symbol placement and spacing (`Rp 50.000` vs `Rp50.000` vs
  `50.000 Rp`) — note inconsistency within the SAME product as a real
  finding, not a stylistic preference.

## Bahasa Indonesia / English toggle

- If the product offers a language switch, re-run (or at least spot-check)
  the primary flow in BOTH languages — a common real bug: validation/error
  messages stay in English after switching to Bahasa (or vice versa), while
  static UI text switched correctly.
- Date formats often differ by language context (`DD/MM/YYYY` vs spelled-
  out Indonesian month names like "7 November 2026") — check the displayed
  format matches the active language, not just that a date renders.
- Pluralization: Indonesian doesn't inflect nouns for plural the way English
  does ("1 barang" vs "2 barang", not "2 barangs") — a direct English-string
  i18n bug sometimes leaks an English plural suffix onto Indonesian text.

## Addresses

- RT/RW (neighborhood/sub-neighborhood unit), kelurahan/desa (village),
  kecamatan (district) fields — a form built on a generic Western address
  template (just "street, city, state, zip") forces Indonesian users into
  a format that doesn't fit; note this as a real finding if the product
  claims to target Indonesian addresses but uses a generic field set.
- Postal code is 5 digits.
- Province dropdown, if present, should list all current Indonesian
  provinces (including newer-split ones) — a stale list missing a
  recently-split province is a real, checkable finding.

## Dates and holidays (where relevant to the flow)

- Indonesian national holidays affect delivery-date estimates, business-day
  calculations, and support-response-time promises — if a flow quotes a
  delivery/response date, sanity-check it doesn't quietly assume every day
  is a business day when the product claims otherwise.
