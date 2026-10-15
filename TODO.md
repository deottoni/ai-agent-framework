# TODO

## Rebuild the interactive HTML guide (v1.1+, both libraries)

Andre likes the visual, interactive guide format and wants it for his AI features. The original is archived at `archive/interactive-guide-v1.0.0.html`. It's a single-file page with a sidebar nav, an overview with stat tiles, an animated conversation-flow strip, intent-category chips, design-principle cards, the 5-layer assembly order, a config-variables reference and a pre-deploy checklist.

**Why rebuild instead of updating it:** it's a v1.0.0 (2026-02-26) snapshot of the old flat layout.
- 11 of the 22 files it describes changed in v1.1.0 (see `customer-facing/CHANGELOG.md`).
- It has no `internal-ops/` coverage (the 8 Claude Code subagents).
- It's missing `GETTING_STARTED`, `EXAMPLE_DEPLOYMENT`, `VARIABLES.md` and `CHANGELOG`.
- It's off-brand: indigo accent and system fonts.

**Scope for the new version:**
- [ ] Keep the archived page's interaction model (sidebar sections, flow strip, clickable cards, checklist).
- [ ] Cover both libraries: `customer-facing/` (modules / policies / tools / assembly / variables / pre-deploy) and `internal-ops/` (the 8 agents, agent anatomy, getting started).
- [ ] Generate counts and file lists from the real repo contents (e.g. `customer-facing/metadata.yml`) so it can't drift again.
- [ ] Use Andre's brand: dark #060709, a single cyan accent #2BBDF5, Outfit headings + DM Sans body, AO monogram. Spec: `~/Desktop/claude/ai-consulting/brand/andreottoni/design-system.md`.
- [ ] Ship as `index.html` at the repo root (GitHub Pages ready, same as zero-to-claude) and link it from the README and andreottoni-site.
- [ ] Consider making it a reusable pattern for guides to Andre's other AI features (ai-pm-os, ai-builder-os, zero-to-claude).
