# Bug report template

Use this shape whenever turning a run failure into a `file_bug` call or a
report to the person. One bug per distinct root cause — three failing test
cases that all hit the same broken endpoint are ONE bug, not three.

## Fields

- **Title** — outcome language, not test-case language. "Checkout fails
  when a promo code is expired," not "Test case 14 failed" or "Assertion
  error on step 6."
- **Severity** — from user impact, not from how the test happened to be
  tagged:
  - `critical`: blocks a core flow for everyone (can't sign up, can't check
    out, site down).
  - `high`: blocks a core flow for some users/conditions (fails only for one
    role, one payment method, one input class).
  - `medium`: a real defect, workaroundable (wrong error message, a cosmetic
    but functionally-blocking layout issue).
  - `low`: cosmetic, no functional impact.
- **Steps to reproduce** — copied from the failing case's actual steps, in
  plain language, not raw YAML/Midscene syntax unless the person asked for
  that level of detail.
- **Expected** — what should have happened (from the case's expectedResult,
  or the obvious correct behaviour if none was stated).
- **Actual** — what really happened, in concrete terms ("the order total
  stayed at Rp 150.000 after applying a 50%-off code," not "the assertion
  failed").
- **Evidence** — the run link, plus the first failure's screenshot
  (`get_evidence` → `get_screenshot`). Attach more than one screenshot only
  when the failure is genuinely a sequence (e.g. before/after a race
  condition).
- **Likely cause** — a SEPARATE, clearly-labeled guess, kept apart from
  "Actual" so the factual observation doesn't get contaminated by a guess
  that might be wrong (e.g. "likely cause: the discount calculation runs
  before the minimum-spend check" — label it as a guess, not a fact).
- **Bug vs. test-authoring issue** — say explicitly which one this looks
  like. A test that fails because of a flaky selector or a wrong assumption
  about the UI is a TEST issue, not a product bug — don't file it as one;
  instead fix or flag the test case.

## Grouping failures into bugs

Before filing anything, group the run's failures by apparent root cause
(same error message, same broken element, same endpoint failing) — this is
exactly what `run_suite`'s aggregation step does for you (its
`suggestedBugs` list is already deduplicated this way). Review that list
rather than filing one bug per failing case.

## What NOT to do

- Don't file a bug for a flaky run with no clear repro (re-run first; if it
  passes the second time and you can't explain the flake, say so instead of
  filing).
- Don't include a literal password/PIN/OTP value in a bug report, even if
  one somehow appeared in a step's text — scrub it.
- Don't claim certainty about a root cause you haven't actually verified
  (reading server logs, source code) — phrase it as a hypothesis.
