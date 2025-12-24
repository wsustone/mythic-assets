# Mythic Guardians Design Notes

> Canonical copy relocated here from `woolen/docs` so every client references the same lore and gameplay direction. Update this file before tagging new asset drops.

## Core Tower Archetypes

| Guardian | Role | Base Effect | Visual Motif |
| --- | --- | --- | --- |
| Wolf Sentinel | Multi-hit DPS | Rapid arrows that chain to nearby creeps | Moonlit stone wolf statue |
| Owl Seer | Area control | Slow-firing orb that explodes with lingering stasis field | Floating owl totem with glowing eyes |
| Stag Warden | Support aura | Buffs attack speed + armor for nearby guardians | Antlered shrine with radiant roots |
| Serpent Oracle | Damage-over-time | Spits venom streams that apply stacking decay | Coiled serpent pillar with flowing water |
| Bear Bastion | Tank / blocker | Creates temporary spirit walls and taunts creeps | Massive bear carved from obsidian |

## Resource & Progression Names

- **Clarity**: Primary resource, replaces "total build points". Earned passively and from defeating creeps.
- **Dreamlight**: Special currency dropped by elite/boss creeps, used to unlock guardian aspects and path blessings.
- **Echo Fragments**: Collected from mini-objectives (artifacts, checkpoints). Used to activate one-time mapwide effects.

## Combination Unlocks (after boss kills)

Each major wave ends with a named Nightmare avatar. Defeating it grants a permanent combination recipe and often a Dreamlight burst.

| Boss | Unlock | Effect |
| --- | --- | --- |
| Nightmare of Dread | Wolf + Serpent = **Dire Howl** | Chain lightning that applies decay stacks |
| Nightmare of Stagnation | Owl + Stag = **Time Grove** | Explosions leave healing auras for guardians and stasis for creeps |
| Nightmare of Ruin | Bear + Serpent = **Obsidian Torrent** | Taunt wall emits corrosive mist |
| Nightmare of Discord | Wolf + Owl = **Skymark Volley** | Rapid arrows plant time mines that explode after a delay |
| Nightmare of Hunger | Stag + Bear = **Bulwark Bloom** | Buff aura extends to path segment, energizing path filters |

Unlock flow:
1. Player defeats the boss.
2. Combination recipe becomes available in build menu (requires Dreamlight).
3. Combination upgrades an existing guardian (e.g., select Wolf tower, spend Dreamlight + Clarity to evolve into Dire Howl).

### Path Blessings (preview for map polish task)

| Blessing | Cost | Effect |
| --- | --- | --- |
| Safe Harbor | Dreamlight ×2 + 1 Clarity/min | Convert node into refuge; creeps slow & take morale damage |
| Mirror Zone | Dreamlight ×3 + 1.5 Clarity/min | Reflect 20% projectile damage toward previous segment |
| Lunar Veil | Dreamlight ×2 + 1 Clarity/min | Grants stealth/mitigation to nearby guardians until they fire |

These blessings consume Dreamlight and occupy limited path nodes, encouraging strategic placement.

---

## Current Prototype Implementation Snapshot (Dec 2025)

