# Consuming Shared Assets

This repo is designed to be pulled into multiple game clients (Expo mobile, macOS native, PC, etc.). Use the workflow below to stay in sync.

## 1. Add as a Submodule

```bash
git submodule add <git-url> mythic-assets
```

Commit the updated `.gitmodules` file so the link is preserved for all contributors.

## 2. Pull Latest Assets

```bash
git submodule update --remote mythic-assets
```

This fetches the newest tagged release or main branch assets (depending on your submodule tracking config).

## 3. Reference Assets

- **Expo / React Native:**
  - SVGs via [`react-native-svg`](https://github.com/software-mansion/react-native-svg):
    ```tsx
    import WolfIcon from '../../mythic-assets/art/svg/guardians/wolf.svg';
    ```
  - Raster textures via standard `Image` imports (ensure assets are copied into `expo-assets` during bundling if needed).
- **Native macOS / Windows:**
  - Copy or link required files from `mythic-assets/art/...` into your platform asset catalog.
  - Fonts live under `typography/` and include license notes in the same folder.

## 4. Document Changes

Whenever you add or update assets:
1. Update the relevant Markdown file under `docs/` (e.g., `docs/lore/wolf.md`).
2. Describe palette/usage notes in `art/palettes/`.
3. Tag a release (`git tag vYYYY.MM.DD-assets && git push --tags`) so client repos can pin stable packs.

---
If you need automated optimization (SVGO, sprite sheet builders, etc.), place scripts inside `tooling/scripts/` and document their usage in this folder.
