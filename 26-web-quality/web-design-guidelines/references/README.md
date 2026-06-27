# Web Design Guidelines — Extended Reference

## Rule Rationale

### Why `<button>` not `<div onClick>`

`<div>` and `<span>` elements have no implicit ARIA role, receive no keyboard focus by default, and don't fire `click` on Enter/Space. Screen readers skip them as interactive elements. Always use `<button>` for actions and `<a>` for navigation.

### Why never `transition: all`

`transition: all` catches every CSS property, including ones that trigger layout (width, height, padding). This forces the browser to recalculate layout on every frame of the transition — causing jank. Explicitly list properties: `transition: transform 200ms, opacity 200ms`.

### Why virtualize lists > 50 items

Rendering 200+ DOM nodes per list item means thousands of nodes in the DOM simultaneously. The browser must style, layout, paint, and composite every visible node on each frame. Libraries like `@tanstack/react-virtual` or `react-window` render only the visible rows, keeping the DOM small regardless of list length.

### Why `tabular-nums` for changing numbers

Proportional numerals (the default) have different widths per digit. When numbers update (counters, prices, timers), the text reflows and adjacent elements shift. `font-variant-numeric: tabular-nums` makes all digits the same width — no layout shift on updates.

### Why `prefers-reduced-motion`

~35% of users have motion sensitivity settings enabled. Vestibular disorders, migraines, and ADHD can be triggered by rapid motion. Wrapping animations in `@media (prefers-reduced-motion: no-preference)` gives users control without removing motion from those who enjoy it.

## WCAG Quick Reference

| Level | Contrast ratio (text) | Who benefits |
|-------|----------------------|-------------|
| AA | 4.5:1 body, 3:1 large | Legal minimum in most jurisdictions |
| AAA | 7:1 body, 4.5:1 large | Low vision users |

Large text = 18pt (24px) regular or 14pt (18.67px) bold.

## Anti-Pattern Code Examples

### Bad: div onClick navigation
```jsx
// ❌ Breaks history, right-click, cmd+click
<div onClick={() => router.push("/profile")}>Profile</div>

// ✓
<Link href="/profile">Profile</Link>
```

### Bad: outline-none without replacement
```css
/* ❌ Keyboard users can't see focus */
button:focus { outline: none; }

/* ✓ */
button:focus-visible { outline: none; box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.5); }
```

### Bad: blocking paste
```jsx
// ❌ Never do this
<input onPaste={(e) => e.preventDefault()} />
```

### Bad: transition all
```css
/* ❌ */
.btn { transition: all 200ms; }

/* ✓ */
.btn { transition: transform 200ms ease, opacity 200ms ease; }
```

### Bad: unvirtualized list
```jsx
// ❌ 500 items = 500+ DOM nodes rendered at once
{items.map(item => <Row key={item.id} {...item} />)}

// ✓ with @tanstack/react-virtual
const virtualizer = useVirtualizer({ count: items.length, getScrollElement: () => parentRef.current, estimateSize: () => 48 });
```
