# Domain checklist: search, filter, sort

## Search

- A query that matches exactly one known item → that item appears, ranked
  sensibly (not buried under unrelated results).
- A query with no matches → a clear "no results" state, never a blank page
  or an infinite spinner.
- A query with a typo (one character off a real term) → either fuzzy-
  matches to the intended result, or falls through cleanly to "no results"
  — report the actual behaviour; don't assume fuzzy matching exists if the
  product never claimed it.
- Empty query submitted → either shows all items, shows a prompt to type
  something, or is prevented from submitting — whichever the product does,
  check it's deliberate and not a crash.
- Very long query string (e.g. 500+ characters pasted in) → handled
  gracefully, not a 500 error.
- Special characters in the query (`%`, `_`, `'`, `"`, `<`, emoji) → treated
  as literal search text, never causing an error page or (if security
  checks are in scope) a SQL/markup injection — see
  [[test-design-techniques]] §Security.
- Query in Bahasa Indonesia and in English, if the product serves both
  markets — do results come back for both, or only one language's content?

## Filters

- Each individual filter narrows results correctly (apply one, check every
  shown result actually matches it).
- Combining two or more filters narrows further (AND semantics) rather than
  accidentally showing the union (OR) unless the product explicitly works
  that way — check and report the real behaviour.
- A filter combination with zero matching results → clear empty state, not
  a crash, and the applied filters remain visible so the user can adjust.
- Clearing one filter (not all) restores results to the state as if that
  filter was never applied, without resetting the others.
- Price-range filter boundary: the min and max values themselves should be
  INCLUDED in results (off-by-one is a common real bug here).

## Sort

- Each sort option actually reorders results correctly (price low→high
  really is ascending; "newest" really is by recency, not by default
  relevance).
- Sort persists across pagination (page 2 is a continuation of the same
  sorted order, not re-sorted from an unsorted page 2).
- Sort combined with an active filter/search still respects both.

## Pagination / infinite scroll

- Last page shows the correct remainder count, not a stray empty page.
- Changing a filter/sort resets pagination to page 1 (a common real bug:
  staying on page 3 of a now-5-result filtered set, showing nothing, no
  feedback that you need to go back to page 1).
- Rapid-fire "next page" clicks don't skip or duplicate results.
