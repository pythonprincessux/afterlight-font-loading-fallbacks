# Test Record — Font Loading & Fallbacks
**Course:** GIT 337 — Practice: Font Loading and Fallbacks
**Name:** Kiara McRae
**File:** `index.html` + `styles.css`


## 1. Font source & licensing

One web font, one system stack — chosen to keep the fallback test clean (a single point of
failure, so any change on reload can be attributed to Fraunces specifically).

| | Display face (web font) | Body face (system stack) |
|---|---|---|
| Family | Fraunces | system-ui sans (no web font) |
| Source | Google Fonts (`fonts.googleapis.com`) | OS-installed fonts only |
| Designer | Un-Type (Wei Huang, Kristyan Sarkis et al.) | n/a |
| License | SIL Open Font License 1.1 | n/a — no font file requested |
| Weights loaded | 400, 600, 700 (variable optical-size axis 9–144) | whatever weights the OS ships |
| Requests the page makes | 1 stylesheet request to `fonts.googleapis.com/css2`, triggering `.woff2` requests to `fonts.gstatic.com` | none |

# Afterlight Journal - Font Loading and Fallbacks Test Record

## 320px Viewport

**Condition:** 320 CSS px at 100% zoom with Fraunces loaded.

**Expected:** Typography should remain readable and contained without clipping or page-level horizontal overflow.

**Observed:** The H1 wrapped within the available space, body text remained readable, buttons remained contained, and the token table reflowed within the viewport. DevTools measurements showed `scrollWidth = 320` and `clientWidth = 320`, confirming no page-level horizontal overflow.

**Revision:** No revision was required from the initial 320px test.

## Wide Viewport

**Condition:** 1280 CSS px at 100% zoom.

**Expected:** Content should remain readable and appropriately constrained rather than stretching excessively across the viewport.

**Observed:** The specimen remained contained at the wide viewport and the text measure prevented long-form content from stretching excessively. DevTools measurements showed `scrollWidth = 1280` and `clientWidth = 1280`, confirming no page-level horizontal overflow.

**Revision:** No revision required.


## 200% Zoom

**Condition:** Browser zoom increased to 200%.

**Expected:** Typography should reflow while remaining readable, contained, and operable without clipping or page-level horizontal overflow.

**Observed:** At 200% zoom, the effective viewport measured 267 CSS px. Content reflowed without clipping or page-level horizontal overflow. DevTools showed `scrollWidth = 267` and `clientWidth = 267`. The H1 remained readable but became unusually tall as the available width decreased.

**Revision:** The minimum H1 size was reduced from `2rem` (32px) to `1.875rem` (30px). The fluid middle value and `3.375rem` maximum were retained so the change affected the narrow end of the scale without changing the intended wide-screen hierarchy.


## Preferred Font Loaded

**Condition:** Normal page load with Fraunces available.

**Expected:** Display headings should render using Fraunces while body content uses the system-font stack.

**Observed:** Fraunces rendered as the display typeface and the system-font stack rendered the body typography. The display and body roles remained visually distinct.

**Revision:** No revision required.


## Preferred Font Blocked

**Condition:** Fraunces was blocked using Chrome DevTools request blocking and the page was reloaded.

**Expected:** The browser should use the defined serif fallback while preserving readable hierarchy and usable layout.

**Observed:** DevTools showed the Fraunces Google Fonts request as blocked. The browser substituted the serif fallback. At 320px, the H1 remained readable with essentially the same line wrapping, hierarchy remained clear, body text remained stable because it used a system-font stack, and no clipping or obvious overflow occurred.

**Revision:** The fallback itself did not require a stack change because it preserved the intended hierarchy and layout.


## Evidence-Based Revision

**Initial observation:** During the 200% zoom test, the effective viewport became substantially narrower. Although the H1 remained readable and did not overflow, its 32px minimum size caused the heading to become unusually tall.

**Decision:** Adjust only the narrow-end H1 size rather than changing the entire type scale or fallback stack.

**Revision:**

Before:

`--fs-h1: clamp(2rem, 1.5rem + 2.8vw, 3.375rem);`

After:

