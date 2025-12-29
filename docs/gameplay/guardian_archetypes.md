# Guardian Archetypes

Reference profiles for the five core guardians currently implemented across clients. Use these when wiring archetype data into gameplay or when generating art/audio deliverables.

## Summary Table

| Guardian | Role | Signature Ability | Synergy Morph (Partner) | Visual Motif |
| --- | --- | --- | --- | --- |
| Wolf Sentinel | Multi-hit DPS | **Moonshard Volley** – chained arrows that mark targets | Dire Howl (with Serpent) or Skymark Volley (with Owl) | Moonlit stone wolf |
| Owl Seer | Area control | **Stasis Lumen** – charge-up orb that slows and detonates | Time Grove (with Stag) or Skymark Volley (with Wolf) | Floating owl totem |
| Stag Warden | Support aura | **Verdant Pulse** – buffs atk speed + armor | Time Grove (with Owl) or Bulwark Bloom (with Bear) | Antlered shrine with roots |
| Serpent Oracle | DoT beam | **Venom Torrent** – stacking decay beam | Dire Howl (with Wolf) or Obsidian Torrent (with Bear) | Coiled serpent pillar |
| Bear Bastion | Tank / blocker | **Obsidian Guard** – spirit wall + taunt | Obsidian Torrent (with Serpent) or Bulwark Bloom (with Stag) | Obsidian bear statue |

## Upgrade Paths

### Wolf Sentinel
- Tier 2: +15% attack speed, unlocks *Howl Mark* (marked creeps take +10% damage).
- Tier 3: Arrows split on detonation for splash pressure.
- Resonances: Strong with Owl (crit vision) and Serpent (poison inheritance).

### Owl Seer
- Tier 2: Lingering stasis field (10% slow) boosts allied accuracy.
- Tier 3: *Chronal Echo* secondary explosion (50% damage after 1 s).
- Resonances: Owl + Stag heals allies; Owl + Wolf reveals stealth.

### Stag Warden
- Tier 2: Aura adds regen + shield refresh every 10 s.
- Tier 3: Aura links to nearest path blessing, empowering it by +20%.
- Resonances: Essential for Bulwark Bloom with Bear.

### Serpent Oracle
- Tier 2: Poison spreads to adjacent creeps when stacks cap.
- Tier 3: *Toxic Wake* trails linger on the path.
- Resonances: Thrives with Wolf crit chains and Owl slows.

### Bear Bastion
- Tier 2: Spirit wall gains thorns (reflects 20% melee).
- Tier 3: Wall collapse pulses damage + shields allies.
- Resonances: Anchors Obsidian Torrent (Bear + Serpent).

## Resonance Hooks

| Resonance | Requirement | Effect |
| --- | --- | --- |
| Moon Hunt | Wolf + Owl adjacency | +10% crit chance & stealth reveal |
| Eternal Grove | Owl + Stag + Bear triangle | Aura shields regen faster (+20% radius) |
| Fang Current | Wolf + Serpent | Projectiles inherit poison stacks (+15% speed) |
| Dreamward Bulwark | Any 4 unique guardians linked | Barrier blocks one rush every 30 s |

## Implementation Notes

- Archetype data should live in a shared `GuardianArchetype` config (name, ability stats, resonance tags, synergy morph payloads).
- Combat systems should reference archetype stats instead of emotion strings; this keeps theming aligned with Mythic Guardian lore.
- When exporting art/animation, include morph-ready elements (FX anchors, attach points) so combo forms stay consistent across clients.
