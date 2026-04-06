# Full Mode

Rebuild everything. This is the default mode for `/restructure`.

---

## What Full Mode Does

Full mode runs the complete 10-step pipeline:

1. Detect framework
2. Detect styling system
3. Scan all UI files and build UI model
4. Mark all logic for preservation
5. Strip all UI structure from every component
6. Reset design tokens
7. Load style engine
8. Apply density + grid preferences
9. Apply user prompt (if provided)
10. Rebuild all UI components

---

## Scope

Full mode touches:

| Category | Action |
|---|---|
| Layout wrappers | Rebuilt |
| Grid/flex structures | Rebuilt |
| Spacing classes | Rebuilt |
| Typography classes | Rebuilt |
| Color/token classes | Rebuilt |
| Design token files | Reset + regenerated |
| tailwind.config theme | Updated |
| globals.css variables | Reset + regenerated |
| Navigation structure | Rebuilt |
| Card structures | Rebuilt |
| Form layouts | Rebuilt |
| Table layouts | Rebuilt |

Full mode does NOT touch:

| Category | Action |
|---|---|
| Hook files | Untouched |
| API route files | Untouched |
| Data models | Untouched |
| Service files | Untouched |
| Type definitions | Untouched |
| Business logic | Untouched |
| useState/useEffect | Untouched |
| Event handlers | Untouched |

---

## Execution Order

For efficiency, execute in this order:

1. **Token files first** — reset `tokens.ts`, `theme.ts`, `tailwind.config.ts`, `globals.css`
2. **Shared layout components** — `Layout.tsx`, `Navbar.tsx`, `Sidebar.tsx`, `Footer.tsx`
3. **Page files** — process top-level pages next
4. **Component files** — leaf components last

This ensures tokens are available when rebuilding component classes.

---

## Output Format

After completing full mode, report:

```
✓ Full Redesign Complete

Framework:        Next.js App Router
Style applied:    Apple
Density:          Comfortable

Token files reset:
  ✓ tailwind.config.ts
  ✓ globals.css

Layout files rebuilt:
  ✓ app/layout.tsx
  ✓ components/Navbar.tsx

Page files rebuilt:
  ✓ app/page.tsx
  ✓ app/dashboard/page.tsx

Component files rebuilt:
  ✓ components/Card.tsx
  ✓ components/Button.tsx
  ✓ [N more]

Logic preserved:
  ✓ All hooks untouched
  ✓ All API calls untouched
  ✓ All handlers untouched
```
