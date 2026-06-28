# Obsidian Theme Architecture — Full Research

## 1. Theme File Structure

Every Obsidian theme needs exactly **2 required files** in a folder inside `.obsidian/themes/`:

```
Kanagawa/
├── manifest.json    # metadata (required)
├── theme.css        # all styles (required)
├── README.md        # recommended for community listing
├── LICENSE          # recommended (MIT preferred)
└── screenshots/     # submission requires 512×288 px image
```

### manifest.json
```json
{
  "name": "Kanagawa",
  "version": "1.0.0",
  "minAppVersion": "1.0.0",
  "author": "k2",
  "authorUrl": "https://github.com/your-username"
}
```

The `name` field **must** match the directory name exactly.

### versions.json (optional, for future-proofing)
```json
{
  "1.0.0": "1.0.0"
}
```

## 2. CSS Architecture — The 4-Layer Model

Based on analysis of successful multi-variant themes (Noctis Family, Arcadia, Minimal), the recommended structure is:

### Layer 1: Global Constants (`body {}`)
```css
body {
  /* Custom palette variables — always available */
  --kanagawa-sumiInk3: #1F1F28;
  --kanagawa-fujiWhite: #DCD7BA;
  /* ... all 104 palette colors */
}
```

### Layer 2: Dark/Light Mode Fallbacks (`.theme-dark {}`, `.theme-light {}`)
```css
.theme-dark {
  /* Wave palette by default (when no variant selected) */
  --kanagawa-bg: var(--kanagawa-sumiInk3);
  --kanagawa-fg: var(--kanagawa-fujiWhite);
}

.theme-light {
  /* Lotus palette by default */
  --kanagawa-bg: var(--lotusWhite3);
  --kanagawa-fg: var(--lotusInk1);
}
```

### Layer 3: Variant Overrides (`body.kanagawa-dragon {}`)
Applied via Style Settings `class-select`. Each variant block overrides the OOTB (out-of-the-box) variables. Chained with `.theme-dark` or `.theme-light` for proper specificity.

```css
.theme-dark.kanagawa-dragon {
  --kanagawa-bg: var(--dragonBlack3);
  --kanagawa-fg: var(--dragonWhite);
}
```

### Layer 4: Mapping to Obsidian's CSS Design Tokens (`body {}`)
This is where we translate our semantic palette → Obsidian's official CSS variables.

```css
body {
  /* Backgrounds */
  --background-primary: var(--kanagawa-bg);
  --background-secondary: var(--kanagawa-bg-alt);

  /* Text */
  --text-normal: var(--kanagawa-fg);
  --text-muted: var(--kanagawa-fg-dim);

  /* Links */
  --link-color: var(--kanagawa-link);
  --link-color-hover: var(--kanagawa-link-hover);

  /* Code */
  --code-normal: var(--kanagawa-code);
  --code-background: var(--kanagawa-code-bg);

  /* Headings */
  --h1-color: var(--kanagawa-h1);
  --h2-color: var(--kanagawa-h2);
  /* ... */
}
```

## 3. The Specificity Problem (Critical)

Obsidian internally sets color variables with this specificity:
```css
body.theme-dark { /* (0,1,1) */ }
body.theme-light { /* (0,1,1) */ }
```

If the theme only defines variables under `body` (specificity 0,0,1), Obsidian's built-in defaults **will override them** because 0,1,1 > 0,0,1.

**The fix:** Re-declare key accent/interactive variables under `body.theme-dark, body.theme-light`:
```css
/* (0,1,1) — matches Obsidian's specificity exactly, source-order wins */
body.theme-dark,
body.theme-light {
  --color-accent: var(--kanagawa-accent);
  --interactive-accent: var(--kanagawa-accent);
  --background-primary: var(--kanagawa-bg);
  --text-normal: var(--kanagawa-fg);
}
```

## 4. Offering Multiple Variants via Style Settings

