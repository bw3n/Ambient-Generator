# Ambient Gen — Redesign Notes

This document tracks the changes made to the `Ambient Gen` project, a generative ambient music player that creates long-form, evolving music using Web Audio. The redesign brings the player in line with a new design reference (`gradient.ai`) — a single linear vertical gradient, white serif title, white-outlined dots, white chevron circles, white tag pills, and a thin white stroke at the bottom that expands into a tall white drawer.

The working file is `/Users/boon/Ambient Gen/outputs/generative-player.html`. The original (pre-redesign) backup is `generative-player.html.before-any-edit`.

---

## The design reference (`gradient.ai`)

The redesign was driven by `gradient.ai` — a single-page design mockup. Key visual elements:

- **Single vertical linear gradient** — `g1` (dark) at top → `g2` (mid) → `g3` (light/near-white) at bottom. No centre radial wash, no sun glow, no grain.
- **Title** — large Instrument Serif, weight 400, solid white, lower-middle of viewport.
- **Palette dots** — 9 small white-outlined circles at top-center; the active one is solid white with a subtle glow halo.
- **Chevron arrows** — white-outlined circles at left/right viewport edges for piece navigation.
- **Tag pills** — small rounded pills with dark blue text on translucent white fill, below the title.
- **Bottom stroke** — a thin horizontal pill at the bottom of the viewport; on hover/click it expands into a tall white drawer.

---

## Redesign — version history

The redesign was done incrementally with small, reversible patches. Each version (`vN.M`) is tagged in the source code with a comment explaining what changed and why.

### v1.0 — gradient reset

Replaced the previous multi-layer background (top highlight + centre radial wash + bottom vignette + solid + body::before sun glow + body::after SVG grain overlay) with a single vertical linear gradient. Stop order: `g1` (top) → `g2` (mid) → `g3` (bottom). The previous v0.8 stacked darkening effects that made corners muddy. The new design reads as "sky overhead, fog below."

Fixed a pre-existing bug: the `--g1`/`--g2`/`--g3` CSS variables were never declared with defaults in `:root`, so initial render showed empty values. Added `:root { --g1: #1a4d6e; --g2: #2a8aa8; --g3: #a8e8f8; }` defaults and `@property` registration for smooth interpolation.

### v1.1 — typography (Instrument Serif + Google Sans Flex)

Loaded both fonts from Google Fonts. Title (`.title`) became Instrument Serif weight 400, solid white, no gradient fill — `font-size: clamp(180px, 32vw, 340px)` at the time. Tag pills (`.tag`) became Google Sans Flex, weight 500, 14px, dark blue text (`#1a4d6e`) on translucent white fill, 8px 18px padding.

### v1.2 — palette dots restored

Re-introduced the top palette dots with active-state glow. Inactive: 8px white-outlined circles. Active: solid white with `box-shadow: 0 0 0 1px ... 0 0 8px 2px ... 0 0 20px 6px ...` for a soft halo.

### v1.3 — tags reduced and styling fixed

Reduced tag padding from `5px 11px` to `4px 8px`. Tags now fit on a single row instead of wrapping. Tag text color changed from white to dark blue (`#1a4d6e`) on translucent white pill — the old white-on-white was invisible against the pale gradient bottom.

### v1.7 — comprehensive bottom drawer redesign

This is the largest single change. The bottom area (a translucent dark scrim pill with VOL slider, SEED input, etc.) was redesigned as a proper bottom-drawer that slides up on click.

**Drawer structure:**
- `.drawer` — fixed-position container anchored to viewport bottom, z-index 200
- `.drawer-handle` — the thin pill that floats above the viewport bottom (always visible)
- `.drawer-body` — the panel that slides up on click, contains header strip + rules

**Multiple sub-iterations:**

