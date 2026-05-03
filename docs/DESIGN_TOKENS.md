# DESIGN_TOKENS — sign.it 1.0 reference

The complete token sheet rendered in `port-registry-v26.html`.
Edit the `:root` block at the top of `<style>` to tune; never
override per-component (the design system is global on purpose).

## Colours

| Token | Hex | Role | Notes |
|---|---|---|---|
| `--ink` | `#07060D` | Page background, primary surface | Maximum contrast canvas. |
| `--ink-deep` | `#0F0E1A` | Sidebar, kanban columns | One step lighter than ink. |
| `--ink-card` | `#15131F` | Cards, ribbons, agents, spend board | Two steps lighter than ink. |
| `--ink-edge` | `#221F33` | Borders, dividers, dashed rules | Visible outline against ink-card. |
| `--paper` | `#F2F1F5` | Primary text, headings | 17.94:1 on ink — AAA. |
| `--paper-mute` | `#C8C3D8` | Secondary text, descriptions | 11.16:1 on ink-deep — AAA. |
| `--paper-soft` | `#B0AAC8` | Mono labels, axis ticks, meta | 8.24:1 on ink-card — AAA. **Lifted from v25's `#8B86A0` (5.25:1) to clear the AAA bar at 12px small text.** |
| `--magenta` | `#C81EBD` | Brand mark, sidebar active marker, gradient endpoints | **Background only.** Magenta-as-text is 4.20:1 on ink and fails AAA. |
| `--magenta-2` | `#D756CD` | Gradient mid-tones, hover targets | Background only. |
| `--lime` | `#C6FB50` | "Now"-state highlight, running agents, lime gradient | 16.64:1 on ink — AAA. |
| `--amber` | `#FFB800` | Idle agents, ETA-warn state | 10.58:1 on ink-card — AAA. |
| `--rose` | `#FF8B9F` | Stalled agents, ETA-late, error log | 8.25:1 on ink-card — AAA. **Lifted from v25's `#FF6B8A` (6.75:1) to clear the AAA bar.** |
| `--lavender` | `#A49CF2` | Provisioning state, kanban Next-column accent | 7.53:1 on ink-card — AAA. |
| `--teal` | `#1FE0E0` | Done state, AATP-OS fork ribbon | 11.18:1 on ink-card — AAA. |

### Accent rule

State colours encode meaning. Lime = running, amber = idle, rose
= stalled, lavender = provisioning, teal = done. Don't reuse
these for decorative purposes — they're load-bearing in the UI.

### Magenta-as-text rule

The `--magenta` token is restricted to **background** roles
(brand-mark fill, sidebar active marker, kanban-snap-dot active,
gradient endpoint in spend bars and ETA progress bar). When a
status would naturally read as magenta (e.g. provisioning), the
token used is `--lavender`, which is the cool-purple member of
the palette that *does* clear AAA.

## Typography

| Property | Value | Notes |
|---|---|---|
| Display family | `Inter, system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif` | System-font fallback chain. Inter is preferred but not loaded from a CDN — `Keine externe API-Calls` rule. |
| Mono family | `'JetBrains Mono', ui-monospace, 'SF Mono', Consolas, 'Liberation Mono', monospace` | Same — JetBrains Mono if installed locally, otherwise modern system mono. |
| Base body size | `16px` | **Never smaller.** WCAG AAA + sign.it 1.0 rule. |
| Mono labels | `12px` minimum | Mono uppercase + letter-spacing 0.06em. |
| Display headings | `30px` for view titles, `26px` for kanban title, `22px` for prompts | font-weight 600. |
| `font-feature-settings` | `'cv11', 'ss01', 'ss03'` | Inter's stylistic sets — terminal-friendly numerals. |

## Geometry

| Token | Value | Use |
|---|---|---|
| `--cut-sm` | `4px` | Buttons, pills, badges, snap-dots |
| `--cut-md` | `8px` | Cards, ribbons, kanban columns, spend board |
| `--cut-lg` | `14px` | Reserved for hero/landing surfaces (not used in v26 yet) |

### Polygon, never radius

Every clipped element uses `clip-path: polygon(...)` with diagonal
corner cuts. `border-radius` is forbidden in sign.it 1.0 — the
diagonal cut is the brand. Two patterns:

```css
/* Two-corner cut (top-left + bottom-right) — the default for cards */
clip-path: polygon(
  var(--cut-md) 0, 100% 0,
  100% calc(100% - var(--cut-md)),
  calc(100% - var(--cut-md)) 100%,
  0 100%, 0 var(--cut-md)
);
```

The single-cut variant (used in v26 throughout) gives a directional
visual rhythm without losing rectilinear surface alignment. A
four-corner cut variant exists for the design system but isn't
needed in v26.

## Motion tokens

| Token | Value | Use |
|---|---|---|
| `--dur-fast` | `120ms` | Hover colour transitions, button press |
| `--dur-base` | `220ms` | Card slide, edge widen, view fade |
| `--dur-slow` | `420ms` | Bar fill, log line cascade, ETA progress |
| `--ease` | `cubic-bezier(0.2, 0.8, 0.2, 1)` | Default ease — quick start, soft settle |
| `--ease-out` | `cubic-bezier(0.16, 1, 0.3, 1)` | Snappy out — spend bars, kanban cards |

All animations collapse to ≤ 1&#8239;ms under
`prefers-reduced-motion: reduce`. Pulse loops (`.ribbon-cell.is-now`,
`.stream-dot`) lock to a static fully-opaque state so the indicator
remains visually present without movement.

## Layout

| Token | Value | Use |
|---|---|---|
| `--sidebar-w` | `260px` | Fixed sidebar width (desktop) |
| `--content-pad` | `32px` | Horizontal padding around `.content` |

Mobile (`<= 720px`):
- Sidebar collapses to a fixed-position panel hidden off-canvas
  to the left; `.menu-toggle` button surfaces it.
- `.theater` (B3) collapses from 280px-fixed-left + flex-right
  to a single column.
- `.kanban-board` becomes a horizontal scroll-snap-x carousel
  with snap-dot indicator.

## What's not in this file

- Component-level overrides. The design system is global; per-
  component CSS lives in `port-registry-v26.html` directly.
- Theme switching. v26 is dark-only; a light variant ships in
  v0.2.0 with paper-on-paper-mute reversed.
- Localisation tokens. v26 is en-only.
