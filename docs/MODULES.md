# MODULES — port-registry v26 fork-control surface

The Fork-Control sidebar group exposes five modules. Each is the
operator's selection from the Round-2 exploration variants
(A1/B3/C1/D1/E2). This file records the spec, the rationale for the
variant chosen, and the WCAG/accessibility notes per module.

## Sidebar mapping

| Sidebar item | View id | Module | Variant |
|---|---|---|---|
| Forks | `view-forks` | A · Activity Ribbon | **A1** — 24h pattern per fork as stripes |
| Provisioning | `view-provisioning` | B · Stream Theater | **B3** — ETA timer + live log feed |
| Agents | `view-agents` | C · Status Stripe | **C1** — 4&#8239;px coloured edge per agent card |
| Missions | overlay (`#kanbanOverlay`) | D · Kanban Fullscreen | **D1** — Done/Now/Next, scroll-snap mobile |
| API Keys | `view-api-keys` | E · Spend Bars | **E2** — animated burn-rate ranking |

The default A1/B3/C1/D1/E2 is the recommendation from Round-2. If
`human_inbox/operator-choice-modules.md` exists with a different
combination, that file overrides; this build did not find such a
file and applied the default.

---

## A1 — Activity Ribbon

**Goal.** A glance-readable summary of *when* each fork is busy.
Show 24 stripes per fork (one per UTC hour); intensity (opacity)
encodes activity volume; the current hour pulses to indicate
"happening now".

**DOM.** `.activity-board > .ribbon (one per fork) > .ribbon-strip
(grid of 24 .ribbon-cell) + .ribbon-axis (24 hour labels)`.

**Why this variant.** A2 was a continuous heat-curve and A3 a
sparkline; both lose the discrete-hour meaning that makes
"midnight quiet, morning ramp" legible at a glance. A1 keeps
hourly granularity in the smallest visual surface.

**Per-fork colour.** TaxonAir uses magenta, BOOOMSTA lavender,
AATP-OS teal — three distinct hues so a side-by-side glance
disambiguates the rows without reading the labels. The pulsing
"now" cell switches to lime regardless of fork colour so it
always reads as "live" against the dim baseline.

**Interaction.** Hover the row → all 24 cells brighten 18%
(`filter: brightness(1.18)`). The current-hour cell pulses
between brightness 1 and 1.6 with a 1.6&#8239;s ease-in-out
loop and a soft `box-shadow` halo for emphasis.

**Accessibility.** Each cell carries a `title` attribute with the
hour label and percentage intensity (`title="14:00 — intensity
88%"`) so screen readers and keyboard users can scrub. The pulse
animation is gated by `prefers-reduced-motion: reduce`.

---

## B3 — Stream Theater

**Goal.** Communicate provisioning progress without the operator
needing to tail a log file. Two parallel modes: *headline* (ETA
countdown, large mono digits) and *trace* (live log feed).

**DOM.** `.theater > .eta-card + .stream-card`. The card pair is
side-by-side ≥ 900&#8239;px; stacked vertically below.

