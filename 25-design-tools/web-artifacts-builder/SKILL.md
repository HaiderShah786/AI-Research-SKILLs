---
name: web-artifacts-builder
description: Provides a suite of tools for creating elaborate, multi-component Claude.ai HTML artifacts using React, Tailwind CSS, and shadcn/ui. Use for complex artifacts requiring state management, routing, or pre-built UI components — not for simple single-file HTML or JSX artifacts. Trigger when the user needs a rich interactive frontend artifact with multiple components, pages, or complex state.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [React, Tailwind CSS, shadcn/ui, HTML Artifacts, Frontend, TypeScript, Vite]
dependencies: [node>=18.0.0]
---

# Web Artifacts Builder

Build powerful, multi-component Claude.ai HTML artifacts using React 18 + TypeScript + Vite + Tailwind CSS + shadcn/ui, bundled into a single self-contained HTML file.

**Use this skill for**: complex artifacts with state management, routing, multiple shadcn/ui components, or multi-page flows.
**Do NOT use for**: simple single-file HTML/JSX artifacts — just write those directly.

## Design Warning

**VERY IMPORTANT**: Avoid what is often referred to as "AI slop" — excessive centered layouts, purple gradients, uniform rounded corners, and Inter font. Make deliberate, specific design choices.

## Quick Start Workflow

```
1. Initialize project  →  scripts/init-artifact.sh <project-name>
2. Develop artifact    →  edit generated code
3. Bundle to HTML      →  scripts/bundle-artifact.sh
4. Share artifact      →  display bundle.html to user
5. Test (optional)     →  only if requested or issues arise
```

## Step 1: Initialize Project

```bash
scripts/init-artifact.sh <project-name>
cd <project-name>
```

Creates a fully configured project with:
- React 18 + TypeScript via Vite
- Tailwind CSS 3.4.1 with shadcn/ui theming system
- Path aliases (`@/`) configured
- 40+ shadcn/ui components pre-installed
- All Radix UI dependencies included
- Parcel configured for bundling (via `.parcelrc`)
- Node 18+ compatibility (auto-detects and pins Vite version)

## Step 2: Develop the Artifact

### Project Structure

```
<project-name>/
├── index.html          # Entry point (required for bundling)
├── src/
│   ├── App.tsx         # Root component
│   ├── main.tsx        # React DOM entry
│   ├── components/     # UI components
│   │   └── ui/         # shadcn/ui components (pre-installed)
│   └── lib/
│       └── utils.ts    # cn() utility
├── tailwind.config.js
└── tsconfig.json
```

### Common Development Tasks

**Adding a page/route**:
```tsx
// src/App.tsx — simple state-based routing
const [page, setPage] = useState<"home" | "detail">("home");
return page === "home" ? <Home onNav={setPage} /> : <Detail onBack={() => setPage("home")} />;
```

**Using shadcn/ui components** (all pre-installed):
```tsx
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Input } from "@/components/ui/input";

// Full list: https://ui.shadcn.com/docs/components
```

**State management** (no external library needed for most artifacts):
```tsx
import { useState, useReducer, useContext, createContext } from "react";
```

**Tailwind CSS** — use utility classes directly:
```tsx
<div className="flex flex-col gap-4 p-6 bg-background rounded-lg shadow-sm">
  <h1 className="text-2xl font-semibold tracking-tight">Title</h1>
</div>
```

### Avoiding Common Pitfalls

- **No purple gradients** — use purposeful color choices from the design brief
- **No centered everything** — left-align body text; reserve center for display elements
- **No Inter font** — pick a deliberate typeface pair
- **No uniform border-radius** — vary or eliminate; don't round every element the same way
- **Real content** — use actual copy, not lorem ipsum

## Step 3: Bundle to Single HTML File

```bash
scripts/bundle-artifact.sh
```

Produces `bundle.html` — a fully self-contained artifact with all JavaScript, CSS, and dependencies inlined. Ready to share in Claude conversations.

**Requirements**: `index.html` must exist in the project root.

**What the script does**:
1. Installs bundling dependencies (parcel, `@parcel/config-default`, `parcel-resolver-tspaths`, `html-inline`)
2. Creates `.parcelrc` with path alias support
3. Builds with Parcel (no source maps, production mode)
4. Inlines all assets into single HTML via `html-inline`

## Step 4: Share with User

