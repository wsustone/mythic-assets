# Resource & Progression Economy

Shared terminology and baseline values for currencies, unlock flow, and pacing. Keep gameplay code, HUD copy, and marketing beats aligned with these definitions.

## Currency Glossary

| Resource | Usage | Acquisition | Notes |
| --- | --- | --- | --- |
| **Clarity** | Primary build currency for guardians, walls, and blessings | Passive income per wave, creep kills, map objectives | Baseline income should support ~1.5× rebuild cost each wave to encourage expansion. |
| **Dreamlight** | Unlocks guardian aspects, combination evolutions, and major path blessings | Elite/boss drops (≈1 for mini, 3–5 for Nightmares), rare map nodes | Treated as special currency; UI must highlight when players earn it. |
| **Echo Fragments** | Activates one-time mapwide effects, sustains commander ultimates | Side objectives (Artifacts, Wells), some blessings refund on completion | Keep scarce (1–2 per map objective). Loss events often drain Clarity per second. |

## Costs & Baselines

| Guardian | Clarity Cost | Range | Cadence | Notes |
| --- | --- | --- | --- | --- |
| Wolf Sentinel | 65 | Medium | 0.7 s chain shots | Scales via crit/resonance to avoid early dominance. |
| Owl Seer | 90 | Long | 2.5 s charge → AoE | Relies on Dreamlight upgrades for power spikes. |
| Stag Warden | 75 | Short aura | Continuous | Buff caps at +25% to prevent stacking abuse. |
| Serpent Oracle | 80 | Medium beam | Continuous ramp | DoT builds over 3 s; positioning critical. |
| Bear Bastion | 100 | Short | 10 s wall cooldown | High health anchor; low DPS without combos. |

Combination guardians cost **+40% Clarity** plus **2–3 Dreamlight**; players must defeat at least one Nightmare to evolve hybrids via synergy morphs (no footprint expansion).

## Dreamlight Expenditures

1. **Guardian Evolutions** – Spend Dreamlight + Clarity to ascend a specific guardian tier or trigger combos.
2. **Path Blessings** – Reserve map nodes that offer global buffs at the expense of ongoing Clarity maintenance.
3. **Commander Ultimates** – Optional unlocks with cooldowns reduced by Echo Fragments; Dreamlight sets the activation threshold.

## Path Blessing Reference

| Blessing | Cost | Effect | Maintenance |
| --- | --- | --- | --- |
| Safe Harbor | Dreamlight ×2 + 1 Clarity/min | Converts node into refuge; creeps slow & take morale damage. | Pay Clarity upkeep while active. |
| Mirror Zone | Dreamlight ×3 + 1.5 Clarity/min | Reflects 20% projectile damage toward previous segment. | Pauses if Anchor Breaker flips node. |
| Lunar Veil | Dreamlight ×2 + 1 Clarity/min | Grants stealth/mitigation to nearby guardians until they fire. | Highlight in HUD with crescent badge. |

Limit blessing slots per map to avoid covering every path; design intent is meaningful territory control.

## Combination Unlock Flow

1. Defeat the associated Nightmare boss (see `docs/lore/dream_paths.md` for narrative context).
2. Recipe appears in the build/evolution menu, requiring Dreamlight + Clarity.
3. Player selects an eligible guardian and evolves it into the named combo (e.g., Wolf + Serpent → **Dire Howl**).

| Boss | Unlock | Effect |
| --- | --- | --- |
| Nightmare of Dread | Wolf + Serpent = **Dire Howl** | Chain lightning that also applies decay stacks. |
| Nightmare of Stagnation | Owl + Stag = **Time Grove** | AoE blasts leave healing aura + stasis. |
| Nightmare of Ruin | Bear + Serpent = **Obsidian Torrent** | Taunt wall emits corrosive mist. |
| Nightmare of Discord | Wolf + Owl = **Skymark Volley** | Rapid arrows plant delayed time mines. |
| Nightmare of Hunger | Stag + Bear = **Bulwark Bloom** | Buff aura extends along path, energizing filters. |

## Progression Phases

| Phase | Features | Platforms |
| --- | --- | --- |
| Phase 1 – Core Loop | Single-cell guardians, Clarity-only builds, basic PvP skirmish | Mobile + Browser |
| Phase 2 – Growth | Tiered footprints, Dreamlight upgrades, archetype abilities | Browser primary, mobile Tier 2 cap |
| Phase 3 – Systems | Resonances, blessings, combos, commander ultimates | Browser focus, opt-in mobile queue |
| Phase 4 – Live Ops | Seasonal bosses, draft PvP, path modifiers | Both |

Phase gates should be mirrored in backend feature flags so accounts unlock complexity gradually.
