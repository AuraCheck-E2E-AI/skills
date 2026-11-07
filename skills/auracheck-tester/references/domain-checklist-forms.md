# Domain checklist: generic forms

Any form on any site — contact, profile edit, settings, a wizard — not just
checkout or signup. Apply this when you discover a form that doesn't fit
one of the other domain checklists.

## Validation

- Every required field, left empty, blocks submission with a field-level
  message — not just one generic "please fill the form" banner.
- Every field's stated format (email, phone, date, number) rejects an
  obviously wrong value in that format.
- Client-side validation matches server-side validation: submit a value
  that would pass a weak client check but violates the real rule (if you
  can construct one) and confirm the server still rejects it — never trust
  the client-side check alone for anything that matters.
- Whitespace-only input in a required text field → treated as empty, not
  accepted as valid content.
- Leading/trailing whitespace in a real value (" John ") → trimmed or
  handled sanely, not stored/displayed with stray spaces that look like a
  bug.

## Boundary values (apply per field)

- Text field: 1 char, the stated max, max+1 — see
  [[test-design-techniques]] §Boundary.
- Number field: 0, negative (if not allowed), the stated max, max+1,
  decimal where only integers are allowed.
- Date field: today, the earliest/latest allowed, one day outside range,
  an impossible date (Feb 30) if the picker allows free text entry.
- Dropdown/select: the first option, the last option, and — if it's a
  searchable/combo field — a value not in the list typed directly.

## File upload

- Accepted file type at the max stated size → succeeds.
- Rejected file type (e.g. .exe where only images are allowed) → rejected
  with a clear message, never silently accepted.
- File over the max size → rejected before or shortly after upload starts,
  with a clear size-limit message.
- Zero-byte file → rejected or handled explicitly, not a silent crash.
- Re-uploading to replace a previous file (if supported) → old file is
  actually replaced, not duplicated.

## Multi-step forms

- Progress is preserved across steps when going back to an earlier step and
  returning — see the State-transition technique.
- Submitting a step with the browser back button open in another tab
  doesn't corrupt progress in the first tab (lower priority — only test if
  the product is known to be used this way).
- The final submit genuinely uses the data from EVERY step, not just the
  last one visited (a common real bug: step 2's edit doesn't make it into
  the final payload because of stale client state).
- Validation errors on an early step surfaced when the user tries to jump
  ahead — not silently allowed through to a later step.

## Autosave / draft forms

- If the form claims to autosave, verify it actually does (refresh mid-fill
  and check the draft is there) rather than trusting a "saved" indicator
  that doesn't reflect real persistence.

## Error handling

- A server-side rejection (duplicate entry, business-rule violation) is
  shown as a specific, field-attributable message where possible — not a
  generic "something went wrong."
- A network failure during submit → the user can tell the submit didn't
  go through, and retrying doesn't double-submit.