- **v1.7a-d** — initial drawer structure with hover-to-open, body slides up from below
- **v1.7e** — card flush with viewport bottom, fully opaque white
- **v1.7f** — JS hover management with shared `.preview` class
- **v1.7g** — body flush to bottom, pill floating above
- **v1.7h** — wider thicker pill matching gradient.ai reference
- **v1.7j** — pill floats with breathing room (56px above viewport bottom)
- **v1.7k** — pill behind card (body z-2, pill z-1)
- **v1.7l** — card stacked high above page (drawer z-200)
- **v1.7m** — subtle elevation shadow on card
- **v1.7n** — pill behind card stacking fix
- **v1.7p** — completely white pill (no dark outline ring)
- **v1.7q** — click-to-open with bouncy pill animation
- **v1.7r** — smooth slide-up (no bounce) + subtle press state
- **v1.7s** — play button moved up
- **v1.7t** — tags 50% smaller, start button lower
- **v1.7u** — title 25% smaller (340px → 255px max)
- **v1.7v** — smaller dots, tighter gap (8px / 10px)
- **v1.7w** — responsive breakpoints (tablet ≤860px, mobile ≤480px)
- **v1.7x** — drawer-head wraps on narrow viewports
- **v1.7y** — universal safe zone tokens (`--safe` 24/20/16)
- **v1.7z** — rules-head `justify-content: space-between`
- **v1.7aa** — chevrons hidden when drawer is open

### The final pill state

After all the iterations, the bottom pill is:
- 140px wide × 5px tall (50% smaller than the original 280×10)
- Solid white, no border, no shadow
- Floats 40px above viewport bottom with breathing room
- Behind the card when the drawer is open (body z-2, pill z-1)
- Hover state: widens to 180px, subtle 0.35s easing
- Press state: widens to 200px with a subtle dark background hint (15% opacity)
- Click state: pins open (`.open` class)
- All transitions use `cubic-bezier(0.4, 0, 0.2, 1)` (Material standard easing) — no bouncy overshoot

### The final card state

The drawer card is:
- Full-width at mobile, max 960px at desktop
- Flush with viewport bottom (no gap)
- Fully opaque white (`#fff`)
- Subtle drop shadow visible at top edge when open (`0 -8px 32px rgba(20, 60, 84, .15)`)
- Slides up smoothly from below the viewport on click
- Contains a header strip (VOL/SEED/DURATION/idle/×) and a rules body (Rules heading, Randomise/Copy link/Reset, preset chips, rule grid)
- All content uses the universal safe zone token (`--safe`) for consistent horizontal padding

### Universal safe zone tokens

Introduced CSS custom properties that update at each breakpoint:
- `--safe` — horizontal padding for primary surfaces (24px desktop, 20px tablet, 16px mobile)
- `--safe-tight` — tighter safe zone for nested elements (20/16/12px)

Applied to: `.drawer-head`, `.drawer-body #rules`, `.tags`, and other key surfaces.

### Responsive breakpoints

Three breakpoints with token updates at each:
- **Desktop** (≥861px) — `--safe: 24px`, `--safe-tight: 20px`
- **Tablet** (≤860px) — `--safe: 20px`, `--safe-tight: 16px`
- **Mobile** (≤480px) — `--safe: 16px`, `--safe-tight: 12px`

The drawer adapts at each breakpoint:
- Mobile: drawer is full-width, max-height 75vh, play button is icon-only (no "Start" label)
- Tablet: tighter padding, smaller title (140px max)
- Short height (≤600px): drawer body max-height 80vh

---

## Visual hierarchy and composition

The final composition reads top-to-bottom as one vertical stack:

1. **Compact dot strip** (8px dots, 10px gap) at top — piece navigation
2. **Title** (Instrument Serif, 255px max, weight 400) — dominant focal point
3. **Tag row** (compact pills with dark blue text on white)
4. **Start button** (translucent white with dark icon, 20px gap from tags)
5. **Large gap** of gradient
6. **Floating pill** (140×5px white) at the bottom, 40px above viewport edge
7. **Drawer** (slides up from below, contains all controls)

The chevrons (‹ ›) are visible only when the drawer is closed. When the drawer opens, they fade out (`.drawer-open` body class triggers `body.drawer-open .carousel-arrow { opacity: 0; pointer-events: none; }`).

---

## Pitfalls hit and lessons saved

Several pitfalls were hit and saved to the `ambient-gen-redesign-pitfalls` skill in `~/.hermes/skills/`. Key ones:

1. **The "padding + background-clip: content-box" hit-area trick FAILS under `box-sizing: border-box`**. With universal `* { box-sizing: border-box }`, `height: 6px` with `padding: 14px 0` clamps the content area to 0px — the bg has nowhere to render. Solution: use a `::before` pseudo-element for hit-area extension, with the visible pill as a separate element.

