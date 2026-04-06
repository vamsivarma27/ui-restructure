# Restructure Flow — Master Execution Prompt

This file defines the complete internal reasoning flow for the `/restructure` skill. Reference this when executing any restructure command.

---

## Pre-Flight Checklist

Before starting any restructure:

- [ ] Parse all arguments from the command
- [ ] Confirm parsed arguments with user (one-line summary)
- [ ] Verify you understand the codebase structure (run a quick scan)
- [ ] Confirm you will not touch logic files
- [ ] Start restructure

---

## Phase 1: Reconnaissance

### 1A — Project scan

```
Scan:
  package.json → dependencies → detect React/Next/Vue + styling libraries
  tsconfig.json → path aliases
  tailwind.config.* → current token setup
  globals.css / variables.css → CSS variable setup
  app/ or pages/ → routing model
  components/ → component inventory
```

### 1B — Build component inventory

List every UI file that will be modified. Categorize:

```
Layout files (modify first):
  - app/layout.tsx
  - components/Layout.tsx
  - components/Navbar.tsx
  - components/Sidebar.tsx
  - components/Footer.tsx

Page files (modify second):
  - app/page.tsx
  - app/[route]/page.tsx
  - pages/index.tsx

Component files (modify last):
  - components/*.tsx
  - src/components/*.tsx
```

### 1C — Logic audit

Before stripping, identify in each file:

```
Logic to preserve:
  □ useState / useReducer calls
  □ useEffect calls
  □ Custom hook calls
  □ API calls (fetch, axios, SWR, React Query)
  □ Server actions (Next.js)
  □ Event handler functions
  □ .map() / .filter() / .reduce() on data
  □ Conditional renders based on data (not layout)
  □ Form submission logic
  □ Authentication checks
```

---

## Phase 2: Stripping

For each component file:

```
Read file
↓
Identify logic blocks → mark as PRESERVE
↓
Identify layout blocks → mark as STRIP
↓
Remove STRIP blocks
↓
Verify PRESERVE blocks still intact
↓
Write stripped file
```

**Strip only these patterns:**

```
Tailwind layout:   flex, grid, gap-*, p-*, m-*, space-*, max-w-*, w-*, h-*
Tailwind colors:   bg-*, text-*, border-*, ring-*, shadow-*
Tailwind type:     text-sm, text-lg, font-*, leading-*, tracking-*
Tailwind radius:   rounded-*
Tailwind effects:  opacity-*, blur-*, backdrop-*
```

**Never strip:**

```
Key props:         key={item.id}
Data props:        {...item}, value={data.name}
Event props:       onClick={handler}, onSubmit={handleSubmit}
Conditional:       {isLoading && <Spinner />}
Data access:       item.name, user.email, product.price
Semantic tags:     <main>, <section>, <article>, <header>, <footer>
Form elements:     <form>, <input>, <select>, <textarea>
```

---

## Phase 3: Token Reset

If NOT `--keep-tokens`:

```
1. Open tailwind.config.ts
2. Find theme.extend section
3. Clear: colors, fontSize, spacing, borderRadius, boxShadow
4. Open globals.css
5. Find :root { } block
6. Clear all --color-*, --font-*, --radius-*, --shadow-* variables
```

Preserve:
```
- tailwind.config.ts: content array, plugins list, darkMode setting
- globals.css: @tailwind directives, base/components/utilities imports
```

---

## Phase 4: Token Generation

Load style engine. Extract:

```
From engine file:
  spacing.scale → generate spacing variables
  typography.scale → generate font-size variables
  color.system → generate color tokens
  shadow.scale → generate shadow tokens
  radius.scale → generate border-radius tokens
```

Apply to:

```
tailwind.config.ts → theme.extend
globals.css → :root { } CSS variables
tokens.ts (if exists) → export const tokens = { ... }
```

---

## Phase 5: Component Rebuild

For each stripped component:

```
Read stripped component
↓
Determine component role:
  - Navigation? → Apply nav pattern from engine
  - Card? → Apply card pattern from engine
  - Form? → Apply form pattern from engine
  - Table/list? → Apply list/grid pattern from engine
  - Page section? → Apply section pattern from engine
↓
Apply layout from mode file
↓
Apply tokens from style engine
↓
Write rebuilt component
↓
Verify:
  □ All original map() calls present
  □ All original handlers present
  □ All original data bindings present
  □ No hardcoded data (only original data refs)
```

---

## Phase 6: Verification

After all files rebuilt:

```
For each modified file:
  □ Open file
  □ Search for original handlers → confirm present
  □ Search for original .map() calls → confirm present
  □ Search for API calls → confirm present
  □ Run mental diff: "What did I change? Only UI."
```

If any logic is missing → immediately fix before proceeding.

---

## Phase 7: Report

Output final report:

```
✓ /restructure complete

Command parsed:
  Style:   [value]
  Mode:    [value]
  Prompt:  [value]

Changes made:
  Token files:    [count] files reset + regenerated
  Layout files:   [count] files rebuilt
  Page files:     [count] files rebuilt
  Components:     [count] files rebuilt

Logic audit:
  ✓ [N] useState calls preserved
  ✓ [N] useEffect calls preserved
  ✓ [N] API calls preserved
  ✓ [N] event handlers preserved

New design system:
  Spacing:    [engine] scale applied
  Typography: [engine] scale applied
  Colors:     [engine] palette applied
  Radius:     [engine] scale applied
  Shadows:    [engine] scale applied

Next steps:
  1. Review the changes visually in your browser
  2. Run: npm run dev
  3. If anything looks off, try: /restructure --mode theme (tokens only)
  4. To undo: git checkout -- . (if you haven't committed)
```

---

## Error Handling

### If a file cannot be determined:

Skip it, note it in the report:
```
⚠ Skipped: components/UnknownWidget.tsx (could not determine role)
   Manual review recommended.
```

### If logic is ambiguous:

When it's unclear if something is UI or logic, **keep it** (err on the side of preservation):
```
// Keeping this — may be data-driven conditional
{showSidebar && <Sidebar />}
```

### If user has custom components (e.g., shadcn):

For shadcn components, only modify the **wrapper** and **className props**, not the component internals:

```jsx
// OK to change:
<Card className="[new classes]">

// Do NOT change:
<CardHeader> → CardContent> → CardFooter> structure (shadcn internal)
```

---

## Framework-Specific Notes

### Next.js App Router

- `app/layout.tsx` is the root shell — rebuild this first
- `use client` directive must be preserved if present
- Server components: can have layout but no hooks
- Client components: full rebuild including hooks context

### Vue 3

- `<template>` section → strip and rebuild (layout)
- `<script setup>` section → preserve everything
- `<style scoped>` section → replace with new token-based styles

### styled-components

- Replace `styled.div` template literals layout properties
- Keep interpolated props and conditional logic
- Theme provider → update theme object with new engine values
