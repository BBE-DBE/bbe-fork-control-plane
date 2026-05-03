# Changelog

All notable changes to bbe-fork-control-plane are documented here.
Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
SemVer: [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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

[0.1.0]: https://github.com/BBE-DBE/bbe-fork-control-plane/releases/tag/v0.1.0