**Why this variant.** B1 was a single progress bar (no
trace), B2 was just the log (no ETA). B3 keeps both because they
encode different questions ("how long?" vs "what's happening
now?") and operators consult both during a provisioning incident.

**ETA dynamics.** The countdown shifts colour as time runs out:
- ≥ 60&#8239;s remaining → lime
- 30..59&#8239;s → amber
- < 30&#8239;s → rose

The progress bar fills a magenta→lavender gradient. The total
ETA budget is 240&#8239;s in the mock; the script exposes
`etaTotal` for tests.

**Log feed dynamics.** New log lines cascade in via a `fade-up`
keyframe — each line gets a staggered `animation-delay` of
`index × 36ms` so the feed appears to type itself. Once the
initial 14-line block is rendered, the script appends 3
post-build lines on a 4.2&#8239;s interval to mimic a real
streaming source.

**Accessibility.** Log lines are real DOM elements (not
canvas-rendered), so screen readers can read them. The pulse-dot
in the header is decorative (no `aria-live`); the feed scrolls
visibly so sighted operators see new lines, and a future v0.2.0
will add `aria-live="polite"` for non-sighted operators.

---

## C1 — Status Stripe

**Goal.** A grid of agent cards where the agent's *state* is
encoded as a 4&#8239;px coloured edge on the left side of each
card. Hover widens the edge to 6&#8239;px and the card slides
4&#8239;px right.

**DOM.** `.agents-grid > .agent (one per agent) > .agent-name +
.agent-fork + .agent-state + .agent-task + .agent-foot`. The
edge is rendered via `.agent::before` so the underlying card
geometry stays a single polygon clip.

**Why this variant.** C2 was a coloured chip below the name (more
clutter, less peripheral readability); C3 was a coloured ring
around the avatar (no avatars in v26). The edge stripe scans well
across a 3- or 4-column grid because the eye picks up vertical
colour bands at the periphery.

**State → colour.**
| State | Colour | Meaning |
|---|---|---|
| `running` | lime | Agent actively making progress |
| `idle` | amber | Awaiting next mission |
| `stalled` | rose | Blocked, escalated |
| `provisioning` | lavender (was magenta — see WCAG note) | Being spun up |
| `done` | teal | Sprint closed, awaiting handoff |

**WCAG note.** The original v25 mock used `--magenta` for
provisioning state text on `.agent-state`. Magenta-on-ink-card
is 3.82:1 — fails AAA at 12&#8239;px small text. v26 substitutes
lavender (`#A49CF2`, 7.53:1, AAA). The magenta token itself is
unchanged; it stays in use as a background colour (sidebar
active marker, brand-mark, ETA gradient, kanban-snap dot).

The `--paper-soft` and `--rose` tokens were also lifted from
their v25 values for the same reason — see `DESIGN_TOKENS.md`.

---

## D1 — Kanban Fullscreen

**Goal.** Mission Done/Now/Next at full-viewport scale so the
operator sees the entire pipeline without the sidebar/header
chrome. The default Missions view is intentionally sparse — a
3-stat summary plus the "Open Kanban" button — because the
useful surface IS the fullscreen.

**DOM.** `.kanban-overlay (z-index 1000)` with three columns,
each rendered from `SPRINTS.{done,now,next}`. Toggled via
`.is-open`; backdrop `rgba(7,6,13,0.92)` + `backdrop-filter:
blur(12px)`.

**Why this variant.** D2 was an inline kanban (always visible,
crowded); D3 was a popover (too small for the data volume).
Fullscreen + Esc-to-close gives the kanban its full real estate
when actually needed and keeps the rest of the dashboard
uncluttered when not.

**Drag-feel.** `:active` on a card applies `transform: rotate(2deg)
scale(1.02)` — the same micro-tilt that makes Trello / Linear
cards feel "lifted" without v26 actually implementing drag-and-
drop yet (v0.2.0 will).

**Mobile.** Below 720&#8239;px, the three columns become a
horizontal scroll-snap-x mandatory carousel with one column
visible at 88% width. A snap-dot indicator below the columns
fills the active dot in magenta and stretches it 2.4× on the
x-axis — a single visual that encodes both "which column" and
"how far across".

**Open paths.**
- Click "Open Kanban" button on the Missions view
- `⌘⇧K` / `Ctrl⇧K` keyboard shortcut

**Close paths.**
- Click the "Close · Esc" button
- Press `Esc`
- Click anywhere on the backdrop outside the columns

**Body scroll lock.** While the overlay is open, `document.body.
style.overflow = 'hidden'` prevents the underlying view from
scrolling — standard modal behaviour.

---

## E2 — Spend Bars

**Goal.** Today's API-key burn-rate by fork, ranked descending,
animated on view-shown. Each fork gets one row: name + env on
the left, animated bar in the middle, dollar total on the
right.

**DOM.** `.spend-board > .spend-row (one per fork) > .spend-name
+ .bar-track > .bar-fill + .spend-amount`.

**Why this variant.** E1 was a stacked-area chart over time
(useful but chart-heavy); E3 was a sparkline per provider (needed
more screen). E2 sacrifices the time-axis for clarity at a
single moment — "right now, who's burning the most?" — and
anchors the secondary providers list below.

**Animation.** The fill uses `transform: scaleX(0)` with
`transform-origin: left center` and toggles to `scaleX(1)` once
the view becomes active. JS staggers the toggles by `80ms +
index × 90ms` so the bars unroll in rank order.

**Colour by intensity.** Bar gradient shifts based on percentage
relative to the max:
- > 75% of max → rose → magenta-2 (heaviest spender)
- 40..75%     → amber → lavender (mid)
- < 40%       → lime → lavender (light)

**Provider list.** Below the bars, a `repeat(auto-fill, minmax
(220px, 1fr))` grid of providers with state (`active`,
`ratelimit`, `disabled`). Mock data covers Anthropic, OpenAI,
Google, Hetzner, Netcup, Stripe.

---

## Cross-cutting acceptance

- **Single-file.** Everything in `port-registry-v26.html`. No
  external CSS, JS, or font requests. System-font fallback
  `Inter`/`JetBrains Mono` declared but not loaded from a CDN.
- **No browser storage.** No `localStorage`, `sessionStorage`,
  `IndexedDB`, or cookies. Mock data is JS constants
  (`FORKS`, `AGENTS`, `SPRINTS`, `ACTIVITY_24H`, `PROVIDERS`).
- **No external API calls.** No `fetch`, `XMLHttpRequest`, or
  `WebSocket`. The "live" log feed is a `setInterval` over a
  static array.
- **WCAG AAA.** All paper / paper-mute body text passes 7:1.
  paper-soft is lifted to `#B0AAC8` to pass on ink-card. State
  colours (rose, lime, amber, teal, lavender) all pass 7:1 on
  ink-card after the rose lift. Magenta is no longer used as
  text colour anywhere — only as a background.
- **`prefers-reduced-motion`.** Every keyframe animation and
  transition collapses to ≤ 1&#8239;ms. Pulse dots stay solid
  rather than blink.
- **Mobile.** Tested layout breakpoint at 720&#8239;px. Sidebar
  collapses to a `.menu-toggle` button; theater stacks; kanban
  becomes a scroll-snap carousel.
