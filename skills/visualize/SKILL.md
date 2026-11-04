---
name: visualize
description: Visualize an idea, flow or system as a calm pixel-art carousel of pages rendered in the thread. Use when asked to visualize, illustrate, sketch or "show me" something visually.
---

# Visualize

Draw a topic as a short carousel of pages, each holding one idea in inline SVG pixel art, then render it in the thread.

## Steps

1. **Outline the pages.** Write one sentence per page before drawing, such as "requests arrive as tickets, get sorted into trays, and land on a calendar". Start with an overview page of the whole flow, then give each step, actor or tricky part its own page that zooms in and goes deeper. Aim for 3 to 6 pages; keep a single page only when the topic is truly one idea. Done when each sentence says something no other page says, and every element you plan to draw serves its page's sentence.
2. **Choose metaphors.** Map each actor and object to a plain physical thing from [Metaphors](#metaphors), and reuse the same sprite for the same thing on every page. Done when each one is something a child could name.
3. **Build the carousel** following [Pages](#pages), [Visual style](#visual-style) and [Pixel tiles](#pixel-tiles). One self-contained HTML file, no external assets or fonts; the only script is the small inline carousel script. Done when every rule in Visual style holds on every page.
4. **Render it in the thread.** In T3 Code, pass the HTML to `html_preview` once per page, setting `data-page` on the deck to that page's index, and check each PNG: every Visual style rule holds, and each sprite reads as the object it stands for. Then call `html_render` once, with `data-page="0"` and the largest returned `contentHeight`. Elsewhere, save it as `visualize-<topic>.html` and link the absolute path. Done when the carousel is visible to the user and every page has passed its check.
5. **Caption it** below the render with one line per page: what that page says, read left to right.

## Pages

Put all pages in one deck, stacked in the same grid cell so the canvas keeps the height of the tallest page and stays still while paging:

```html
<div class="canvas deck" data-page="0">
  <div class="pages">
    <section class="page on">…</section>
    <section class="page">…</section>
  </div>
  <nav><button class="prev" aria-label="Previous">…</button><span class="dots"></span><button class="next" aria-label="Next">…</button></nav>
</div>
```

- CSS: `.pages { display: grid }`, `.page { grid-area: 1 / 1; visibility: hidden }`, `.page.on { visibility: visible }`. The first page carries `on` in the markup so it renders before the script runs.
- Script: on load, show the page whose index is in `data-page`; prev and next buttons and the left and right arrow keys move one page; prev is disabled on the first page and next on the last.
- Each page has its own short title, so the page reads alone.
- Draw the arrow buttons and the dots in the same pixel tiles: warm grey arrows that darken on hover, one tile per page for the dots, mustard for the current page. Centre the nav below the pages with a page count such as `2 / 4` in secondary text. Give buttons no background or border.

## Visual style

- Background `#f1f0ec`, warm off-white. Put it on one canvas `<div>` with 32px padding and `border-radius: var(--radius, 12px)`, and leave `html` and `body` transparent; `html_render` places the page on the thread background.
- Text `#393a38`, dark charcoal. Secondary text `#736f65`.
- Illustration palette, muted:
  - blue `#6b8cae`
  - forest green `#3f6b4a`
  - mustard yellow `#d4a53c`
  - warm grey `#b5aea2`
  - Use a lighter or darker step of the same hue for a second tone. Keep to these four families plus brand colours.
- Sans-serif text: `var(--font-sans, ui-sans-serif, system-ui, sans-serif)`. Per page, one short title, a few small labels, nothing else.
- Lots of empty space: let the illustration occupy about half the canvas, with wide margins between groups.
- Flat colour only. Elements sit directly on the background, with no cards, borders around groups, shadows, gradients, textures or patterned backgrounds.

## Pixel tiles

Build every illustration from small square tiles with a thin gap between them, so the grid stays visible.

- Draw inline `<svg>` with one `<rect>` per tile: a tile size of 8 and a gap of 1 means each rect is 7×7 at multiples of 8. Keep the tile size the same across every page.
- Use `shape-rendering="crispEdges"` on the SVG.
- Keep sprites small and cute: 8 to 16 tiles across. Simple silhouettes, rounded by leaving off corner tiles.
- For larger sprites, write the sprite as rows of characters (`..bb..`) with a colour key, then generate the rects with a short script. Paste the static SVG output into the page so it renders without JavaScript.
- Leave at least two empty tiles between sprites so neighbouring shapes stay separate. Touching sprites merge into a different shape.
- Group each sprite in a `<g>` with an `id` so it can move as a unit.

## Motion

Animate only when movement explains the idea, such as a ticket travelling into a tray or a block filling a calendar slot.

- Use CSS keyframes on sprite groups (`transform`, `opacity`). Move in whole-tile steps with `steps()` timing so motion stays pixel-true.
- Keep loops slow and calm, 3 to 6 seconds, with a pause between cycles.
- Wrap animation in `@media (prefers-reduced-motion: no-preference)` so the still frame reads on its own.

## Metaphors

| Concept | Draw it as |
| --- | --- |
| Request, message, question | Speech bubble |
| Task, issue, job | Paper ticket |
| Queue, backlog, triage | Inbox tray, tickets stacked inside |
| Schedule, deadline, booking | Calendar grid, filled slots |
| Customer, account, tenant | Coloured block, one colour per customer |
| Flow between steps | Row of dotted tiles, or a ticket moving along it |

Reach for the same kind of everyday object when a concept is missing from this table.

## Brand logos

When a real product appears (Linear, Slack, GitHub, Supabase), draw its logo in the same tile grid using its real shape and colour placement, so it is recognisable at a glance. Use the brand's actual colours for the logo only. If you are unsure of the shape or colours, look the logo up before drawing it.
