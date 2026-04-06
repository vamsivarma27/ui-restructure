---
name: ui-restructure
description: |
  Restructure and redesign UI from scratch without deleting project logic. Solves UI lock-in caused by hardcoded design tokens, layout structure embedded in JSX, and spacing repeated across components. Reverse engineers existing UI, extracts layout intent, preserves all business logic, API integrations, hooks, and data flow — then rebuilds the UI with a fresh design system.

  Use this skill when:
  - You want to redesign the UI without losing backend/logic code
  - AI-generated UI has become visually locked into an old structure
  - You want to apply a named design system (Apple, Linear, Minimal, Dashboard)
  - You want to convert list layouts to card grids
  - You need to reset design tokens and rebuild spacing/typography scales
  - You want a full or partial UI rebuild (layout-only, theme-only, grid-only)

  Supports: Next.js, React (Vite/CRA), Vue 3, Tailwind, CSS modules, styled-components, shadcn, plain CSS.
license: MIT
metadata:
  author: claude-ui-restructure
  version: "1.0.0"
  category: ui-design
  tags: ui,restructure,redesign,design-system,tailwind,nextjs,react,vue,tokens
argument-hint: "[--style apple|linear|minimal|dashboard] [--mode full|layout|theme|grid] [--prompt 'custom UI'] [--keep-tokens] [--grid cards] [--density compact]"
---

# Claude UI Restructure Skill

You are executing the `/restructure` skill. Your goal is to fully redesign the UI of this codebase without touching any business logic, API integrations, hooks, or data flow.

Follow the 10-step execution pipeline below. Read each step fully before acting on it.

---

## Behavior Rules (Non-Negotiable)

**NEVER modify:**
- Hooks (`useState`, `useEffect`, `useReducer`, custom hooks)
- Event handlers and callbacks
- API calls, fetch logic, server actions
- Data mapping and transformation
- Props interfaces and types
- Service layer files
- Database models
- Route handlers / API routes

**ONLY modify UI:**
- Layout wrappers and containers
- Spacing classes (`gap-*`, `p-*`, `m-*`, `space-*`)
- Typography classes
- Color/token usage
- Grid and flex structures
- Design token files

---

## Step 1 — Parse Command Arguments

Read the user's `/restructure` command and extract:

| Argument | Value |
|---|---|
| `--style` | `apple` / `linear` / `minimal` / `dashboard` / none |
| `--mode` | `full` / `layout` / `theme` / `grid` (default: `full`) |
| `--prompt` | Custom UI description string |
| `--keep-tokens` | Boolean flag — reuse existing tokens |
| `--grid` | `cards` / `list` |
| `--density` | `compact` / `comfortable` / `spacious` |

If no arguments, run full redesign.

Load the parser reference: `parser/commands.md`

---

## Step 2 — Detect Framework

Scan the project root for:

| Signal | Framework |
|---|---|
| `app/` directory + `layout.tsx` | Next.js App Router |
| `pages/` directory + `_app.tsx` | Next.js Pages Router |
| `vite.config.ts` + no `app/` dir | React (Vite) |
| `src/App.jsx` + `public/index.html` | React (CRA) |
| `*.vue` files + `vite.config.ts` | Vue 3 |

State detected framework before proceeding.

---

## Step 3 — Detect Styling System

Scan for:

| File/Signal | Styling System |
|---|---|
| `tailwind.config.*` | Tailwind CSS |
| `*.module.css` files | CSS Modules |
| `styled-components` in package.json | styled-components |
| `components.json` (shadcn config) | shadcn/ui |
| Inline `style={{}}` props | Inline styles |
| Plain `*.css` imports | Plain CSS |

Multiple systems may coexist. Record all detected systems.

---

## Step 4 — Scan UI Files

Scan these directories:
- `components/`
- `app/`
- `pages/`
- `src/`
- `layouts/`

For each UI file, build a UI model:

```
UI Model:
- Navigation type: sidebar | top-nav | bottom-nav
- Layout type: single-column | two-column | sidebar+content | full-width
- Content pattern: card-grid | list | table | dashboard | hero+sections
- Spacing scale: tight (4px base) | normal (8px base) | airy (16px base)
- Typography scale: extracted from heading classes
- Container width: max-w-* detected
- Density: compact | comfortable | spacious
- Token files: list of files
```

Output the UI model before proceeding.

---

## Step 5 — Identify Logic to Preserve

Before touching any file, scan every UI component and mark:

**PRESERVE (do not touch):**
```
// Data & state
const [items, setItems] = useState(...)
const { data } = useSWR(...)
useEffect(() => { fetchData() }, [])

// Handlers
const handleSubmit = async () => { ... }
const onDelete = (id) => { ... }

// Data mapping
{items.map(item => ( ... ))}

// API calls
const res = await fetch('/api/...')
const data = await res.json()

// Props
interface Props { userId: string; onSuccess: () => void }
```

