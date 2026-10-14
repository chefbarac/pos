# AGENTS.md

## Project
- Single-file vanilla JS PWA POS: `index.html` (all CSS + JS inline, ~12.5k lines). `sw.js` = service worker. No build step, no dependencies, no test framework, no linter.
- Storage: IndexedDB (`dbGet`/`dbPut` helpers). Company-scoped transactions, templates, costing, etc.

## Conventions
- DOM built with the `el(tag, attrs, children)` helper (not innerHTML); `qs`/`qsa` for lookups. 4-space indent; match surrounding style. No comments unless asked.
- Receipt feature (`openReceiptModal`): customer-facing line edits are an **image-only working copy** (`editLines`) — never mutate the saved transaction. Inline click-to-edit via `receiptInlineEdit()` (Enter commit / Esc cancel / blur commit-or-revert); PNG via `drawReceiptCanvas()` at 2× scale; clipboard write falls back to download.
- Total on receipt copy auto-recalculates from visible (non-hidden) lines.

## Required after changing index.html
- Bump `CACHE_NAME` in `sw.js` (`pos-cache-vX.Y.Z`). The SW is cache-first — without a version bump, users keep the stale app forever offline.

## Verify (no linter/tests)
- Extract inline scripts and syntax-check the main one:

```powershell
node -e "const fs=require('fs');const h=fs.readFileSync('index.html','utf8');const re=/<script(?![^>]*src)[^>]*>([\s\S]*?)<\/script>/gi;let m,i=0;while((m=re.exec(h))){i++;fs.writeFileSync(process.env.TEMP+'/s'+i+'.js',m[1]);}console.log(i)"
node --check $env:TEMP\s2.js
```

- Manual test: serve on localhost (clipboard needs a secure context), open a sale → Generate Receipt → edit lines → Copy as image → paste.

## Git
- Commit style: short lowercase phrases ("refinements", "bug fixes", "receipt inline editing...").
- Commit only when explicitly asked; never push unless asked.