Display the `bundle.html` file content to the user as a Claude artifact. This is the deliverable.

## Step 5: Testing (Optional)

Only perform if requested or if issues arise after presenting the artifact. Use Playwright or Puppeteer with the pre-installed Chromium:

```javascript
const { chromium } = require("playwright");
const browser = await chromium.launch({ executablePath: "/opt/pw-browsers/chromium" });
const page = await browser.newPage();
await page.setContent(fs.readFileSync("bundle.html", "utf-8"));
// ... test interactions
await browser.close();
```

Do not test upfront — it adds latency between the request and when the finished artifact reaches the user.

## Common Issues

**Build fails with path alias errors**: Ensure `parcel-resolver-tspaths` is listed in `.parcelrc` and `tsconfig.json` has `paths` configured for `@/*`.

**Component not found**: All 40+ shadcn/ui components are pre-installed in `src/components/ui/`. Import from `@/components/ui/<component-name>`.

**Artifact too large**: Parcel inlines everything — large images or fonts will bloat the file. Use small base64 assets or CDN URLs (note: CDN URLs won't work in Claude's offline artifact sandbox).

**Styles not applying**: Confirm `tailwind.config.js` `content` array includes `./src/**/*.{ts,tsx}`. Purging removes unused classes.

**Blank artifact on display**: Check browser console for runtime errors — usually a missing import or `undefined` prop.

## Dependencies

- Node.js 18+ (auto-detected by init script)
- `npm` (bundled with Node)
- All React, TypeScript, Vite, Tailwind, shadcn/ui, and Radix UI deps installed by init script

## Common Development Tasks

### Multi-page Navigation (State-Based)

```tsx
type Page = "home" | "detail" | "settings";
const [page, setPage] = useState<Page>("home");

const PAGES: Record<Page, React.ReactNode> = {
  home:     <Home onNavigate={setPage} />,
  detail:   <Detail onBack={() => setPage("home")} />,
  settings: <Settings onBack={() => setPage("home")} />,
};

return <>{PAGES[page]}</>;
```

### Data Fetching Inside Artifact (Static Data)

Since the artifact is self-contained, embed data directly — no external fetch calls (they'll fail in Claude's sandbox):

```tsx
// data.ts — embed as a module
export const ITEMS = [
  { id: 1, label: "Item A", value: 42 },
  { id: 2, label: "Item B", value: 17 },
];

// Component
import { ITEMS } from "./data";
```

### Using shadcn/ui Components

All 40+ components are pre-installed. Import from `@/components/ui/`:

```tsx
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Input } from "@/components/ui/input";
import { Badge } from "@/components/ui/badge";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Dialog, DialogContent, DialogHeader, DialogTitle, DialogTrigger } from "@/components/ui/dialog";
import { ScrollArea } from "@/components/ui/scroll-area";
import { Separator } from "@/components/ui/separator";
import { Switch } from "@/components/ui/switch";
import { Tooltip, TooltipContent, TooltipProvider, TooltipTrigger } from "@/components/ui/tooltip";
```

Full component list: https://ui.shadcn.com/docs/components

### Tailwind Customization

Add custom tokens to `tailwind.config.js`:

```js
module.exports = {
  theme: {
    extend: {
      colors: {
        brand: {
          dark:   "#141413",
          light:  "#faf9f5",
          orange: "#d97757",
        },
      },
      fontFamily: {
        display: ["Playfair Display", "Georgia", "serif"],
        body:    ["Inter", "system-ui", "sans-serif"],
      },
    },
  },
};
```

### Animation with Tailwind

```tsx
// Fade in on mount
<div className="animate-in fade-in duration-500">
  <Card>...</Card>
</div>

// Slide in from bottom
<div className="animate-in slide-in-from-bottom-4 duration-300">
  Content
</div>
```

### Bundling Troubleshooting

```bash
# If bundle.html is blank — check for TypeScript errors first
npx tsc --noEmit

# If path aliases break — ensure parcel-resolver-tspaths is in .parcelrc
cat .parcelrc

# If Tailwind classes are purged — verify content array in tailwind.config.js includes all .tsx files
grep -r "content" tailwind.config.js
```

## References

**shadcn/ui component docs**: https://ui.shadcn.com/docs/components

**Advanced patterns (routing, data fetching, animations)**: See [references/README.md](references/README.md)
