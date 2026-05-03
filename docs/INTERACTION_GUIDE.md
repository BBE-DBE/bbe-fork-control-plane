# INTERACTION_GUIDE — when each animation triggers in v26

Every animation in `port-registry-v26.html` is listed below with
its trigger, target, timing, and the `prefers-reduced-motion`
fallback. Use this file when changing motion behaviour — bypass
the per-component CSS and update the token first if the change is
global.

---

## Sidebar nav

| Trigger | Target | Effect | Timing |
|---|---|---|---|
| `:hover` on `.nav-item` | `color`, `background` | Brightens text + reveals card-tone background | `120ms ease` |
| `.is-active` toggle | `::before` left bar | Magenta vertical stripe slides in | `120ms ease` |
| `:focus-visible` on any nav button | global `outline` | Lime 2&#8239;px outline + 2&#8239;px offset | instant |

**Reduced motion:** `transition-duration` collapses to 1&#8239;ms;
the visual effect still applies, just no easing.

---

## A1 — Activity Ribbon

| Trigger | Target | Effect | Timing |
|---|---|---|---|
| Page load (or view-switch to Forks) | `.ribbon-strip > .ribbon-cell` | Cells render at their per-hour intensity opacity | instant |
| Per-frame loop | `.ribbon-cell.is-now` | Pulses brightness 1 ↔ 1.6 with lime box-shadow halo | `1600ms ease-in-out infinite` |
| `:hover` on `.ribbon` | All `.ribbon-cell` in the row | `filter: brightness(1.18)` plus a card background lift to `#1A1727` | `220ms ease` |

**Reduced motion:** the `is-now` keyframe is disabled; the cell
stays at brightness 1 (still visually distinct via `--lime`
colour vs. the default fork hue, plus its position in the strip).

---

## B3 — Stream Theater

| Trigger | Target | Effect | Timing |
|---|---|---|---|
| View-switch to Provisioning | `#streamFeed` | Renders 14 mock log lines, each with `animation-delay: index × 36ms` | `420ms ease` per line |
| `setInterval` (4.2&#8239;s, while view active) | `#streamFeed` | Appends 1 streaming line per tick (3 total) until exhausted | `420ms ease` per line |
| `setInterval` (1&#8239;s, while view active) | `#etaTime`, `#etaFill` | Decrements ETA, updates colour band, grows progress fill from left | `420ms ease` |
| Always | `.stream-dot` | Pulses opacity 1 ↔ 0.3 | `1400ms ease-in-out infinite` |

**ETA colour bands:**
- ≥ 60s: `--lime`
- 30..59s: `--amber`
- < 30s: `--rose`

**Reduced motion:** log lines render instantly (no fade-up); the
stream dot is solid; ETA decrements without colour-fade
transition.

---

## C1 — Status Stripe

| Trigger | Target | Effect | Timing |
|---|---|---|---|
| `:hover` on `.agent` | `transform: translateX(4px)`, background to `#1A1727` | Card slides right and lifts | `220ms ease-out` |
| `:hover` on `.agent` | `::before` width | Stripe widens from 4px to 6px | `220ms ease-out` |
| `:focus-visible` on `.agent` | global `outline` | Lime 2&#8239;px outline + 2&#8239;px offset | instant |

**Reduced motion:** the slide collapses to 1&#8239;ms; the stripe
still widens (a width transition is allowed because it doesn't
imply movement of the *card*, only a static state change of the
edge).

---

## D1 — Kanban Fullscreen

| Trigger | Target | Effect | Timing |
|---|---|---|---|
| Click `#openKanban` OR `⌘⇧K` | `.kanban-overlay` | `.is-open` toggles, body scroll locked, `kanban-close` focused | instant (no fade) |
| Click `.kanban-close` OR `Esc` OR backdrop click | `.kanban-overlay` | Removes `.is-open`, restores body scroll | instant |
| `:hover` on `.k-card` | `background` to `#1A1727` | Card lift | `220ms ease` |
| `:active` on `.k-card` | `transform: rotate(2deg) scale(1.02)`, `cursor: grabbing` | Drag-feel for tactile feedback | instant |
| Mobile horizontal scroll | `.snap-dot` | Active dot becomes magenta + `transform: scaleX(2.4)` | `120ms ease` |

The overlay deliberately appears *instantly* rather than fading
in — the operator pressed a button and expects the surface to
arrive immediately. Adding fade-in would feel laggy. The
backdrop blur is the visual cushion.

**Reduced motion:** all transitions clamp; the active drag-feel
rotation collapses to no rotation but the cursor swap still
triggers (semantic, not motion).

---

## E2 — Spend Bars

| Trigger | Target | Effect | Timing |
|---|---|---|---|
| View-switch to API Keys | `.bar-fill.is-shown` | Bars unroll from `transform: scaleX(0)` to `scaleX(1)` with `transform-origin: left center`, staggered by index | `420ms ease-out`, stagger `80ms + index × 90ms` |
| `:hover` on `.provider` | `background` to `#1A1727` | Card lift | `120ms ease` |

**Reduced motion:** bars appear at full width instantly (no roll).

---

## Sub-system: keyboard

| Key | Effect |
|---|---|
| `Tab` / `Shift+Tab` | Standard focus traversal. All buttons + cards are tabbable. |
| `Esc` | Close kanban overlay if open; otherwise no-op. |
| `⌘⇧K` / `Ctrl⇧K` | Switch to Missions view AND open the kanban overlay. |
| `Enter` / `Space` on focused `.nav-item` | Activate view (HTML button default behaviour). |

---

## Sub-system: mobile (≤ 720&#8239;px)

| Trigger | Effect |
|---|---|
| Page load | Sidebar collapsed; `.menu-toggle` button visible top-left. |
| Tap `.menu-toggle` | Sidebar slides in from left (`transform: translateX(0)`, `220ms ease`). |
| Tap any `.nav-item` while sidebar open | View activates AND sidebar closes. |
| Horizontal scroll on `.kanban-board` | `scroll-snap-type: x mandatory` snaps to one column at a time; `.snap-dot` indicator updates. |

---

## When to add a new animation

Three rules:

1. **Map to a state change.** Animations explain *what just
   happened*. Don't add motion for decoration alone.
2. **Use a token.** `--dur-fast`, `--dur-base`, or `--dur-slow`,
   plus `--ease` or `--ease-out`. Don't hardcode timings.
3. **Add a reduced-motion fallback.** Either via the global
   `@media (prefers-reduced-motion)` block (which clamps all
   `transition-duration` and `animation-duration` to 1&#8239;ms),
   or via an explicit override if the animation is critical
   semantically (in which case opt out of the global clamp).

The pulse dot in B3, the "is-now" pulse in A1, and the bar-roll
in E2 each have explicit reduced-motion handling because their
default behaviours are not just decorative.
