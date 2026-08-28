# AI Disclosure & Verification Record

**Course:** GIT 337 — Practice: Font Loading and Fallbacks
**Name:** Kiara McRae
**AI permission level for this assignment:** AI-Assisted with Disclosure — "AI may compare
possible font stacks or propose test cases. You must verify syntax, licensing, loading, and
rendered behavior yourself."

## What AI assistance was used

ChatGPT was used to:
- Propose an initial font pairing (Fraunces display + Inter body, later simplified to Fraunces
  alone with a system-font body stack) with metric-compatible fallbacks.
- Draft the tokenized CSS type scale (`--fs-label` → `--fs-h1`, line-height, measure tokens).
- Scaffold `index.html`, applying those tokens to headings, long-form text, metadata, buttons,
  and compact labels.
- Propose the DevTools-based test procedure for the 320px / wide-viewport / 200%-zoom checks and
  the font-blocking test.
- Split the single-web-font decision and file organization (`index.html`, `styles.css`,
  `test-record.md`, `ai-disclosure.md`) at my direction.

## What was verified independently

I independently verified the implementation and test results in Chrome and Chrome DevTools.

- I confirmed Fraunces is distributed under the SIL Open Font License 1.1 using its Google Fonts licensing information.
- I tested the specimen at a 320 CSS px viewport. DevTools showed `scrollWidth = 320` and `clientWidth = 320`, confirming no page-level horizontal overflow.
- I tested the specimen at a 1280 CSS px wide viewport. DevTools showed `scrollWidth = 1280` and `clientWidth = 1280`, and the constrained text measure kept the content readable rather than allowing it to stretch across the viewport.
- I tested the specimen at 200% browser zoom. The effective viewport measured 267 CSS px, with `scrollWidth = 267` and `clientWidth = 267`. Content remained readable and reflowed without page-level horizontal overflow.
- I used Chrome DevTools Network request blocking to make the preferred Fraunces font unavailable and reloaded the page. The Network panel showed the Fraunces request as blocked, and I observed the browser use the defined serif fallback.
- I compared the loaded-font and blocked-font versions at 320px. The fallback preserved the display hierarchy and essentially the same H1 wrapping without clipping or page-level overflow.
- I confirmed that body typography remained stable during the blocked-font test because the body uses a system-font stack rather than a requested web font.
- Based on the narrow/zoom behavior I observed, I reduced the minimum value of `--fs-h1` from `2rem` (32px) to `1.875rem` (30px) while retaining the existing fluid value and `3.375rem` maximum.
- I re-tested the revised implementation myself and recorded the resulting behavior in `test-record.md`.
- All screenshots, Network observations, viewport measurements, wrapping observations, and rendered-behavior results documented in `test-record.md` came from tests I performed directly in the browser.

*(Fill in after doing the work — e.g., "Confirmed Fraunces is OFL-licensed on its Google Fonts
license page. Ran the 320px, wide-viewport, and 200% zoom tests in Chrome and captured
screenshots. Blocked the Fraunces request in DevTools Network tab, reloaded, and confirmed the
fallback font and any wrapping changes noted in test-record.md. Made the revision logged there
and re-tested it myself.")*

## Note on scope

ChatGPT cannot render a live browser, capture real screenshots, or confirm how fonts actually
load on this machine — all rendered evidence in `test-record.md` comes from testing performed by me
directly in-browser, not from ChatGPT. 
ChatGPT was used for permitted comparison, troubleshooting, organization, and test-planning assistance. I did not treat AI output as evidence that the implementation worked. Font licensing, viewport behavior, zoom behavior, network/font-loading behavior, fallback rendering, overflow measurements, and the final revision were independently checked by me using the relevant source information, Chrome, and Chrome DevTools. The observed results recorded in `test-record.md` reflect my own browser testing.