**STRIP (remove layout bias):**
```
className="flex gap-4 p-4 max-w-7xl mx-auto"
className="grid grid-cols-3 gap-6"
className="text-sm font-medium text-gray-600"
className="rounded-lg shadow-md border border-gray-200"
```

---

## Step 6 — Strip UI Structure

For each UI component file:

1. Read the file
2. Identify all layout/spacing/typography classes
3. Remove them, leaving only semantic structure + logic

**Transformation example:**

Before:
```jsx
<div className="flex flex-col gap-6 p-8 max-w-7xl mx-auto">
  <div className="grid grid-cols-3 gap-4">
    {items.map(item => (
      <div className="bg-white rounded-xl shadow-md p-4 border border-gray-100">
        <h3 className="text-sm font-semibold text-gray-800">{item.name}</h3>
        <p className="text-xs text-gray-500 mt-1">{item.description}</p>
        <button onClick={() => handleDelete(item.id)}
                className="mt-3 px-3 py-1.5 text-xs bg-red-500 text-white rounded-md">
          Delete
        </button>
      </div>
    ))}
  </div>
</div>
```

After (logic preserved, layout stripped):
```jsx
<div>
  <div>
    {items.map(item => (
      <div key={item.id}>
        <h3>{item.name}</h3>
        <p>{item.description}</p>
        <button onClick={() => handleDelete(item.id)}>
          Delete
        </button>
      </div>
    ))}
  </div>
</div>
```

Repeat for all UI files. Do NOT strip logic. Do NOT strip semantic HTML tags. Do NOT strip key props.

---

## Step 7 — Reset Design Tokens

Find token files:
- `tokens.ts` / `tokens.js`
- `theme.ts` / `theme.js`
- `design-system.ts`
- `tailwind.config.ts` (theme.extend section)
- CSS variables in `globals.css` or `variables.css`

**If `--keep-tokens` flag is set:** Skip this step.

**Otherwise:**
- Delete or clear the existing token values
- Do NOT delete the file structure — just reset values
- Prepare for new token injection in Step 10

---

## Step 8 — Load Style Engine

Based on `--style` argument, load the corresponding engine file:

| Style | Engine File |
|---|---|
| `apple` | `engines/apple.md` |
| `linear` | `engines/linear.md` |
| `minimal` | `engines/minimal.md` |
| `dashboard` | `engines/dashboard.md` |
| none specified | Use `minimal` as default |

Read the engine file fully. It defines the complete design system to apply.

---

## Step 9 — Apply Mode

Based on `--mode` argument, load the corresponding mode file:

| Mode | File | What It Does |
|---|---|---|
| `full` | `modes/full.md` | Rebuild everything |
| `layout` | `modes/layout.md` | Change structure only, keep tokens |
| `theme` | `modes/theme.md` | Change tokens only, keep layout |
| `grid` | `modes/grid.md` | Convert lists to card grids |

Default mode is `full`.

If `--prompt` is provided, combine:
- Style engine definition
- User's prompt description
- Mode constraints

Then generate the UI accordingly.

---

## Step 10 — Rebuild UI

Using the stripped components from Step 6 + the style engine from Step 8 + the mode from Step 9:

1. **Regenerate design tokens** with new scale values
2. **Rebuild layout containers** with new structure
3. **Apply new typography scale** to all heading/text elements
4. **Apply new spacing scale** throughout
5. **Apply new color system** from style engine
6. **Rebuild component structures** using new grid/layout
7. **Write all modified files**

For each file modified, show a brief diff summary.

After completing all files, output:

```
✓ UI Restructure Complete
Framework: [detected framework]
Style: [applied style]
Mode: [applied mode]
Files modified: [count]
Logic preserved: ✓ (hooks, handlers, API calls untouched)
Tokens regenerated: ✓ / Tokens kept: ✓
```

---

## Quick Command Reference

| Command | Action |
|---|---|
| `/restructure` | Full redesign with default (minimal) style |
| `/restructure --style apple` | Apple glassmorphism style |
| `/restructure --style linear` | Linear compact style |
| `/restructure --style minimal` | Clean minimal style |
| `/restructure --style dashboard` | Data-dense dashboard style |
| `/restructure --mode layout` | Change layout structure only |
| `/restructure --mode theme` | Change tokens/colors only |
| `/restructure --mode grid` | Convert lists to card grids |
| `/restructure --keep-tokens` | Rebuild layout but keep existing tokens |
| `/restructure --grid cards` | Force card grid layout |
| `/restructure --density compact` | Apply compact spacing density |
| `/restructure --prompt "..."` | Custom redesign prompt |

---

For detailed engine and mode specs, see the `engines/` and `modes/` directories.
For command parsing details, see `parser/commands.md`.
For the full restructure flow, see `prompts/restructure-flow.md`.