The [Style Settings plugin](https://github.com/mgmeyers/obsidian-style-settings) (2.3M downloads) allows theme-defined configuration UI.

For variant switching, use `class-select`:

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
  -
    id: kanagawa-font-mono
    title: Monospace font
    type: variable-text
    variable: --font-monospace-theme
    default: 'Hack Nerd Font', 'Source Code Pro', ...
*/
```

This generates a dropdown in Obsidian's Style Settings pane. When user picks "Dragon", the class `kanagawa-dragon` is added to `<body>`, ensuring `.theme-dark.kanagawa-dragon` selectors activate.

### Wrangling the class-select behavior
- `class-select` adds the value as a class on `body`
- It only adds one class at a time (mutually exclusive)
- With `allowEmpty: false`, one option is always active
- The default can be set via `default: kanagawa-wave`
- Works for dark variants. Light mode (lotus) uses `.theme-light` natively

## 5. Obsidian's CSS Variable System (~400+ variables)

### Core Backgrounds (theme-dark / theme-light)
| Variable | Purpose |
|----------|---------|
| `--background-primary` | Main editor background |
| `--background-primary-alt` | Elevated surfaces on primary |
| `--background-secondary` | Sidebar background |
| `--background-secondary-alt` | Elevated sidebar elements |
| `--background-modifier-border` | Borders |
| `--background-modifier-hover` | Hover highlight |
| `--background-modifier-active-hover` | Active hover highlight |
| `--background-modifier-form-field` | Input bg |
| `--background-modifier-error` | Error bg |
| `--background-modifier-success` | Success bg |
| `--background-modifier-message` | Toast/notice bg |

### Text Colors
| Variable | Purpose |
|----------|---------|
| `--text-normal` | Primary text |
| `--text-muted` | Dimmed text |
| `--text-faint` | Faint/secondary text |
| `--text-accent` | Accent text |
| `--text-accent-hover` | Accent hover |
| `--text-on-accent` | Text on accent bg |
| `--text-error` | Error text |
| `--text-warning` | Warning text |
| `--text-success` | Success text |
| `--text-selection` | Selection highlight bg |
| `--text-highlight-bg` | Mark/highlight bg |

### Interactive Elements
| Variable | Purpose |
|----------|---------|
| `--interactive-normal` | Default interactive bg |
| `--interactive-hover` | Interactive hover bg |
| `--interactive-accent` | Primary accent bg (buttons, active items) |
| `--interactive-accent-hover` | Accent hover |
| `--checkbox-color` | Checked checkbox |
| `--checkbox-border-color` | Checkbox border |
| `--toggle-thumb-color` | Toggle switch dot |

### Editor Content
| Variable | Purpose |
|----------|---------|
| `--h1-color` through `--h6-color` | Heading colors |
| `--bold-color` | Bold text color |
| `--italic-color` | Italic text color |
| `--link-color` | Internal link color |
| `--link-color-hover` | Internal link hover |
| `--link-external-color` | External link |
| `--link-external-color-hover` | External link hover |
| `--link-unresolved-color` | Broken/wikilink not found |
| `--tag-color` | Tag text color |
| `--tag-background` | Tag background |
| `--code-normal` | Code text color |
| `--code-background` | Code block background |
| `--code-comment` | Comment color in code |
| `--code-function` | Function color in code |
| `--code-keyword` | Keyword color in code |
| `--code-string` | String color in code |
| `--code-number` | Number color in code |
| `--code-operator` | Operator color in code |
| `--table-header-background` | Table header |
| `--table-row-even-background` | Even rows |
| `--table-row-odd-background` | Odd rows |
| `--blockquote-color` | Blockquote text |
| `--blockquote-border-color` | Blockquote left border |

### Callout Colors (Obsidian 0.15+)
| Variable | Default |
|----------|---------|
| `--callout-info` | `--color-blue` |
| `--callout-todo` | `--color-blue` |
| `--callout-abstract` | `--color-cyan` |
| `--callout-tip` | `--color-cyan` |
| `--callout-success` | `--color-green` |
| `--callout-question` | `--color-orange` |
| `--callout-warning` | `--color-orange` |
| `--callout-failure` | `--color-red` |
| `--callout-error` | `--color-red` |
| `--callout-danger` | `--color-red` |
| `--callout-bug` | `--color-red` |
| `--callout-example` | `--color-purple` |
| `--callout-quote` | `--color-gray` |

### Graph View
| Variable | Purpose |
|----------|---------|
| `--graph-controls-background` | Graph controls panel |
| `--graph-controls-foreground` | Controls text |
| `--graph-controls-border` | Controls border |
| `--graph-line` | Edges between nodes |
| `--graph-node` | Default node |
| `--graph-node-unresolved` | Unresolved note |
| `--graph-node-focused` | Focused/selected node |
| `--graph-node-tag` | Tag node |
| `--graph-node-attachment` | Attachment node |

### Canvas
| Variables for card backgrounds, borders, etc. |

### Ribbon
| Variable | Purpose |
|----------|---------|
| `--ribbon-background` | Left ribbon bg |
| `--ribbon-background-collapsed` | Collapsed ribbon bg |
| `--ribbon-border-color` | Ribbon right border |

### Scrollbar
| Variable | Purpose |
|----------|---------|
| `--scrollbar-bg` | Track |
| `--scrollbar-thumb-bg` | Thumb |
| `--scrollbar-active-thumb-bg` | Active thumb |

### Window Frame
| Variable | Purpose |
|----------|---------|
| `--titlebar-background` | Title bar bg |
| `--titlebar-background-focused` | Focused title bar |
| `--titlebar-text-color` | Title text |
| `--titlebar-text-color-focused` | Focused title text |

## 6. Community Theme Submission Process

1. **Host on GitHub** — public repo with `theme.css`, `manifest.json`, README, LICENSE
2. **Create a GitHub Release** — tag version matching `manifest.json`, attach `theme.css` and `manifest.json` as binary assets
3. **Submit via community.obsidian.md** — link your GitHub account, enter repo URL, agree to developer policies
4. **Pass automated review** — Obsidian's CI checks the manifest, theme structure, and license
5. **Announce** — post in forum's Share & Showcase, Discord #updates (requires developer role)

### Review checklist:
- ✅ MIT or similar open-source license
- ✅ No remote network calls / remote assets loaded
- ✅ `!important` avoided where possible
- ✅ At least one screenshot (512×288 px)
- ✅ Works in both light and dark mode (or declares which it supports)

## 7. Pattern Reference: Noctis Family for Multi-Variant Themes

The [Noctis Family theme](https://github.com/baylesj/obsidian-noctis-family) by baylesj is the best reference for multi-variant structure:

1. **Custom palette vars**: `--noc-base-00` through `--noc-base-100` (12-step neutral scale)
2. **Syntax vars**: `--noc-keyword`, `--noc-string`, `--noc-function`, etc.
3. **Default block** (`body {}`): maps all vars to Obsidian design tokens
4. **Variant blocks** (`body.noctis-azureus {}` etc.): override only the custom vars
5. **Specificity fix** (`body.theme-dark, body.theme-light {}`): re-declares key accent/interactive vars
6. **Syntax highlighting**: separate `.cm-s-obsidian .cm-*` and `.token.*` selectors
7. **Component styles**: workspace chrome, settings modal, buttons, inputs, scrollbars, graph

### Key pattern for our Kanagawa port:
```
body (custom palette + mapping)          → Layer 1 + Layer 4
.theme-dark (wave defaults)               → Layer 2
.theme-light (lotus defaults)             → Layer 2
.theme-dark.kanagawa-dragon (alt dark)    → Layer 3
.theme-dark.kanagawa-wave (explicit)      → Layer 3 (redundant but safe)
body.theme-dark, body.theme-light (fix)   → Specificity override
```

## 8. CSS Variable Naming Convention

Use prefixed custom properties to avoid collisions:
```
--kanagawa-waveBlue1: #223249;
--kanagawa-sakuraPink: #D27E99;
```

Or just reuse the palette names directly aligned with kanagawa.nvim:
```
--sumiInk3: #1F1F28;
--fujiWhite: #DCD7BA;
--springGreen: #98BB6C;
```

(No prefix needed if the variables are only used within our own `var()` references — Obsidian's own variables are already prefixed with `--color-`, `--background-`, etc.)

## 9. Applicable Editor Scope

Our theme needs to cover:

- **Live Preview** (CodeMirror 6) — `.cm-s-obsidian .cm-*` classes
- **Reading View** — `.markdown-rendered .*` selectors
- **Source Mode** — `.HyperMD-codeblock` etc. (legacy, but still used)
- **Canvas** — card backgrounds, borders
- **Graph** — node colors, lines, text
- **Settings UI** — modals, buttons, inputs, dropdowns
- **Workspace chrome** — titlebar, tabs, ribbon, status bar, scrollbars

## 10. Size Target

Based on analysis:
- kmkkiii: 509 lines (too minimal, missing many components)
- sspaeti: 741 lines (better coverage, mixed palette)
- Noctis Family: 850+ lines (reference quality, 11 variants)
- Minimal theme: ~400 lines (very efficient, limited styling)

**Our target: 700-900 lines** — comprehensive coverage without bloat. Palette vars (~100 lines), variant blocks (~200 lines), token mapping (~150 lines), component styles (~300 lines), syntax highlighting (~100 lines).