### Data Structures
- `Emotion` union (`'fear' | 'joy' | 'anger' | 'calm'`) drives both tower identity and enemy affinity across the codebase (@src/types/index.ts#1-67).
- `Tower` objects track id, `type` (currently an `Emotion`), `shapeId`, spatial info, level, Dreamlight stats, evolution points, and health. There is no canonical “guardian archetype” wrapper yet (@src/types/index.ts#21-32).
- `TowerShape` definitions live in `src/game/towerShapes.ts` and tie each polyomino to exactly one emotion. Placement helpers (`getShapesByEmotion`, `canPlaceShape`) expose these lists but do not yet understand rotations or archetype-specific limits (@src/game/towerShapes.ts#1-132).
- Game state slices are maintained separately in `App.tsx` (`towers`, `walls`, `enemies`, `projectiles`, `emotionCurrents`, `dreamlightPool`, etc.) without a shared `GameState` interface, which makes multi-system updates brittle (@App.tsx#50-123).

### Placement & Build Loop
1. The HUD (`TopHUD` + `ResourceBar`) lets players pick a placement mode and select an emotion, spending the corresponding `EmotionCurrents` bucket (@src/components/TopHUD.tsx#1-120, @src/components/ResourceBar.tsx#1-61).
2. `TowerShapeSelector` filters available shapes by the selected emotion and previews them with fixed block sizes (@src/components/TowerShapeSelector.tsx#1-137).
3. `App.createTower` enforces clarity cost (50), emotion-current cost (10), and grid occupancy via `gridSystem`. Successful placements record the `shapeId`, stamp a center position derived from `GRID_SIZE`, and initialize Dreamlight stats at zero (@App.tsx#197-302).
4. Walls use a parallel flow but fixed circular collision checks; towers and walls currently share the same clarity pool, aligning with “Clarity” from the design doc but without lore naming in UI yet.

### Tiered Footprint Progression (Proposed Replacement for Polyomino Shapes)
- **Philosophy**: Every guardian begins as a single-cell statue so early placement is painless, but evolving them increases their footprint. Players must reserve adjacent cells if they hope to reach Tier 3, creating meaningful layout planning without needing the Tetris-like shape picker.
- **Footprint Rules**:
  | Guardian | Tier 1 Footprint | Tier 2 Footprint | Tier 3 Footprint Concept |
  | --- | --- | --- | --- |
  | Wolf Sentinel | 1×1 | 2×2 centered on original cell | 3×2 rectangle projecting toward enemy path (forward bite) |
  | Owl Seer | 1×1 | 2×2 | 3×3 halo (needs space all around to spin up stasis field) |
  | Stag Warden | 1×1 | 2×2 | “Cross” aura: original cell plus one cell N/E/S/W for aura roots |
  | Serpent Oracle | 1×1 | 1×2 (extends forward) | 1×3 beam lane with optional side tendrils (2×3) |
  | Bear Bastion | 1×1 | 2×1 (bulk backward) | 2×3 block shielding the rear, hinting at wall creation |

- **Upgrade UX Notes**:
  - Hovering an upgrade button shows ghosted outlines for future tiers so players can keep buffer tiles clear.
  - If adjacent cells are occupied, evolution UI highlights the blocking structures and offers “relocate” guidance instead of failing silently.
  - Grid system reserves pending cells once an upgrade is queued so two simultaneous upgrades can’t collide.

- **Implementation Checklist**:
  1. Replace `TowerShapeSelector` with an “Evolution Footprint” panel that previews Tier 2/Tier 3 footprints per guardian.
  2. Update `gridSystem.canPlaceTower` and upgrade logic to use `getFootprint(tier, guardianKind)` instead of `shape.blocks`.
  3. Teach AI/FX to offset spawn points based on footprint center (large structures still fire from the original anchor).
  4. Document footprint data inside the upcoming `GuardianArchetype` configs so designers can tweak dimensions without touching layout code.

### Platform Experience & Feature Layering
- **Device Roles**
  - **Mobile (iOS/Android)**: Fast “Dream Duel” PvP matches sourced from the player’s guardian roster. UI stays stripped down—Tier 2 cap, no blessings, simplified resource HUD—so a five-minute session feels readable on touchscreens.
  - **Browser/Desktop**: Full campaign builder with tiered footprints, path blessings, resonances, commander ultimates, and map editing. This is where players plan layouts, unlock combos, and tweak guardian loadouts before jumping back to mobile.
  - Both clients share the same account and guardian inventory via backend profiles; PvP rewards (e.g., Dreamlight bursts, cosmetics) funnel back into campaign progression.

- **Layered Rollout**
  | Phase | Features Unlocked | Platforms |
  | --- | --- | --- |
  | Phase 1 – Core Loop | Single-cell guardians, Clarity-only economy, basic PvP skirmish + early campaign missions | Mobile + Browser |
  | Phase 2 – Growth | Tiered footprints, Dreamlight upgrades, archetype-specific abilities (campaign only); PvP remains at Tier 2 | Browser focus, mobile inherits simplified Tier 2 |
  | Phase 3 – Systems | Resonances, path blessings, combo evolutions, commander ultimates; PvP “advanced queue” selectively enables them | Browser primary, opt-in mobile queue |
  | Phase 4 – Live Ops | Seasonal bosses, draft PvP, path modifiers, cross-platform events | Both |

- **Design Notes**
  - Tutorials surface only the mechanics available on that platform/phase. Campaign missions act as onboarding before PvP unlocks deeper tools.
  - Guardian loadouts sync automatically; mobile client reads “approved” kits from desktop to avoid mid-match respec complexity.
  - Backend service should track feature flags per player (phase, platform) so we can A/B complexity and gather usability data before promoting systems globally.

### Combat Behavior
- `towerAI` computes range as `100 + dreamlight.speed * 5`, picks the first enemy inside that radius, and spawns a projectile with base damage `10 + level * 5`. `attackSpeed` is calculated but not yet wired to firing cadence, so towers effectively shoot every frame they find a target (@src/game/towerAI.ts#1-36).
- `synergy.getDamageMultiplier` encodes the Fear/Joy/Anger/Calm rock-paper-scissors chart, returning 1.5× for preferred matchups, 1.0× for mirrors, and 0.75× otherwise. `getSpecialAbility` only checks Dreamlight stat thresholds and emotion, not future guardian kits (@src/game/synergy.ts#1-39).
- Enemies prefer targets based on `getTargetPreference`, another hard-coded emotion loop mapping (fear→joy→anger→calm→fear). There is no notion of guardian role or archetype weight yet (@src/game/enemyMovement.ts#18-26).

### Resource & Progression Hooks
- `EmotionCurrents` represent the nitty-gritty cost to manifest a guardian; they are currently abstract numbers per emotion without thematic naming (should become Clarity inflows per guardian affinity).
- Dreamlight accumulation exists (`dreamlightPool` state + `TowerUpgrade`/HUD readouts) but towers don’t yet absorb the pool automatically; designers manually hook this later (@src/components/TowerUpgrade.tsx#1-68, @App.tsx#50-123).
- Wave configs inject enemies with randomly selected emotions unlocked per wave tier, ensuring all four affinities eventually appear but still tied to raw strings instead of guardian/creep families (@src/game/waveSystem.ts#1-120).

### Known Gaps Before Refactor
1. **Guardian Archetype Layer** – Need a `GuardianKind` union + `GuardianArchetype` config so towers reference role/kit data instead of bare emotions.
2. **Shared `GameState` Contract** – Consolidate the disparate slices that `App.tsx` threads into loops (AI, movement, HUD) to simplify future systems (combo unlocks, resonance checks).
3. **Shape/Archetype Decoupling** – `TOWER_SHAPES` must reference guardian archetypes (and rotations) rather than emotions, unlocking features like “rotate tower shapes” and cross-guardian unlocks.
4. **Combat Timing** – `towerAI` requires attack-speed scheduling plus projectile definitions tied to archetype stats to match the kits described earlier.
5. **Resource Terminology** – Align UI/state names with lore (Clarity, Dreamlight, Echo Fragments) so docs and code speak the same language, easing collaboration with design.

This snapshot should stay updated as we iterate so engineers and designers share the same picture before we formalize types or refactor gameplay logic.

## Guardian Resonance Chains

Placing guardians in adjacency grants resonance bonuses that push different playstyles:

| Resonance | Requirements | Effect |
| --- | --- | --- |
| Moon Hunt | Wolf + Owl adjacency | Shared targeting data, +10% crit + reveal stealth creeps |
| Eternal Grove | Owl + Stag + Bear triangle | Aura regenerates guardian shields, +20% buff radius |
| Fang Current | Wolf + Serpent | Projectiles inherit poison stacks, +15% projectile speed |
| Dreamward Bulwark | Any 4 unique guardians linked | Pulsating barrier blocks one creep rush every 30s |

Resonances encourage intentional layouts even on fixed paths; UI could highlight active links.

## Alternate Creep Goals

- **Memory Shards**: Creeps break off to steal Echo Fragments from side shrines; losing shards reduces Clarity income.
- **Anchor Obelisks**: Destroying these flips nearby path blessings (turning Safe Harbor into Panic Zone), forcing mid-wave reactions.
- **Pathfork Rituals**: Certain creeps can briefly open secondary routes, requiring coverage beyond the primary lane.

Mini-objectives reward proactive defense and tie into the Echo Fragment resource loop.

## Constellation Ultimates (Commander Abilities)

- **Asterion Roar**: Global taunt + damage reduction for guardians, ideal for stabilizing during rushes.
- **Nyctis Eclipse**: Blankets a path segment in darkness, slowing creeps and amplifying DoT effects.
- **Selene Mirror**: Reflects the next boss ability, turning it into a wave-wide buff for guardians.

Ultimates cost Dreamlight + a cooldown, reinforcing the commander fantasy comparable to Sol Survivor powers.

## Balancing Notes

### Resource Economy
- **Clarity** income per wave should cover ~1.5x the cost of replenishing destroyed towers, nudging players to expand rather than turtle.
- **Dreamlight** drops scale primarily with elite/boss creeps (roughly 1 Dreamlight per mini-boss, 3–5 per Nightmare) to keep combination unlocks meaningful.
- **Echo Fragments** should be scarce (1–2 per map objective) so activating mapwide effects remains a strategic choice.

### Guardian Costs & Stats (baseline suggestions)
| Guardian | Cost (Clarity) | Range | Damage/Effect cadence | Niche balance note |
| --- | --- | --- | --- | --- |
| Wolf Sentinel | 65 | Medium | 0.7s chain shots | Scales with crit/resonance to avoid early dominance |
| Owl Seer | 90 | Long | 2.5s AoE stasis burst | High Dreamlight upgrade scaling, mediocre single-target |
| Stag Warden | 75 | Short | Constant aura | Buff values cap at +25% to prevent stacking abuse |
| Serpent Oracle | 80 | Medium | Beam DoT | Damage ramps over 3s so positioning matters |
| Bear Bastion | 100 | Short | 10s wall cooldown | High health but limited damage; synergy reliant |

Combination guardians should cost +40% Clarity + 2–3 Dreamlight to evolve, ensuring the player defeats at least one Nightmare before fielding a hybrid.

### Enemy & Wave Scaling
- Base creeps: health increases ~15% per wave, movement speed +5% every two waves.
- Introduce resistances after wave 4 (e.g., “Nightglass” creeps halve DoT) to force guardian variety.
- Nightmares should test new mechanics (e.g., Nightmare of Discord periodically silences resonances) and drop sufficient Dreamlight to unlock the combo themed to that boss.

### Resonance & Blessing Limits
- Limit active resonances per guardian to 2 to avoid unstoppable chains.
- Path blessings consume Dreamlight plus a maintenance cost (small Clarity drain per minute) so players can’t blanket the entire lane.

### Commander Ultimates
- Cooldowns: 90s baseline, reduced by collecting Echo Fragments to encourage completing objectives.
- Ultimates should not directly kill bosses; instead, they set up survivability or burst windows.

Balancing should be reviewed once prototype stats exist; these targets give initial guardrails when translating the design into actual numbers.

## Detailed Guardian Kits

### Wolf Sentinel
- **Ability: Moonshard Volley** – Fires up to 3 chained projectiles, each dealing 70% base damage.
- **Upgrade Path**:
  - Tier 2: +15% attack speed, unlocks *Howl Mark* (marked creeps take +10% damage from all sources).
  - Tier 3: Arrows split on mark detonation, dealing splash.
- **Synergy Hooks**: Resonates strongly with Owl (crit vision) and Serpent (poison inheritance).

### Owl Seer
- **Ability: Stasis Lumen** – Charges for 2.5s then detonates, dealing area damage and slowing by 35% for 4s.
- **Upgrade Path**:
  - Tier 2: Adds lingering stasis field (10% slow) that boosts allied accuracy.
  - Tier 3: Gains *Chronal Echo*—secondary explosion after 1s at 50% strength.
- **Synergy Hooks**: Combines with Stag to heal allies, with Wolf to reveal stealth.

### Stag Warden
- **Ability: Verdant Pulse** – Emits aura granting +15% attack speed and +10 armor to nearby guardians.
- **Upgrade Path**:
  - Tier 2: Adds health regeneration + shield refresh every 10s.
  - Tier 3: Aura extends to closest path blessing, empowering it by +20%.
- **Synergy Hooks**: Critical to resonance webs; pairs with Bear for Bulwark Bloom.

### Serpent Oracle
- **Ability: Venom Torrent** – Continuous beam applying stacking decay (5% per tick, up to 30%).
- **Upgrade Path**:
  - Tier 2: Poison spreads to adjacent creeps when stacks max out.
  - Tier 3: Unlocks *Toxic Wake* trails that linger on the path.
- **Synergy Hooks**: Benefits from Wolf crits (higher burst) and Owl slows (longer exposure).

### Bear Bastion
- **Ability: Obsidian Guard** – Periodically erects a spirit wall that blocks creeps for 3s and taunts.
- **Upgrade Path**:
  - Tier 2: Wall gains thorns, reflecting 20% melee damage.
  - Tier 3: Upon collapse, wall pulse damages enemies and shields allies.
- **Synergy Hooks**: Anchor for support networks, essential for Obsidian Torrent combo.

## Creep Families & Stat Progression

### Lesser Nightmares (Core fodder)
| Type | Wave Intro | Health Multiplier | Speed | Traits |
| --- | --- | --- | --- | --- |
| Shades | Wave 1 | 1.0 | 1.0 | Baseline creep, no resistances |
| Glimmers | Wave 2 | 0.7 | 1.4 | Low health runners; drop extra Clarity |
| Husk | Wave 3 | 1.4 | 0.8 | Armored, weak to magic/DoT |

### Elite Spirits
| Type | Wave Intro | Health Multiplier | Speed | Traits |
| --- | --- | --- | --- | --- |
| Nightglass | Wave 4 | 1.6 | 1.0 | 50% DoT resistance, reflects 10% ranged damage |
| Dreadbound | Wave 5 | 2.0 | 0.9 | Carries shield that must be broken before damage applies |
| Riftrunner | Wave 6 | 1.2 | 1.3 | Periodically dashes, ignoring slows for 2s |

### Objective Creeps
| Type | Behavior | Reward/Penalty |
| --- | --- | --- |
| Shard Thief | Splits from main path to steal Echo Fragments; killing it returns double fragments. | Lose 1 Clarity/sec while shard missing. |
| Anchor Breaker | Targets path blessings to invert them. | Prevents maintenance cost for 30s if stopped. |
| Pathforger | Opens temporary secondary lane if not intercepted. | Secondary lane spawns Glimmers for 20s. |

### Nightmares (Bosses)
- **Wave 5: Nightmare of Dread** – Focuses lightning + decay, telegraphs targeted strikes on high-DPS towers.
- **Wave 10: Nightmare of Stagnation** – Emits expanding stasis zones, punishes clustered defenses.
- **Wave 15: Nightmare of Ruin** – Summons Anchor Breakers mid-fight, heavily armored body segments.
- **Wave 20: Nightmare of Discord** – Silences resonances on hit; requires distributed guardian network.

**Scaling formulae (baseline):**
- Health = BaseHealth * (1 + 0.15 * (wave - 1)) for fodder; elites start at BaseHealth * 2.
- Speed increases by 5% every other wave, capped at 1.8x.
- Armor/resistance introduced after wave 4, increasing 10% every 3 waves.

Bosses drop 3–5 Dreamlight + unique crafting materials for combinations.

## Map & Path Presentation

| Layer | Implementation Notes | Gameplay Impact |
| --- | --- | --- |
| Dreamstream Track | Gradient ribbon following path, highlights turns with glyphs | Clarifies enemy route, sells mythic theme |
| Build Zones | Hex glyph overlay on valid tiles; blocked tiles show faint roots/rocks | Communicates placement quickly |
| Waypoint Totems | Small guardian idols every 2–3 nodes, pulse when creeps pass | Adds rhythm; tie into path blessings |
| Background Parallax | Soft constellations + drifting particles | Depth/ambience without clutter |
| Path Status UI | Mini badges for active blessings/resonances along path edges | Helps players see effects at a glance |

During polish, ensure overlays scale with board bounds (already started in LevelPaths). Provide toggle for debug vs. final art.

## Spawn Pacing & Wave Composition Guidelines

| Wave Tier | Spawn Behavior | Notes |
| --- | --- | --- |
| 1–3 | Linear spawns every 2.0s, max 4 concurrent | Tutorial pacing, only Shades/Glimmers/Husks |
| 4–6 | Alternate bursts (3 creeps quickly, 2s pause) | Introduce elites + first objective creep |
| 7–10 | Dual-route warnings: telegraph spawn point 3s prior | Encourage coverage; add Riftrunners |
| 11–15 | Mixed packs (fast runners shielded by tanks) | Use HUD countdown + audio cue |
| 16+ | Boss escorts (Nightmare + support squads) | Dreamlight rewards increased to fund combos |

Spawn interval should never fully pause unless wave complete; the spawn loop fix ensures retries instead of stopping at cap.

## Creep Goal Variant Hooks

- **Artifact Recovery**: Optional side objective; defending earns Echo Fragments. Failure boosts next wave health by 10%.
- **Sanctum Pulse**: Player can trigger a pulse every few waves to stun creeps on marked nodes if no Anchor Breakers survive.
- **Dreamlight Wells**: Neutral points that, once captured, provide periodic Dreamlight but attract Pathforgers if left unguarded.

Tie these hooks into upcoming gameplay loops so players feel agency beyond tower placement.
