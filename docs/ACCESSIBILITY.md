# ACCESSIBILITY — port-registry v26 v0.2.0

This document records every accessibility decision in the v26
codebase, what it satisfies (WCAG 2.1 / 2.2 success criterion),
and what's deferred.

## Compliance summary

| Standard | Level | Status |
|---|---|---|
| WCAG 2.1 | AA | All criteria addressed |
| WCAG 2.1 | AAA | Contrast, focus visible, keyboard navigation passes; AAA *Reading Level* and *Contextual Help* not applicable to this surface |
| ARIA Authoring Practices Guide | — | Modal dialog, status, log, tablist patterns followed |
| `prefers-reduced-motion` | — | Every keyframe & transition clamped to ≤ 1ms |

## Per-criterion notes

### 1.4.6 Contrast (AAA)
- Tested 11 critical foreground/background pairs via Python
  WCAG-luminance script; all ≥ 7:1.
- `--paper-soft` lifted from v25 `#8B86A0` (5.25:1) → v26
  `#B0AAC8` (8.24:1) so 12&#8239;px mono labels clear AAA.
- `--rose` lifted from v25 `#FF6B8A` (6.75:1) → v26 `#FF8B9F`
  (8.25:1) so stalled-state badges clear AAA.
- `--magenta` is restricted to background-only roles. Magenta
  as text fails AAA; the design system token is unchanged but
  text usage was reassigned to lavender. Documented in
  `DESIGN_TOKENS.md`.

### 1.4.13 Content on Hover or Focus (AA)
- Tooltips delivered via `title` attribute on activity-ribbon
  cells (intensity readout). `title` content is also conveyed
  visually via cell opacity, so no information is hover-only.

### 2.1.1 Keyboard (A)
Every interactive element is keyboard-reachable:
- Sidebar nav: `<button>` elements, native focus.
- Kanban cards: `tabindex="0"` + `role="article"` plus
  draggable, with full keyboard-DnD support (Space/arrows/Esc).
- Buttons (`btn`, `kanban-close`, `helpClose`, `menuToggle`):
  native `<button>`.
- View tabs: `<button>` semantics; activated via Enter/Space.

### 2.1.2 No Keyboard Trap (A)
- The Kanban overlay implements an explicit focus-trap (Tab
  cycles within), but Esc and the Close button always release
  the trap. Same for the Help overlay.
- The mobile sidebar is auto-closed on view-switch so it can't
  trap touch users either.

### 2.4.1 Bypass Blocks (A)
- Skip-link "Skip to main content" rendered at the top of
  `<body>`. Hidden via `transform: translateY(-200%)` until
  focused; visible at top-left when keyboard-focused. Targets
  `#main` which has `tabindex="-1"` for a focusable target.

### 2.4.3 Focus Order (A)
- Tab order traverses skip-link → menu-toggle → sidebar nav
  (top-to-bottom) → main-content (current view's interactive
  elements top-to-bottom). The kanban overlay, when open,
  scopes focus order to its own contents via the focus trap.

### 2.4.6 Headings and Labels (AA)
- Every view has an `<h1>` reflecting the sidebar item.
- Every kanban column has an `<h3>` with a coloured badge.
- Every interactive control has an accessible name via
  visible text, `aria-label`, or `aria-labelledby`.

### 2.4.7 Focus Visible (AA)
- `:focus-visible { outline: 2px solid var(--lime);
  outline-offset: 2px; }` applied globally. Skip-link,
  buttons, cards, links all show the lime focus ring.

### 2.5.7 Dragging Movements (AA, WCAG 2.2)
- Every drag operation has a single-pointer alternative:
  - Mouse / touch: drag-and-drop
  - Keyboard: Space lift + arrow keys + Space drop
- The keyboard alternative is announced in the kanban cards'
  `aria-label` and in the Help overlay (`?` key).

### 3.3.1 Error Identification (A)
- The visible warn/error toasts (rose / amber border) plus
  the `srAnnouncer` ARIA-live="assertive" element together
  surface state changes (stalls, failed drops) to both
  sighted and screen-reader users.

### 4.1.2 Name, Role, Value (A)
- `role="dialog" aria-modal="true"` on both overlays
  (Kanban + Help).
- `role="application"` on the kanban board so screen readers
  let arrow keys through to the keyboard-DnD handler instead
  of intercepting them for browse mode.
- `role="log" aria-live="polite"` on the provisioning stream
  feed.
- `role="article"` + `aria-grabbed` on kanban cards.
- `aria-current="page"` on the active sidebar item.
- `aria-expanded` on the mobile menu-toggle.

### 4.1.3 Status Messages (AA)
- `#srAnnouncer` (`aria-live="assertive"`) announces:
  - Agent stall events
  - Agent recovery events
  - Drop confirmations (mouse / touch / keyboard)
  - Keyboard-DnD lift / drop / cancel
- `#toastRegion` (`aria-live="polite"`) carries the
  visible-toast text so the same content reaches AT users
  who don't see the visual toast.

## What's NOT in v0.2.0 yet

- **Voice control**. Reasonable assumption since this is a
  desktop dashboard, but `data-voice-target` annotations are
  not present. Future v0.4.0.
- **High-contrast mode (Windows)**. The token system uses CSS
  variables and would adapt under `forced-colors: active` if
  we added one targeted rule (e.g. `outline-color: highlight`
  for focus). Not done in v0.2.0.
- **`aria-describedby` linking validation errors to inputs**.
  No form inputs in v26 yet; relevant when settings persistence
  ships.
- **Internationalisation**. en-only. Direction is not
  configurable (no RTL test). v0.3.0 will add `lang` switching
  + RTL audit.

## Verification commands

```bash
# Static contrast verification (Python WCAG script):
python3 contrast-check.py    # see the inline script in CHANGELOG

# JS syntax check:
awk '/^<script>/,/^<\/script>/' port-registry-v26.html \
  | sed '1d;$d' > /tmp/v26.js && node --check /tmp/v26.js

# HTML structural parse:
python3 -c "from html.parser import HTMLParser; HTMLParser().feed(open('port-registry-v26.html').read())"
```

What can ONLY be verified with an actual browser:

```bash
# (run on a workstation with Chrome installed)
lighthouse port-registry-v26.html --view --quiet \
  --chrome-flags="--headless --no-sandbox" \
  --only-categories=performance,accessibility,seo

# (manual VoiceOver / NVDA test pass)
# (manual mobile emulation: iPhone 13, Pixel 7)
```

The build host running v0.2.0 had neither Chromium nor
Lighthouse installed, so the numeric Lighthouse score is not
in this release's RESULT. Static analysis covers what static
analysis can cover; the operator's browser is the final
judge.
