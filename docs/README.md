# Mythic Assets Documentation Hub

This folder captures the cross-platform source of truth for lore, gameplay tuning notes, and UI treatments that every client consuming the `mythic-assets` package should follow. Structure:

| Path | Purpose |
| --- | --- |
| `gameplay/` | Guardian kits, resource economies, and mechanical references used by designers and engineers. |
| `lore/` | Narrative framing for guardian spirits, dream paths, and seasonal events. |
| `ui-guidelines/` | Visual direction, typography, glyph usage, and palette tokens shared across clients. |

If you add new guardians, enemy families, or visual explorations, drop the supporting write-ups into the correct subfolder so downstream teams stay aligned.

Key entry points:

- [`gameplay/guardian_archetypes.md`](gameplay/guardian_archetypes.md) — canonical tower breakdowns.
- [`gameplay/resource_economy.md`](gameplay/resource_economy.md) — shared currency names and costs.
- [`lore/dream_paths.md`](lore/dream_paths.md) — story primer explaining what the Sanctum and Dreamlight represent.
- [`ui-guidelines/theming.md`](ui-guidelines/theming.md) — palette + typography rules for HUDs and marketing art.

> ⚠️ Remember: documentation in this repo is versioned alongside assets. If a release changes stats, lore beats, or visuals, update the paired docs before tagging.
