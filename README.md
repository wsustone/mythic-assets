# Mythic Assets

A shared repository for cross-platform documentation, art, audio, and thematics supporting the Mythic Guardians universe.

## Structure

```
mythic-assets/
├── docs/
│   ├── gameplay/
│   ├── lore/
│   └── ui-guidelines/
├── art/
│   ├── svg/
│   ├── raster/
│   └── palettes/
├── typography/
├── audio/
├── exports/
│   ├── mobile/
│   └── desktop/
└── tooling/
    └── scripts/
```

## Usage

1. **Add as submodule** (recommended):
   ```bash
   git submodule add <repo-url> mythic-assets
   ```
2. **Pull latest assets**:
   ```bash
   git submodule update --remote mythic-assets
   ```
3. **Consume assets** within platform repos (Expo, macOS, etc.) by referencing the shared folders (e.g., `art/svg/guardians/wolf.svg`).

## Contribution Guidelines

- Commit both source design files (e.g., `.fig`, `.psd`) and exported assets (`.svg`, `.png`).
- Document new guardians/mechanics in `docs/` with Markdown.
- Use `tooling/scripts` for optimization tasks (e.g., `svgo`).
- Tag releases when asset batches are ready (e.g., `v2025.12.24-assets`).
