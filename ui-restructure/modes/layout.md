# Layout Mode

Change structural layout only. Tokens and colors are preserved.

---

## What Layout Mode Does

Layout mode runs a **partial pipeline**:

1. ✓ Detect framework
2. ✓ Detect styling system
3. ✓ Scan UI files and build UI model
4. ✓ Mark all logic for preservation
5. ✓ Strip layout/structure classes only
6. ✗ Skip token reset (tokens preserved)
7. ✓ Load style engine (layout rules only)
8. ✓ Rebuild layout containers
9. ✗ Skip color/typography rebuild

---

## Scope

Layout mode touches:

| Category | Action |
|---|---|
| `flex` / `grid` classes | Rebuilt |
| Container widths (`max-w-*`) | Rebuilt |
| Navigation structure | Rebuilt |
| Column configurations | Rebuilt |
| Sidebar presence/absence | Rebuilt |
| `gap-*` spacing classes | Rebuilt |
| `p-*` / `m-*` on containers | Rebuilt |

Layout mode does NOT touch:

| Category | Action |
|---|---|
| Color classes (`bg-*`, `text-*`, `border-*`) | Untouched |
| Typography size/weight | Untouched |
| Design token files | Untouched |
| Shadow classes | Untouched |
| Radius classes | Untouched |
| Business logic | Untouched |
| Hooks and handlers | Untouched |

---

## When to Use Layout Mode

Use `--mode layout` when:

- The color scheme and tokens are already good, but the page structure feels wrong
- You want to switch from single-column to sidebar+content layout
- You want to change navigation position (top → side → bottom)
- You want to change content arrangement (stacked → grid)
- The spacing between sections feels cramped or too loose

---

## Layout Patterns Available

Each style engine defines its preferred layout. In layout mode:

| Style | Layout Applied |
|---|---|
| apple | Top nav + centered content + floating panels |
| linear | Fixed left sidebar + header bar + content area |
| minimal | Top nav + max-width container + section-based |
| dashboard | Fixed sidebar (256px) + top bar + card grid |

If no `--style` is specified in layout mode, infer the best layout from the existing content structure (dashboard-like content → dashboard layout, marketing content → minimal layout).

---

## Output Format

```
✓ Layout Restructure Complete (layout mode)

Framework:        React (Vite)
Style applied:    Linear (layout rules only)
Colors/tokens:    Preserved

Layout files rebuilt:
  ✓ src/App.tsx           (top nav → left sidebar)
  ✓ src/layouts/Main.tsx  (single column → sidebar + content)

Component files rebuilt:
  ✓ src/components/Nav.tsx
  ✓ src/components/Shell.tsx

Untouched:
  → tailwind.config.ts    (tokens preserved)
  → globals.css           (tokens preserved)
  → All component colors  (preserved)
```
