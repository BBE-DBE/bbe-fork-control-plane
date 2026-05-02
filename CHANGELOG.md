# Changelog

All notable changes to bbe-fork-control-plane are documented here. The
format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/);
versioning follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Status

**BLOCKED** on T-PORT-REGISTRY-UI input —
`/mnt/user-data/uploads/port-registry-v25_1.html` is not present on
this system (entire `/mnt` tree absent). See `README.md` for the
search log and the resolution path.

### Added

- Repo skeleton (`main` branch, `git config` set).
- `LICENSE` (MIT).
- `README.md` documenting the blocker and the unblock path.
- This `CHANGELOG.md`.

### Not added (deferred until input lands)

- `port-registry-v26.html` — extension of the v25_1 source. Cannot be
  produced without the source, because the directive's hard
  constraints (_"Keine Änderung an existing views"_, _"Bestehende
  Items bleiben unverändert"_) require diff-style preservation.

## [0.0.0] — 2026-05-03

Repository initialized.
