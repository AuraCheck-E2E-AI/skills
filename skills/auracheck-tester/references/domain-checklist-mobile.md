# Domain checklist: mobile (web and native)

## Mobile web (responsive)

Runs through AuraCheck's normal cloud browser runner at a narrow viewport —
no special setup needed beyond the project supporting a mobile viewport.

- Primary flow (signup, checkout, the main purchase/contact action)
  completable at a narrow width (~375px) without horizontal scroll or
  clipped buttons.
- Tap targets are large enough to be distinguishable (a desktop-sized
  checkbox/link crammed into a mobile layout is a common real finding).
  AuraCheck's own assertions describe what's visible, not pixel sizes — a
  case can still note "button overlaps adjacent text at narrow width" as an
  observed defect.
- A modal/dialog that works on desktop doesn't trap scroll or overflow the
  viewport on mobile width.
- Sticky headers/footers (common on mobile checkout — a sticky "Pay" bar)
  don't cover content or another actionable button.

## Native Android / iOS — AuraCheck desktop-app-only

**Important constraint, not a limitation to work around**: AuraCheck's
cloud runner only drives a web browser. Android and iOS test cases are
authored exactly like web ones (same Midscene-flow steps), but they are
**run by a person, in the AuraCheck desktop app, on their own device or
emulator** — never automatically in the cloud. If a flow needs a real
Android/iOS run, `start_run`/`run_suite` will return a clear
"open the AuraCheck desktop app and click Run on local / Run on iOS"
instruction rather than silently failing. Don't suggest a cloud mobile run
exists; don't promise automatic execution for mobile cases.

When authoring mobile-specific cases anyway (so they're ready for the next
desktop-app run):
- OS-level permission prompts (camera, location, notifications,
  contacts) — does the app explain WHY before asking, and does denying
  gracefully degrade rather than crash/dead-end the flow?
- App backgrounding mid-flow (switch away and back) — state preserved,
  especially mid-payment or mid-upload.
- Deep links (a link that should open the app directly to a specific
  screen) actually land on that screen, not just the app's home.
- Push notification tap → opens to the relevant content, not just the
  app's default screen.
- Native keyboard type matches the field (numeric keyboard for a phone/OTP
  field, email keyboard for email) — a cosmetic-seeming but real
  usability finding worth noting.

## Both

- Orientation change (portrait↔landscape) mid-flow doesn't lose entered
  data (web: resize the viewport as a proxy if a true orientation event
  isn't available).
- Slow network (throttle if the runner supports it, or at minimum note
  this as untested if it doesn't) — forms should show a loading state,
  not look frozen/unresponsive during a slow submit.
