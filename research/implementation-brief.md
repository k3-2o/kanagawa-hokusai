# Kanagawa Obsidian Theme — Implementation Brief

You have zero context of the research phase. This prompt tells you how to get it.

## Step 1: Load Context from Memory

Search memory with these keywords to find the session that did the research:
```
kanagawa obsidian theme research wave dragon lotus palette
```

Read the memory results to understand what was discussed, why certain choices were made, and what the research uncovered. Pay attention to:
- Why we're porting Kanagawa (the nvim theme by rebelot) to Obsidian
- What existing ports exist and their flaws
- The architecture decisions made

## Step 2: Read the Research Files

All research material is at:
```
/home/k2/Documents/my-vault/Projects/kanagawa-obsidian/research/
```

Read in this exact order:

**First — the overview:**
1. `research/research-summary.md` — full project scope, palette info, existing themes, what's missing

**Second — the architecture (most important):**
2. `research/obsidian-theme-architecture.md` — how Obsidian themes work, CSS variable system, 4-layer model, specificity problem, Style Settings for multi-variant, submission process

**Third — the live examples:**
3. `research/live-theme-study.md` — analysis of Dracula Official and Blue Topaz, what patterns to borrow/avoid

**Fourth — the source material:**
4. `research/source/colors.lua` — the 104 hex colors from rebelot's palette (this is your color bible)
5. `research/source/themes.lua` — the wave/dragon/lotus semantic mappings (this tells you which color goes where)

**Fifth — existing ports for reference:**
6. `research/existing/sspaeti/theme.css` — 741 lines, 30 stars, mixed with gruvbox (know what not to do)
7. `research/existing/kmkkiii/theme.css` — 509 lines, 1 star, has all 3 variants via Style Settings (know what the baseline looks like)

## Step 3: What to Build

Create an Obsidian theme at:
```
/home/k2/Documents/my-vault/Projects/kanagawa-obsidian/
```

Required files:
- `theme.css` — all styles
- `manifest.json` — metadata
- `README.md`
- `LICENSE` (MIT)

Theme specs:
- **3 variants**: Wave (dark default), Dragon (dark alt), Lotus (light)
- Variant switching via Style Settings plugin using `class-select`
- Wave is the default dark theme, Lotus is the light theme, Dragon is an alternate dark variant

## Step 4: Architecture to Follow

Use the **3-layer pattern** confirmed by both live themes:

**Layer 1 — Palette variables** (`body {}`):
```
--sumiInk3: #1F1F28;
--fujiWhite: #DCD7BA;
// ... all 104 colors
```

**Layer 2 — Variant semantic blocks**:
```
.theme-dark { /* wave defaults */ }
.theme-dark.kanagawa-dragon { /* dragon overrides */ }
.theme-light { /* lotus */ }
```

**Layer 3 — Obsidian token mapping** (`body.theme-dark, body.theme-light {}`):
```
--background-primary: var(--sumiInk3);
--text-normal: var(--fujiWhite);
// ... map to all ~80 Obsidian design tokens
```

Important: re-declare key accent/interactive variables under `body.theme-dark, body.theme-light {}` at (0,1,1) specificity so they beat Obsidian's defaults.

## Step 5: Style Settings Config

The `class-select` pattern for variant switching (exact YAML format):

```css
/* @settings
name: Kanagawa
id: kanagawa
settings:
  -
    id: kanagawa-dark-variant
    title: Dark theme variant
    type: class-select
    allowEmpty: false
    default: kanagawa-wave
    options:
      -
        label: Wave
        value: kanagawa-wave
      -
        label: Dragon
        value: kanagawa-dragon
  -
    id: kanagawa-font-text
    title: Text font
    type: variable-text
    variable: --font-text-theme
    default: -apple-system, BlinkMacSystemFont, ...
*/
```

## Step 6: Coverage Checklist

Cover all these Obsidian CSS variable groups:
- [ ] Backgrounds (primary/secondary/modifier)
- [ ] Text colors (normal/muted/faint/accent)
- [ ] Interactive elements (buttons, hover, accent)
- [ ] Editor content (links, tags, code, tables, blockquotes)
- [ ] Headings (h1-h6)
- [ ] Bold/italic/highlight
- [ ] Code syntax highlighting (`.token.*` + `.cm-s-obsidian .cm-*`)
- [ ] Callouts (info, tip, warning, error, etc.)
- [ ] Graph view (nodes, lines, text)
- [ ] Canvas (card backgrounds)
- [ ] Workspace chrome (ribbon, titlebar, tabs, status bar, scrollbar)
- [ ] Settings modal (buttons, inputs, dropdowns)

Target size: 700-900 lines (like Dracula Official, not 30K like Blue Topaz).

## Step 7: Test

The Dracula Official theme is installed at:
```
/home/k2/Documents/my-vault/.obsidian/themes/Dracula Official/theme.css
```

The Blue Topaz theme is installed at:
```
/home/k2/.chrollo/.obsidian/themes/Blue Topaz/theme.css
```

You can reference these as live examples of the patterns described above.
