# bbe-fork-control-plane

Single-file HTML/CSS/JS fork-control plane for the BBE-DBE
multi-agent system. Five new dashboard modules (`A1`, `B3`, `C1`,
`D1`, `E2`) wired to mock fork / agent / mission / spend data,
rendered against the sign.it 1.0 design system.

> **Status:** v0.1.0. License: MIT.
> **Artifact:** `port-registry-v26.html` (single file, deploy-ready).

## What ships

| File | Purpose |
|---|---|
| `port-registry-v26.html` | The dashboard. Open it in a browser. |
| `docs/MODULES.md` | Spec + rationale for each of the 5 modules. |
| `docs/DESIGN_TOKENS.md` | sign.it 1.0 token reference. |
| `docs/INTERACTION_GUIDE.md` | Per-component animation triggers + reduced-motion behaviour. |
| `CHANGELOG.md` | Version history. |
| `LICENSE` | MIT. |

## Quickstart

```bash
git clone https://github.com/BBE-DBE/bbe-fork-control-plane.git
cd bbe-fork-control-plane
xdg-open port-registry-v26.html   # or `open` on macOS
```

No build step, no `node_modules`, no CDN. The file loads its own
mock data as JavaScript constants and runs.

## Modules

| Sidebar item | Module | Variant |
|---|---|---|
| Forks | A · Activity Ribbon | **A1** — 24h pattern per fork as stripes |
| Provisioning | B · Stream Theater | **B3** — ETA timer + live log feed |
| Agents | C · Status Stripe | **C1** — 4&#8239;px coloured edge per agent card |
| Missions | D · Kanban Fullscreen | **D1** — Done/Now/Next, scroll-snap mobile |
| API Keys | E · Spend Bars | **E2** — animated burn-rate ranking |

See `docs/MODULES.md` for the full spec, the WCAG note, and the
rationale for each variant.

## Mock data

Three forks with deterministic 24h activity vectors:

| Fork | Env | Agents | Spend (today) | Notes |
|---|---|---|---|---|
| TaxonAir | production | 5 | $8.42 | active across all 24 segments |
| BOOOMSTA | staging | 4 | $5.18 | one stalled agent |
| AATP-OS | production | 2 | $1.02 | 14h idle |

Plus 12 agents distributed across the three forks, 23 done /
5 now / 47 queued sprints, and 6 API providers (Anthropic,
OpenAI, Google, Hetzner, Netcup, Stripe).

All data lives in JS constants at the bottom of `port-registry-
v26.html` — `FORKS`, `AGENTS`, `SPRINTS`, `ACTIVITY_24H`,
`PROVIDERS`. Edit there to mock different scenarios.

## Constraints honoured

- **Single-file HTML/CSS/JS** — no external CSS, JS, fonts, or
  CDN. System-font fallback for Inter / JetBrains Mono.
- **No browser storage** — zero `localStorage`, `sessionStorage`,
  `IndexedDB`, cookies. Mock data is JS constants.
- **No external API calls** — no `fetch`, `XMLHttpRequest`,
  `WebSocket`. The "live" log feed is a `setInterval` over a
  fixed array.
- **Body 16&#8239;px minimum**, mono labels 12&#8239;px minimum.
- **WCAG AAA contrast** for all important text. Two design tokens
  (`--paper-soft`, `--rose`) lifted from their v25 values to
  clear AAA on `--ink-card` at 12&#8239;px small text. `--magenta`
  is restricted to background roles only — it doesn't pass AAA
  as text. Documented in `docs/DESIGN_TOKENS.md`.
- **Polygon clip-paths**, never `border-radius`. Cuts at 4 / 8 /
  14&#8239;px.
- **Mobile responsive** at ≤ 720&#8239;px. Sidebar collapses,
  theater stacks, kanban becomes a scroll-snap-x carousel.
- **`prefers-reduced-motion: reduce`** clamps every keyframe and
  transition to ≤ 1&#8239;ms; pulse loops lock to a static state.

## Keyboard shortcuts

- `Tab` / `Shift+Tab` — focus traversal across all interactive
  elements.
- `Esc` — close the kanban overlay if open.
- `⌘⇧K` / `Ctrl⇧K` — switch to Missions and open the kanban
  overlay in one keystroke.

## Browser support

Modern evergreen Chromium / Firefox / Safari. Uses:

- `clip-path: polygon(...)` (universal since 2018)
- `backdrop-filter: blur(...)` (Safari 9+, Firefox 103+, Chrome 76+)
- CSS custom properties (universal)
- `scroll-snap-type` (universal since 2018)

No CSS Grid subgrid, no container queries, no `:has()` selector
required.

## Operator-choice override

If `human_inbox/operator-choice-modules.md` is committed to this
repo with a different module combination (e.g. `A2/B1/C2/D3/E1`),
re-render the dashboard from the operator's choice. v26 ships
the default A1/B3/C1/D1/E2 because no such file was present at
build time. See `CHANGELOG.md` for the audit trail.

## License

MIT — see [`LICENSE`](LICENSE).
