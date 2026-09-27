# CLAUDE.md: SURROUND waitlist site

This file is for Claude Code. Read it before making changes.

## What this is
This is a one-page waitlist site for **SURROUND**, a one-off underground Drum & Bass party in Sydney. The look is a CRT phosphor oscilloscope/terminal: cyan-on-black, VCR OSD Mono, ASCII art and scanlines.

It's a **static site** with no build step, so push it to GitHub and it deploys as-is (GitHub Pages or Netlify).

## Files
- `index.html`: the whole site (markup, text and logic)
- `support.js`: a small runtime that renders `index.html`. **Never edit this file.** It loads React 18 from unpkg.
- `assets/gunfinger.png`: the source image for the ASCII hand. A copy is embedded in `index.html` as `HAND_DATA` (base64), so the site works even without the assets folder. To swap the hand, replace `HAND_DATA` or point `HAND_SRC.src` at a file.
- `assets/logos/gunfinger-logo-*.svg`: logo exports for socials (not used on the page)

## How index.html is structured
There are two parts:

1. **Template** (between `<x-dc>` and `</x-dc>`, lines ~11–275): HTML with **inline styles only**.
   - `{{ name }}` holes are filled by `renderVals()` in the logic class. Holes are dotted lookups only; never put expressions in them.
   - `<sc-if value="{{ x }}">` and `<sc-for list="{{ items }}" as="item">` handle conditionals and loops.
   - `ref="{{ someRef }}"` attaches refs created in the constructor.
   - `class` and `style` work like normal HTML. `style-hover="…"` sets the hover style.
   - `<helmet>` at the top holds the font link, `@keyframes`, body resets, and the SVG lens-warp filter.

2. **Logic** (`<script type="text/x-dc" data-dc-script data-props="…">`, from ~line 482): a class `Component extends DCLogic`. It works like a React class component without `render()`; `renderVals()` returns everything the template needs.

**Settings** live in the `data-props` JSON on that script tag. Each prop's `"default"` is the live value. To change hand size, glow and so on, edit the default there. It's HTML-escaped, so `&quot;` means `"`.

| Prop | Default | Controls |
|---|---|---|
| `handSize` | 0.95 | Hero hand scale (desktop) |
| `handX` / `handY` | -2 / -7 | Hand offset, % of free area |
| `heroTextX` / `heroTextY` | 0 / 0 | Nudge the hero text block (px) |
| `mobileHandLayout` | "above" | Phone: hand `above`, `below` or `background` |
| `mobileHandSize` | 1 | Phone hand scale |
| `glow` | 0.7 | CRT glow strength everywhere |
| `lensWarp` | true | Subtle wide-angle edge warp (desktop ≥1100px only) |
| `scanlines` | true | Scanline overlay |
| `preloader` / `introEveryLoad` | true / false | Loading screen; once per session unless introEveryLoad |
| `customCursor` | true | Blinking block mouse cursor with X/Y readout (desktop mice only) |
| `radioStreamUrl` | "" | Real stream URL for the radio toggle. Empty means the built-in 174 BPM synth. |
| `queueBase` | 200 | Starting waitlist queue number |
| `waitlistFull` | false | FOMO mode: sign-ups get a "WAITLIST FULL" pop-up and go on the overflow list (sent with `list: "overflow"`); status shows WAITLIST FULL |
| `screenBlue` | #040805 | Background colour |

## Page sections (template, in order)
- **Boot / preloader** (top of the template): the bouncing ASCII hand and 000→174 counter. Messages: "Calibrating low-end", "Loading doubles", "Reloading gunfingers", "Preparing frog lasers", "Sub bass detected", then "Loading complete".
- **Hero** (`data-screen-label="Hero"`, ~line 55): the header row, the H1 `SURROUND` (scrambles on hover), the highlighted subhead "The filthiest Drum & Bass party Sydney has ever seen. One night only.", the terminal signup, and the ASCII hand canvas.
- **Events / INFO** (`data-screen-label="Events"`, ~line 142): the centred highlighted `EVENT INFO` header, then the `EVENT_001.EXE` window with the #001 details, "STATUS: WAITLIST ONLINE" with a green blinker, DATE/LOCATION/LINEUP set to [REDACTED], the `> RESERVE YOUR SPOT` button, the globe, and the foldable data panels (graph and data console).
- **About** (`data-screen-label="About"`, ~line 221): two `>` lines with a blinking block cursor.
- **Footer** (`data-screen-label="Footer"`, ~line 230): a `CONTACT` tag and the links `> INSTAGRAM`, `> TIKTOK`, `> SOUNDCLOUD`, `> EMAIL`, plus the © line.

## Key logic (method names, so you can search for them)
- `drawHero(t)`: ASCII hand layout, cursor-heat glitch and float. It uses `buildGrid`, `buildFromImage` and `renderGrid` (top of the script).
- `drawGlobe(t)` / `drawGlobeText`: a 19s globe loop. It spins to Australia, targets Sydney, zooms in with the analysis, zooms out, then makes one full rotation. It has phosphor trails and a faint scope grid, and country outlines load from jsdelivr world-atlas.
- `tickGraph(now)`: the jagged 3-line data graph.
- `initBoot` / `drawBoot` / `exitBoot`: the preloader and the CRT switch-off exit.
- `startRadio` / `stopRadio` / `startSynth`: the radio toggle (Web Audio).
- `redactGlitch()`: an occasional scramble on [REDACTED] tags, drawn as an overlay so the layout never moves.
- `buildLens` / `syncLens`: the lens warp SVG filter.
- `submit` / `finishSignup(handle)`: the signup flow (email → name → handle). **Signups are currently only saved to the visitor's localStorage.** To collect them, add a `fetch` POST in `finishSignup`, for example to Formspree (`https://formspree.io/f/XXXX`) or a Google Apps Script web app.
- `frame(now)`: the main animation loop, about 30fps on desktop and 20fps on phones, paused when the tab is hidden.

## Design rules (keep these)
- **Font:** VCR OSD Mono (loaded from fonts.cdnfonts.com), everything **UPPERCASE**.
- **Colours:** background `#040805`; phosphor cyan `#7FE9F0` for all text, lines and highlight blocks; hover on solid buttons `#F2FFFF` with a stronger glow; the waitlist blinker is green; the loader hand cycles `#7FE9F0` / `#C9FFF9` / `#F2FFFF` (no blue).
- **[REDACTED] rule:** any text reading [REDACTED] is always highlighted with a white background, dark text (`color: var(--bsod)`) and `padding: 0 .3em`.
- **Highlight blocks** (INFO, ABOUT, CONTACT, the subhead, form labels): cyan background, dark text, with a glow `box-shadow`.
- Glow scales with `var(--g)`, which is set from the `glow` prop.
- Styles are **inline**. Don't add CSS classes or stylesheets; only `@keyframes`, `@font-face` and resets go in `<helmet><style>`.
- Mobile breakpoint is 640px (`this.state.mobile`). Test at 390px and 1440px wide.

## Workflow
- Preview locally with `npx serve .` (or any static server), then open http://localhost:3000. Opening the file directly may block the hand image.
- Keep each change small, check it in the browser, then commit and push to `main`.
