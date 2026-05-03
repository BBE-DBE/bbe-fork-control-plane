# Changelog

All notable changes to bbe-fork-control-plane are documented here.
Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
SemVer: [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.0] — 2026-05-03 (final, supersedes draft v0.2.0)

Live Kanban + Accessibility Pass. Three substantial features
graduate from v0.1.0 `remaining_risks` into shipped capability,
plus an operator-choice surface and a polish pass — and on top
of that: every Codex audit finding (Critical, High, Medium) from
T-AUDIT-V26-UI resolved before this tag is final.

> **Note on the tag.** A draft v0.2.0 was pushed earlier today
> (commit `fbf20b9`) without Codex's audit findings integrated.
> The final v0.2.0 tag is repointed to the post-audit commit
> below. Anyone who pinned the early draft sha should re-pull.

### Codex audit findings resolved

- **CODEX-AUDIT-V26-CRITICAL**: `.btn.btn-primary` text contrast
  was 4.20:1 (`--ink` on `--magenta`) — fails WCAG AAA. Fixed by
  switching the primary-action button to `--ink` on `--lime`
  (16.64:1, AAA) plus a darker-lime hover (`#B0EA45`, 14.12:1,
  AAA). Lime is already the action/success colour in the design
  system, so the swap also tightens semantic consistency.
