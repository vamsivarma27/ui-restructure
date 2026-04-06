# Grid Mode

Convert list-based layouts to card grid layouts. Preserves data mapping and logic.

---

## What Grid Mode Does

Grid mode runs a **targeted pipeline**:

1. ✓ Detect framework
2. ✓ Scan for list patterns
3. ✓ Identify items rendered in vertical lists
4. ✓ Preserve all map/loop logic
5. ✓ Convert list container to grid container
6. ✓ Convert list item to card structure
7. ✓ Apply card design from style engine

---

## Scope

Grid mode touches:

| Category | Action |
|---|---|
| List containers (`flex flex-col gap-*`) | Converted to grid |
| `<ul>` / `<ol>` wrappers | Converted to grid div |
| `<li>` items | Converted to card divs |
| Horizontal list rows | Converted to cards |
| Table-like custom rows | Converted to cards |

Grid mode does NOT touch:

| Category | Action |
|---|---|
| `.map()` logic | Untouched |
| Data passed to items | Untouched |
| Item event handlers | Untouched |
| Actual table elements | Untouched (tables stay tables) |
| Business logic | Untouched |
| Token files | Untouched |

---

## List Detection Patterns

Scan for these patterns to identify convertible lists:

```jsx
// Pattern 1: flex-col container with mapped items
<div className="flex flex-col gap-*">
  {items.map(item => <Row key={item.id} {...item} />)}
</div>

// Pattern 2: explicit ul/li
<ul className="space-y-*">
  {items.map(item => <li key={item.id}>...</li>)}
</ul>

// Pattern 3: vertical stack
<div className="space-y-*">
  {items.map(item => (...))}
</div>
```

---

## Grid Conversion

### Default: Cards grid

Convert to responsive card grid:

```jsx
// Before
<div className="flex flex-col gap-4">
  {items.map(item => (
    <div className="flex items-center p-3 border-b">
      <span>{item.name}</span>
      <button onClick={() => handleClick(item.id)}>View</button>
    </div>
  ))}
</div>

// After (logic fully preserved)
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
  {items.map(item => (
    <div className="bg-white border border-neutral-200 rounded-lg p-4 hover:shadow-md transition-shadow">
      <span>{item.name}</span>
      <button onClick={() => handleClick(item.id)}>View</button>
    </div>
  ))}
</div>
```

### With `--grid cards`:

2–4 column responsive grid with styled cards.

### With `--density compact`:

Tighter grid (4–5 columns), smaller card padding.

---

## Grid Column Rules

| Screen | Columns (default) | Columns (compact) |
|---|---|---|
| Mobile (< 640px) | 1 | 2 |
| Tablet (640–1024px) | 2 | 3 |
| Desktop (1024–1280px) | 3 | 4 |
| Wide (1280px+) | 4 | 5 |

---

## Card Structure

When converting a list item to a card:

1. Identify the data displayed (name, description, status, image, etc.)
2. Map data to card regions:
   - **Header:** Primary identifier (name, title, ID)
   - **Body:** Secondary data (description, metadata)
   - **Footer:** Actions (buttons, links)
3. Apply card styling from the active style engine

---

## Output Format

```
✓ Grid Conversion Complete (grid mode)

Files converted:
  ✓ app/products/page.tsx     (list → 3-col card grid)
  ✓ components/UserList.tsx   (ul/li → card grid)
  ✓ app/orders/page.tsx       (vertical stack → 2-col card grid)

Logic preserved in all conversions:
  ✓ .map() loops untouched
  ✓ onClick handlers untouched
  ✓ Item data props untouched

Grid applied:
  Columns: 1 / 2 / 3 (responsive)
  Card style: minimal border
  Density: comfortable
```