`--fs-h1: clamp(1.875rem, 1.5rem + 2.8vw, 3.375rem);`

This reduced the minimum H1 size from 32px to 30px while preserving the fluid scaling behavior and 54px maximum.

**Final verification:** Record your final retest result here after confirming the revised H1 remains readable at 320px, with Fraunces blocked, and at 200% zoom.

**Fallback stacks (in load order):**
- Display: `"Fraunces", "Iowan Old Style", "Palatino Linotype", "Book Antiqua", Georgia, serif`
- Body: `-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif`


## 2. Token system

| Token | Value | Purpose |
|---|---|---|
| `--font-display` / `--font-body` | font stacks above | family tokens |
| `--fs-base` | `1rem` (16px) | anchor for all relative sizing |
| `--lh-body` / `--lh-display` | `1.55` / `1.14` | dense display type needs tighter leading than long-form paragraphs |
| `--measure-body` / `--measure-lead` | `66ch` / `52ch` | caps line length at any viewport width |
| `--fs-label` → `--fs-h1` | 8-step `clamp()` scale, ~1.2 ratio | bounded fluid scale — hard min at 320px, hard max on ultra-wide |
| `--tap-min` | `44px` | minimum button height, independent of the fluid scale |


## 3. Viewport & zoom test record

**Procedure:** DevTools → device toolbar (`Ctrl/Cmd+Shift+M`) for 320px and wide widths; browser
zoom (`Ctrl/Cmd` + `+`) for 200%. Screenshot each state.

| Test | What to check | Observation |
|---|---|---|
| 320px width, Fraunces loaded | H1 wrapping, button containment, any clipping | Passed at 320×320. H1 wrapped to four short lines, hierarchy still clear, no clipping or horizontal overflow. |
| Wide viewport (1280px+) | Does `--fs-h1` stay proportionate? Does the 66ch measure keep lines from stretching? | Passed at 1280×1280. Heading scaled up proportionately, body measure kept paragraphs from stretching edge-to-edge. |
| 200% browser zoom | Clean reflow, no horizontal scrollbar, tap targets still usable | Effective viewport narrowed to ~267px. No horizontal scrollbar, but the H1 became noticeably tall relative to the available width — this is what prompted the revision in Section 5. |


## 4. Blocked-font evidence

**Procedure:** DevTools → Network → reload once → right-click the Fraunces request(s) → Block
request URL/domain → reload again → compare to Section 3.

| Question | Observation |
|---|---|
| Fallback that actually rendered (check Elements → Computed → `font-family`, bolded = winner) | Fraunces requests blocked in Network tab; Computed styles showed the fallback chain resolving to Georgia. |
| Did H1/H2 wrapping change line count? | H1 wrapping stayed essentially the same at 320px — letterforms and metrics changed, but line breaks held. |
| Any overflow, clipping, or lost alignment on buttons/tags/metadata? | None observed. Buttons stayed contained, no clipping, no overflow. |
| Did hierarchy (display vs. body vs. metadata) still read clearly? | Yes — hierarchy remained obvious with the fallback in place. |


## 5. Revision log

**Observed:** At narrow widths and high zoom, `--fs-h1`'s minimum bound of `2rem` (32px) kept the
heading from shrinking further, which made it disproportionately tall relative to the available
width as the viewport narrowed.

**Decision:** Reduce only the minimum bound of `--fs-h1` rather than touching the rest of the
scale, so the desktop maximum and overall hierarchy stay untouched.

**Revision applied:** `--fs-h1` minimum changed from `2rem` (32px) → `1.875rem` (30px). Maximum
stays `3.375rem` (54px); the fluid `vw` term is unchanged.

```css
/* before */
--fs-h1: clamp(2rem, 1.5rem + 2.8vw, 3.375rem);
/* after */
--fs-h1: clamp(1.875rem, 1.5rem + 2.8vw, 3.375rem);
```

**Retest:** Rechecked the revised heading at 320px with Fraunces loaded, 320px with Fraunces
blocked, and at 200% zoom. The heading retained clear hierarchy and readable wrapping without
clipping or page-level horizontal overflow at any of the three checks.