- **CODEX-AUDIT-V26-HIGH** (mock-data #1): `FORKS.taxonair.agents`
  said 5 but the `AGENTS` array carried 6 TaxonAir agents
  (`a1`,`a2`,`a3`,`a4`,`a5`,`a12`). Bumped to 6. `AGENTS` totals
  now per-fork: 6/4/2 = 12, matching `FORKS.agents`.
- **CODEX-AUDIT-V26-HIGH** (mock-data #2): `SPRINTS.{done,now,next}`
  arrays held 6/5/12 entries but the missions summary displayed
  23/5/47 because of `+17` and `+35` fudge factors in the
  renderer. Sidebar Missions meta showed 70. Removed the
  fudge across all three surfaces; UI now reflects real array
  sizes.
- **CODEX-AUDIT-V26-HIGH** (focus-trap): the Kanban + Help
  overlays already had `trapFocus()` but didn't track the
  trigger element, so closing the overlay sent focus to body
  instead of back to the opener. Added `kanbanReturnFocus` /
  `helpReturnFocus` capture on open + restore on close.
- **CODEX-AUDIT-V26-MEDIUM** (A1 ribbon non-text encoding):
  intensity was conveyed via opacity/colour only, failing WCAG
  1.4.1 "Use of Color". Each ribbon now carries a textual
  summary line (`avg X% · peak HH:00 (Y%)`) plus a per-cell
  `aria-label` with the hour, intensity, and `peak`/`now`
  badges. The peak cell also gets a thin paper outline so it's
  visually distinguishable beyond opacity.
- **CODEX-AUDIT-V26-MEDIUM** (reduced-motion gap): the global
  `@media (prefers-reduced-motion: reduce)` block clamped
  transitions but missed STATIC `:active` transforms. Added
  explicit overrides:
  ```
  .k-card:active           { transform: none !important; }
  .k-card.is-touch-dragging { transform: none !important; }
  ```
  Drag-rotate (the v0.1.0 `rotate(2deg) scale(1.02)` trick) now
  truly respects user preference.

### Added

- **Real drag-and-drop on the Kanban board** (replaces the v0.1.0
  visual-only `:active` rotate-feel). Three input modes:
  - **Mouse / desktop** — HTML5 DnD API. `dragstart` / `dragover`
    / `drop` / `dragend`. Drop-zones get a lime tint when a card
    enters; the column under the cursor gets a stronger lime
    dashed outline.
  - **Touch / mobile** — long-press (220&#8239;ms) lifts the card
    into a fixed-position floating clone that follows the finger;
    `touchend` resolves to the column under the finger via
    `document.elementFromPoint`.
  - **Keyboard / a11y** — `Space` lifts the focused card,
    `←` / `→` move it across columns, `↑` / `↓` reorder within
    the column, `Space` / `Enter` drops, `Esc` cancels the lift.
  - State updates `SPRINTS` in-place (no server). The DOM
    re-renders and a **FLIP** transition (320&#8239;ms ease-out)
    animates the card from its old position to its new one;
    skipped under `prefers-reduced-motion`.
- **`aria-live` + accessibility pass.**
  - Skip-link "Skip to main content" added at the top of `<body>`,
    visible only on keyboard focus, jumps to `#main`.
  - `<main>` carries `id="main"` and `tabindex="-1"` so the skip
    target is focusable.
  - Stream feed (B3) carries `role="log" aria-live="polite"`.
  - Hidden `#srAnnouncer` with `aria-live="assertive"` carries
    state-change messages: agent stalls, recoveries, drag-drop
    confirmations, keyboard-DnD lift/drop announcements.
  - Sidebar nav: `aria-current="page"` on the active item;
    `aria-expanded` on the mobile menu-toggle; `aria-haspopup`
    on the Missions item; `aria-label` on the sidebar.
  - Kanban overlay: `role="dialog" aria-modal="true"` plus
    proper focus-trap (Tab cycles within the overlay; backdrop
    click + Esc close).
  - Help-overlay: same focus-trap pattern, `role="dialog"
    aria-modal="true"`.
  - Kanban board: `role="application"` plus per-list
    `aria-label="Done"|"Now"|"Next"`. Each card has a verbose
    `aria-label` describing id, title, fork, column, and the
    keyboard-DnD interaction.
- **Live heartbeat from mock data.**
  - `requestAnimationFrame` loop ticking every 2&#8239;s (not
    `setInterval` — smoother under battery throttling).
  - Pauses cleanly when `document.hidden` (battery save) and
    resumes on visibility change.
  - Each tick: pulses one random agent's status stripe (lime
    flash + 6→8&#8239;px width burst), increments one random
    fork's `spend_today` by $0.01..$0.09 (animated `is-bumped`
    on the spend amount when the API Keys view is active), and
    rolls a 5% probability of flipping a `running` agent to
    `stalled` (toast + `aria-live` announcement). Stalls auto-
    recover after 10&#8239;s.
  - All visual heartbeat effects are gated by
    `prefers-reduced-motion: reduce` — only the numeric updates
    apply when motion is disabled.
- **Operator-choice module switching.**
  - `MODULE_CHOICE = {A:"A1", B:"B3", C:"C1", D:"D1", E:"E2"}`
    baked in as the default.
  - URL override: `?modules=A2,B1,C2,D3,E1`. Slot letters and
    variants validated against `MODULE_VARIANTS` allow-list to
    prevent injection.
  - Sidebar nav items now carry a small mono module-tag
    (`A1`, `B3`, etc.) that flips magenta-on-ink when the item
    is active.
  - New **Settings view** under the System group lists all five
    slots with their variant pills; clicking a pill swaps the
    active variant for that slot (UI-only, no persistence — no
    `localStorage` per the constraint). Non-default variants
    show a toast: "v0.3.0 will implement this variant; default
    renders for now".
- **UI polish.**
  - Sidebar active item gains an animated underline (`::after`
    with `transform: scaleX` keyframe) below the label.
  - Kanban-card hover: precision border-left + subtle lime glow
    (1&#8239;px box-shadow) on top of the existing background
    lift.
  - Toast notifications top-right: lime-bordered for `ok`,
    amber for `warn`, rose for `error`. Slide in from right,
    auto-dismiss 2.4&#8239;s + 0.5&#8239;s slide-out.
  - Help overlay: `?` key toggles a fullscreen cheatsheet with
    every keyboard shortcut. Esc / backdrop / Close button all
    dismiss.
  - Theater (B3) view: tighter portrait-mobile stack
    (`@media (max-width: 720px) and (orientation: portrait)`).

### Changed

- `port-registry-v26.html` grew from 1489 → ~2349 lines.
- `<main>` got an `id="main"` for the skip-link target.
- All sidebar nav items got `data-slot="A".."E"` for module-tag
  binding.

### Soft decisions

1. **MODULE_CHOICE is UI-state only.** Switching variants in the
   Settings view changes the displayed module-tag and surfaces a
   toast, but the actual rendered modules don't swap because
   v0.2.0 ships only the default A1/B3/C1/D1/E2 implementations.
   The other 10 variants (A2/A3/B1/B2/C2/C3/D2/D3/E1/E3) land in
   v0.3.0. This is the same trade-off as v0.1.0, made explicit
   in the UI now.
2. **HTML5 DnD over Pointer Events.** Pointer Events would unify
   mouse + touch behind one event stream, but real-world
   touch-DnD with Pointer Events still needs custom hit-testing
   logic (browser doesn't auto-fire `pointerleave` on the source
   when crossing into a drop-zone). Splitting into HTML5 DnD
   for desktop + dedicated touch handlers gives more reliable
   behaviour today.
3. **No drop-target reordering of cards within the same column
   via mouse DnD.** The mouse-DnD path only resolves to the
   target column, not the card-position-within-column.
   Within-column reordering ships only via keyboard arrow-up/
   down. Mouse path lands the dragged card at the end of the
   target column. v0.3.0 will add same-column reorder for
   mouse.
4. **No Lighthouse score in the RESULT.** No headless Chrome
   on the build host; running Lighthouse would require
   spinning up Chromium and a static server. The constraints
   that Lighthouse audits (single-file, no external requests,
   semantic HTML, ARIA labels, focus management,
   `prefers-reduced-motion`, contrast 7:1) are all verified
   manually + via Python contrast script. Documented as a
   remaining_risk; operator can run Lighthouse on a real
   browser.
5. **No browser visual testing.** No GUI on this build host —
   Chrome/Firefox/Safari rendering parity not verified. Static
   HTML parses cleanly, JS syntax is valid (`node --check`),
   structural counts match expectations. Visual confirmation
   is operator-side.

### Notes

- WCAG AAA still passes — 11/11 critical pairs verified at ≥
  7:1 (paper / paper-mute / paper-soft / rose / lavender / lime
  / amber / teal on ink-card and ink-deep, plus skip-link
  ink-on-lime at 16.64:1 and toast paper-on-ink-card at
  16.31:1).
- Skeleton (b0c33cb) and v0.1.0 (956b829) commits preserved
  in history.
- Codex audit findings: `gh pr list --state all --search
  "T-AUDIT-V26-UI"` returned empty at build time. If audit
  arrives later, v0.2.x or v0.3.0 absorbs.

## [0.1.0] — 2026-05-03

Initial usable release. Replaces the previous skeleton (which had
been blocked on a missing v25_1.html input) with a fresh v26
build using the operator's explicit module selection.

### Added

- `port-registry-v26.html` — single-file dashboard with five
  Fork-Control modules wired to mock data:
  - **A1 Activity Ribbon** — 24h pattern per fork (Forks view)
  - **B3 Stream Theater** — ETA timer + live log feed
    (Provisioning view)
  - **C1 Status Stripe** — 4&#8239;px coloured edge per agent
    card (Agents view)
  - **D1 Kanban Fullscreen** — Done/Now/Next overlay
    (Missions view; opens via button or `⌘⇧K`)
  - **E2 Spend Bars** — animated burn-rate ranking
    (API Keys view)
- `docs/MODULES.md` — module spec + variant rationale + WCAG
  notes per module.
- `docs/DESIGN_TOKENS.md` — sign.it 1.0 token reference. Three
  tokens lifted from v25 values to clear WCAG AAA on
  `--ink-card`: `--paper-soft` `#8B86A0` → `#B0AAC8`,
  `--rose` `#FF6B8A` → `#FF8B9F`. The `--magenta` token is
  restricted to background roles only.
- `docs/INTERACTION_GUIDE.md` — per-component animation triggers
  and `prefers-reduced-motion` fallbacks.
- `README.md` — quickstart + module table + constraint manifest.

### Notes

- **Operator-choice file absent at build time.** The directive
  pointed at `human_inbox/operator-choice-modules.md` as the
  override source; that file was not present, so the recommended
  default `A1/B3/C1/D1/E2` was applied.
- **No external requests of any kind.** No CDN-loaded fonts, no
  Lucide icon library; SVG icons inlined. System-font fallback
  for Inter / JetBrains Mono.
- **No browser storage.** Mock data is in-source JS constants
  (`FORKS`, `AGENTS`, `SPRINTS`, `ACTIVITY_24H`, `PROVIDERS`).
- **Provisioning state colour** (Agents view) was originally
  spec'd as `--magenta` but magenta-on-ink-card is 3.82:1 — fails
  AAA at 12&#8239;px small text. Substituted `--lavender`
  (`#A49CF2`, 7.53:1, AAA). Documented in `docs/MODULES.md`.

## [0.0.0] — 2026-05-03 (skeleton, superseded by 0.1.0)

Empty repo with `LICENSE`, `README` (blocker note), and
`CHANGELOG`. Was blocked on `port-registry-v25_1.html` not being
present on the filesystem; v0.1.0 unblocks by building from
scratch off the operator's explicit module spec.

[0.2.0]: https://github.com/BBE-DBE/bbe-fork-control-plane/releases/tag/v0.2.0
[0.1.0]: https://github.com/BBE-DBE/bbe-fork-control-plane/releases/tag/v0.1.0
