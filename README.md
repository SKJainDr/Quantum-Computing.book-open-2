# Quantum Algorithms & Complexity — Online Reader

A self-contained GitHub Pages site for **Quantum Algorithms & Complexity: Shor, Grover, QFT, HHL, VQE & Quantum Complexity Theory** (Q.C. Series, Vol. II) by Dr. S. K. Jain.

This is **Volume II** of the same series as `quantum-computers-book-site` (Volume I). It deliberately uses the exact same fonts, box colors, heading styles, and reader UI as Volume I, so the two feel like one consistent series rather than two different-looking sites. If you're publishing both, consider linking between them (e.g. a "More in this series" link) once both are live.

## Design note: matched to Volume I on purpose

Volume II's source `.docx` actually uses a slightly different (though related) color palette for its pedagogical boxes than Volume I's — e.g. its Key Concept boxes are a slightly different purple, its Warning boxes a slightly different orange. **This site intentionally overrides those with Volume I's exact colors and typography** rather than extracting Volume II's own, per your request that the two books read as part of the same series. If you'd ever prefer Volume II to use its own native palette instead, that's a small, isolated change (the color values are all in `assets/css/style.css`).

## Structural differences from Volume I (handled automatically)

Volume II's source document is organized differently from Volume I's in two ways worth knowing about:

- **Chapter banners aren't a heading style.** In Volume I, each chapter's title banner paragraph was tagged with Word's "Heading 1" style. In Volume II, the banner is a bare paragraph with direct formatting (bold, colored, sized by hand) — the literal marker text is just `CHAPTER 3`, `CHAPTER 4`, etc. The conversion script detects these directly rather than relying on the Heading 1 style.
- **Section-heading styles are inconsistent between chapters.** Some chapters (1, 2, 9, 10) tag their major sections ("1.1", "9.1") as Heading 1, while others (3–8) tag the equivalent sections as Heading 2 — with recurring headers like "RECAP" sometimes using yet another style. Rather than trust the raw Word style name, the converter assigns heading depth by **order of first appearance within each chapter**: whichever style shows up first after the chapter title becomes that chapter's "major section" level, the next distinct style becomes "subsection," and so on. A short list of recognizable recurring headers (RECAP, References, chapter-end appendix blocks like "A. Solved Problems") are always pinned to the major-section level regardless of their raw style, since they're meant to sit as peers to the numbered sections. The result is a uniform heading hierarchy across every chapter even though the source wasn't uniform.

## What's inside

- `index.html` — the reader shell (sidebar TOC, topbar controls, reading pane, on-page TOC)
- `assets/css/style.css` — dark/light theme (CSS variables, toggle persists via `localStorage`) — matched to Volume I
- `assets/js/app.js` — chapter loading & routing, on-page TOC generation, read-aloud, visitor counter, like button
- `content/*.md` — the book itself, one Markdown file per chapter, generated from your `.docx` source
- `content/manifest.json` — the chapter list that drives the sidebar (edit titles/order here)

## Features

- **Dark / light theme** — toggle in the top bar, remembers your choice (light is default — it's the book's actual printed appearance)
- **Read aloud** — uses the browser's built-in Web Speech API (no external service, works offline once loaded). Play/pause, stop, and a speed selector (0.8×–1.75×). The paragraph currently being read is highlighted and auto-scrolled into view, and it automatically advances to the next chapter when one finishes.
- **Clickable navigation** — every chapter link, every subsection in the on-page TOC, and Prev/Next chapter buttons are deep-linkable, so you can share a link straight to a section
- **Filter box** in the sidebar to quickly jump to a chapter
- **Visitor counter** (sidebar footer) and **like button** (top bar, red heart)
- Responsive: collapses to a slide-out sidebar on mobile

## Cross-linking the series

The sidebar has a "More in this series" section linking to the sibling volume — but since each book is a separate GitHub Pages site, this repo has no way to know the sibling's URL automatically. The link points to `#` (inert) until you fill it in.

**After you've deployed both volumes**, open `assets/js/app.js`, find the `SERIES_LINKS` constant near the top of the visitor-counter/like-button section, and replace the placeholder `url: "#"` with the sibling's real GitHub Pages URL, e.g.:

```js
const SERIES_LINKS = [
  { label: "Volume II — Quantum Algorithms & Complexity", url: "https://your-username.github.io/quantum-algorithms-book-site/" },
];
```

If you add more volumes to the series later, add more entries to this same array — each renders as its own link.

## Visitor counter & like button

Backed by [Abacus](https://abacus.jasoncameron.dev), a free counting API needing no signup or key. Volume II uses its own counter namespace (`qc-series-vol2-skjain`, set in `assets/js/app.js` as `COUNTER_NAMESPACE`) so its visitor/like counts are tracked separately from Volume I's — they will not share a total.

- **Visitor counter**: increments once per page load, shown in the sidebar footer.
- **Like button**: click once to like — it turns solid red and the count increments, remembered via `localStorage` so it can't be clicked repeatedly. No "unlike" (Abacus only supports anonymous increments, not decrements).

**Verify this actually works once deployed.** This was built and tested in a sandboxed environment with no outbound internet access, so the code's graceful-failure path (showing "—") was verified, but the live API calls were not. Click the like button on your deployed site and refresh to confirm the count persists.

## Publishing to GitHub Pages

1. Create a new GitHub repository (e.g. `quantum-algorithms-book-site`, to sit alongside your Volume I repo).
2. Copy everything in this folder into the repo root and push:
   ```bash
   git init
   git add .
   git commit -m "Quantum Algorithms & Complexity — online reader"
   git branch -M main
   git remote add origin https://github.com/<your-username>/<repo-name>.git
   git push -u origin main
   ```
3. In the repo on GitHub: **Settings → Pages → Source → Deploy from a branch → `main` / `(root)`** → Save.
4. Your book will be live at `https://<your-username>.github.io/<repo-name>/` within a minute or two.

### Testing locally before you push

Opening `index.html` directly by double-clicking it will **not** work — browsers block `fetch()` of local files for security reasons. Serve the folder instead:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## About the text conversion — please proofread

Converted from your `.docx` using the same pipeline built for Volume I: figures extracted and placed at their original captions (37 in this book), pedagogical boxes (Anecdote, Key Concept, Real World, Warning, Example, Solved Problem) detected from the document's table structure and recolored to match Volume I, code blocks fenced from their monospace source formatting, and the chapter/heading-hierarchy fixes described above.

A few things worth a skim:

- **Equations**: this book's equations are typed as inline Unicode math in the text (⟨ψ|, Σ, ⊗, superscripts) rather than embedded MathType objects like some of Volume I's — they display fine as-is but aren't true typeset math.
- **No "math" or "equation" boxes**: Volume II's source doesn't use the Mathematics/Key-Equation box types Volume I had a few of, so you won't see those styles here — nothing missing, just not used in this book.
- One image (`image1.jpeg`, the dedication photo) was initially being silently dropped by the converter because its paragraph happened to be tagged with a heading style in the source despite containing only an image, no text — a genuine bug, now fixed and verified showing correctly on the dedication page. (Checked and confirmed Volume I's site is not affected by this same issue — it uses different underlying logic that didn't have the bug.)

None of this is destructive — the `.md` files are plain text you can hand-edit directly, same as Volume I.