2. **The pill appeared as "just an outline shape" because white-on-pale-cyan is too low-contrast**. The diagnosis-fix cycle of just adjusting math doesn't solve visual problems. Verified that the actual problem was 6px of white on `#a8e8f8` being drowned by the box-shadow ring. Real fix: use a pseudo-element for hit area, separate visible pill as plain white, height 10px (taller to be perceptible).

3. **CSS `:hover` does NOT propagate from `position: absolute` children to a flex parent**. A `position: absolute` child is taken out of normal flow. The parent's hover only matches the parent's flex-painted bounds, not the absolute child's painted position. Solution: JS mouseenter/mouseleave on both pill and body with shared `.preview` class.

4. **Universal `* { box-sizing: border-box }` breaks every "use padding to expand hit area" trick**. If you've ever set `* { box-sizing: border-box }` (Apple-style reset), then EVERY CSS trick that relies on padding being additive to height silently fails.

5. **`translateY(100%)` from `bottom: 100%` doesn't go off-screen**. With `bottom: 100%` and `height: 443px` and `translateY(100%) = 443px`, the body's top moves from y=146 to y=589 — still inside the viewport. The top 44px of the body's box is visible. Solution: anchor with `bottom: 0` and use `translateY(100%)` to shift fully off-screen below.

6. **Stacking context bug — card stacked behind pill on slide**. The drawer container creates a stacking context (z-index: 200, position: fixed). Inside that context, the pill at z-199 was higher than the body at z-1. The fix: set body's z-index to 2 within the drawer — body's z-2 > pill's z-1 within the drawer's stacking context.

7. **Agent tool's `browser_click` sometimes doesn't toggle state class**. `browser_click` (Playwright) fires `mouseenter` BEFORE the click. If click handler toggles and cursor stays on target, mouseenter is still true and adds `preview`. The combined state may be wrong. Workarounds: use native `h.click()` from `browser_console`, sleep after `browser_click` before reading.

8. **The Google Fonts cross-origin stylesheet returns "Cannot access rules"** when read via `document.styleSheets`. This is browser security, not a CSS parse error. The local stylesheet still works — just don't try to access the cross-origin one.

---

## How to run / deploy

The player is a single static HTML file. For local development with sample loading:

```bash
cd "/Users/boon/Ambient Gen"
python3 -m http.server 8000
```

Then open `http://127.0.0.1:8000/outputs/generative-player.html`.

**For GitHub Pages deployment:**
- The file works on GitHub Pages with no build step
- HTTPS serves the samples without `file://` CORS issues
- The local sample pack (`./samples/`) is used when accessible
- The CDN (`cdn.jsdelivr.net/gh/nbrosowsky/tonejs-instruments/`) is the fallback for missing samples

**Why a server is needed for Sea Salt specifically:**
- Sea Salt uses `vsco2-piano` which is a large 7MB+ piano sample pack
- The local pack is at `./samples/vsco2-piano-mf/`
- `file://` blocks `fetch()` for security, so the local pack can't load
- The CDN fallback for the piano is unreliable (jsDelivr 403s for large files)
- Solution: run an HTTP server so local fetch works

The other pieces (Drift, Bloom, Chimes, Circuit, Raga, Eno Machine) work without a server because they use the `tonejs-instruments` library from a reliable CDN.

---

## File structure

```
Ambient Gen/
├── outputs/
│   ├── generative-player.html  (the working file, 178KB)
│   ├── generative-player.html.before-any-edit  (pre-redesign backup, 133KB)
│   └── samples/
│       ├── vsco2-glock/  (6 .ogg files, ~1MB)
│       └── vsco2-piano-mf/  (12 .ogg files, ~7MB)
└── REDESIGN_NOTES.md  (this file)
```

---

## Test status (as of this writing)

- ✓ Title renders correctly at all sizes (255px max desktop, scales down with clamp)
- ✓ Tags render compact, fit on one row
- ✓ Dots render compact, properly spaced
- ✓ Bottom pill floats with breathing room
- ✓ Drawer slides up smoothly (no bouncy overshoot)
- ✓ Pill hidden when drawer is open (no gap on viewport edges)
- ✓ Safe zone consistent across all surfaces
- ⚠ Sea Salt requires local server (vsco2-piano pack, file:// blocks fetch)
- ⚠ Tide cello not yet confirmed playing — status shows "sampled instruments" loaded, but need to verify the swell actually fires
- ⚠ Other pieces use tonejs-instruments CDN — should work without server but not tested in this session
