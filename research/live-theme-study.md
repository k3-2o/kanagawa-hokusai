# Live Theme Study — Dracula Official & Blue Topaz

## Dracula Official (864 lines, 20K)

**Structure:**
- Single combined `.theme-dark, .theme-light { --color-scheme: dark }` block (forces dark always)
- Flat variable declarations — no abstraction layers
- Syntax highlighting uses BOTH legacy `.cm-hmd-codeblock.cm-*` and modern `.token.*` + `.cm-s-obsidian .cm-*`
- Component styles by category: links → codeblocks → blockquotes → images → tables → sidebar → headings → tags → bold/italic → highlight → ribbons → popups → checkboxes
- Ends with print styles

**Notable patterns:**
```
.theme-dark, .theme-light {
    --color-scheme: dark;                    // force dark always
    --background-primary: #282a36;            // straight values
    --dracula-keyword: #FF79C6;               // custom prefixed vars
}
```
- Syntax highlighting: `.cm-hmd-codeblock.cm-keyword` for source mode, `.token.function` for reading view
- Uses `!important` freely (~30+ occurrences) despite guidelines saying to avoid it
- No Style Settings at all
- No multiple variants — it's just Dracula
- Old-style `.markdown-preview-view` selectors mixed with newer `.markdown-rendered`

**What to borrow:** clean sectioning, parallel syntax highlight approach, direct variable overrides
**What to avoid:** overuse of `!important`, mixing legacy and modern selectors without clear separation

## Blue Topaz (30,023 lines, 1.3MB)

**Structure:**
- Style Settings setup runs — lines 32-359 (huge config block)
- 12 color schemes via `class-select`: Default, Avocado, Monochrome, Pink, Nord, Flamingo, Honey milk, Hazelnut chocolate, Lilac, Autumn, Rubik's Cube, Simplicity
- 200+ custom CSS variables in `body {}` block (fonts, sizes, radii, backgrounds, colors, etc.)
- Per-variant color overrides via `body.color-scheme-options-* {}`
- Extensive component CSS for everything imaginable

**Notable patterns:**
```
body {
    --font-text-theme: Bookerly, 'Inter', ...;
    --font-monospace-theme: 'JetBrains Mono', ...;
    --h1-size: 1.5625em;                   // sizes in body
    --radius-m: 7px;                        // spacing variables
}

body.color-scheme-options-avocado-topaz {
    --theme-color: #7E9B76;
    // ... per-scheme colors
}
```

Key takeaway: Blue Topaz is a "theme suite" that tries to be everything. 12 color schemes, background images, blur effects, animated loading screens, custom cloze highlights, fancy cursors. Most of the 30K lines are from this bloat, not actual theme styling.

**What to borrow:** The class-select pattern for variant switching (exact same mechanism we need for wave↔dragon)
**What to avoid:** Everything else. Don't bloat with background images, loading animations, cloze highlights, or 12 color schemes. Keep it focused on faithful kanagawa port.

## Key Architecture Insight

The successful multi-variant themes (Blue Topaz, Noctis Family) all follow the same pattern:

```
body {
    // Custom palette variables (never change)
    --kanagawa-sumiInk3: #1F1F28;
}

body.variant-wave {  // applied via Style Settings class-select
    // Per-variant semantic overrides
    --kanagawa-bg: var(--kanagawa-sumiInk3);
}

.theme-dark, .theme-light {
    // Map semantic vars → Obsidian design tokens
    --background-primary: var(--kanagawa-bg);
}
```

This 3-layer approach (palette → semantic → token) is the industry standard for Obsidian multi-variant themes. We'll use exactly this.
