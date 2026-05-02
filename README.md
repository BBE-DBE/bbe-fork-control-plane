# bbe-fork-control-plane

Working directory for **T-PORT-REGISTRY-UI** — extends the existing
`port-registry-v25_1.html` control plane with four new views
(provisioning / agents / missions / api-keys) and adds two new
sections to the existing `tenants` and `settings` views.

## Status: **BLOCKED — required input missing**

The directive's `INPUT` step requires reading
`/mnt/user-data/uploads/port-registry-v25_1.html` (4871 lines). That
file is **not present on this system**. Searched:

- `/mnt/user-data/uploads/` — directory does not exist (`/mnt` itself absent)
- `/home/dev/**/*port-registry*.html` — no matches
- `/tmp/`, `/var/`, `/opt/` — no matches
- `/home/dev/projects/port-registry/` — TypeScript service, no HTML
  artifact to extend
- Anywhere matching `*v25_1*` or `*v25*.html` — no matches

The directive's hard constraints make a from-scratch rewrite
inappropriate:

- _"Keine Änderung an existing views (ports, hosts, strategy, tenants
  existing parts)"_ — cannot preserve what is not on disk.
- _"Bestehende Items bleiben unverändert"_ — same.
- _"Erweitere zu port-registry-v26.html"_ — implies the v25_1 source
  must be the base.

## What was done

- Repo initialised on `main` (`git config` set per directive).
- This README documenting the blocker.
- `LICENSE` (MIT) written per directive.

## Resolution path

The operator should:

1. Upload `port-registry-v25_1.html` to a path I can read, e.g.
   `/home/dev/projects/bbe-fork-control-plane/inputs/port-registry-v25_1.html`,
   or attach it directly to the next directive turn.
2. Optionally confirm `MASS-MERGE-FINAL-3` is finished (the directive's
   first `VORAUSSETZUNG`).
3. Re-fire T-PORT-REGISTRY-UI; the work plan below picks up from there.

## Work plan once unblocked (Phase 1 → 4)

**Phase 1 — read + plan.** Inventory the v25_1 structure: sidebar
markup, `data-view` slots, design tokens (colour palette, polygon
clip-path values, typography), Lucide icon usage pattern, mock-data
shape conventions.

**Phase 2 — extend.** Insert four new `data-view` blocks
(provisioning / agents / missions / api-keys) with the documented
sidebar items (icons: `server-cog`, `users`, `target`, `key-round`).
Extend `tenants` (active forks + spawn button) and `settings`
(API-keys + hardware-target).

**Phase 3 — mock data.** Three forks
(`project-alpha`/production, `project-beta`/staging,
`project-gamma`/idle); 3-5 agents per fork
(`architect` / `claude_code` / `reviewer`); 5-10 sprints per fork
with estimates; empty API-key fields with placeholder hints.

**Phase 4 — browser smoke.** View-switching, basic form validation,
mock-submit status renders. Single-file artifact, no
`localStorage`, no external API calls.

## Constraints (carried forward, applied once input lands)

- sign.it Design System v1.0 — token-only styling.
- Polygon `clip-path` 4-14 px corners, **no** `border-radius`.
- Typography: Inter (UI) + JetBrains Mono (code/values).
- Dark CI palette: `#07060D` / `#0F0E1A` / `#15131F` / `#F2F1F5`.
- Magenta primary `#C81EBD`. Layer accents: lime, lavender, teal,
  amber, rose, violet.
- Lucide SVG icons matching the existing inline-SVG style.
- HTML/CSS/JS in one file. No `localStorage`.
