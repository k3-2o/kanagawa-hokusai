# Kanagawa Obsidian Theme — Research Phase

## Source Material

Pulled directly from [rebelot/kanagawa.nvim](https://github.com/rebelot/kanagawa.nvim) (master branch):

| File | Source Path | Description |
|------|------------|-------------|
| `source/colors.lua` | `lua/kanagawa/colors.lua` | Raw palette colors (all 3 variants) |
| `source/themes.lua` | `lua/kanagawa/themes.lua` | Semantic mapping of palette to UI/syntax elements |
| `source/init.lua` | `lua/kanagawa/init.lua` | Module entry, config options |
| `source/utils.lua` | `lua/kanagawa/utils.lua` | Color utility functions |
| `source/palette.py` | `palette.py` | Python script to extract palettes from images |
| `source/README.md` | `README.md` | Full project documentation |
| `source/LICENSE` | `LICENSE` | MIT License |

### Platform Extras (for cross-reference)

| File | Source Path |
|------|------------|
| `source/kanagawa_wave.toml` | `extras/alacritty/kanagawa_wave.toml` |
| `source/kanagawa_dragon.toml` | `extras/alacritty/kanagawa_dragon.toml` |
| `source/kanagawa_lotus.toml` | `extras/alacritty/kanagawa_lotus.toml` |
| `source/kanagawa_wave.ini` | `extras/foot/kanagawa-wave.ini` |
| `source/kanagawa_dragon.ini` | `extras/foot/kanagawa-dragon.ini` |
| `source/kanagawa_lotus.ini` | `extras/foot/kanagawa-lotus.ini` |
| `source/kanagawa_wave.conf` | `extras/kitty/kanagawa.conf` |
| `source/kanagawa_dragon.conf` | `extras/kitty/kanagawa_dragon.conf` |
| `source/kanagawa_lotus.conf` | `extras/kitty/kanagawa_light.conf` |

## The 3 Variants (from author's palette)

### Wave (default) — Deep ocean tones
- Background: `#1F1F28` (sumiInk3)
- Foreground: `#DCD7BA` (fujiWhite)
- Accents: springGreen `#98BB6C`, crystalBlue `#7E9CD8`, sakuraPink `#D27E99`
- Mood: Warm dark, high contrast, heart-warming

### Dragon — Late-night muted
- Background: `#181616` (dragonBlack3) — warmer, slightly brown
- Foreground: `#C5C9C5` (dragonWhite) — cooler, greenish-white
- Accents: dragonGreen `#87A987`, dragonBlue `#8BA4B0`, dragonRed `#C4746E`
- Mood: Muted, low contrast, easier on eyes in darkness

### Lotus — Light mode
- Background: `#F2ECBC` (lotusWhite3) — warm parchment
- Foreground: `#545464` (lotusInk1) — soft dark
- Accents: lotusGreen `#6F894E`, lotusBlue `#4D699B`, lotusRed `#C84053`
- Mood: Warm paper-like, comfortable light mode

## Existing Obsidian Theme Landscape

### 1. sspaeti/obsidian_kanagawa (2022, 741 lines, 30 stars)
https://github.com/sspaeti/obsidian_kanagawa

**Good:** Full CSS with palette variables, works as community theme
**Bad:** 
- Mixed kanagawa + gruvbox palette (headings use gruvbox colors)
- Only wave (dark) + lotus (light) — no dragon
- Doesn't use Obsidian's modern CSS variable system fully
- No StyleSettings integration for variant switching
- Some color mappings deviate from nvim theme's semantics

### 2. kmkkiii/obsidian-kanagawa-theme (2026, 509 lines, 1 star)
https://github.com/kmkkiii/obsidian-kanagawa-theme

**Good:** 
- All 3 variants (wave, dragon, lotus)
- Uses Style Settings to switch wave↔dragon in dark mode
- Cleaner variable naming, modern Obsidian CSS vars
- MIT license

**Bad:**
- Minimal styling — mostly CSS variable mappings
- No custom component styling (callouts, embeds, etc.)
- Missing deeper Obsidian UI overrides
- Barely more than a starter theme with a palette swap

## What's Missing / Opportunity

Neither existing theme goes deep on:

1. **Faithful semantic mapping** — match nvim's `themes.lua` classifications directly to Obsidian's CSS properties
2. **All 3 variants with seamless switching** — wave/dragon/lotus as true themes, not just dark-mode overlays
3. **Complete component coverage** — callouts, embeds, tabs, search, modals, graph, backlinks, blockquotes, todos
4. **Syntax highlighting** — match kanagawa's TreeSitter scheme for code blocks
5. **Clean structure** — separate palette vars from theme mappings from overrides, documented for contributors
6. **Small file size** — the nvim theme is ~400 lines of Lua. An Obsidian version should be ~600-800 lines of CSS, not 2000

## Palette Extraction

The full canonical palette has **104 named colors** across all 3 variants. Key groups:

```
Wave (dark):       23 colors (sumiInk*, waveBlue*, winter*, autumn*, etc.)
Syntax accents:    20 colors (spring*, sakura, waveRed, etc.)
Dragon (dark alt): 18 colors (dragonBlack*, dragon*, etc.)
Lotus (light):     37 colors (lotus*, paper-like, soft-white)
Shared diag:       5 colors (samuraiRed, roninYellow, etc.)
```

## Next Steps (Implementation Phase)

1. Initialize Obsidian theme structure (`theme.css`, `manifest.json`, README)
2. Port all 104 palette colors as CSS custom properties in `:root`
3. Map theme variants using `.theme-dark` / `.theme-light` + `.kanagawa-wave` / `.kanagawa-dragon`
4. Map Obsidian CSS variables to kanagawa semantics following author's `themes.lua` schema
5. Add StyleSettings for variant switching
6. Style components: callouts, code blocks, tables, graph, search, modals
7. Test and iterate
