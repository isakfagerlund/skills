---
name: visualize
description: Visualize an idea, flow or system as an illustrated carousel of pages rendered in the thread, in a chosen theme (cursor pixel art by default, simlock, or planetscale). Use when asked to visualize, illustrate, sketch or "show me" something visually.
---

# Visualize

Draw a topic as a short carousel of pages, each holding one idea in inline SVG, then render it in the thread. A theme sets the look; everything in this file holds for every theme.

## Themes

| Theme | Look | File |
| --- | --- | --- |
| `cursor` (default) | Calm, light, warm off-white, muted four-colour palette, flat tiles with a visible grid | [themes/cursor.md](themes/cursor.md) |
| `simlock` | Dark, near-black, one orange accent, hairline frames, mono labels, ordered-dither shading | [themes/simlock.md](themes/simlock.md) |
| `planetscale` | Dark engineering diagram, isometric line-art slabs, yellow active path, blue category, dotted connectors | [themes/planetscale.md](themes/planetscale.md) |

Use the theme the user names, such as "visualize this in simlock". With no theme named, use `cursor`. Read the chosen theme file before step 3. It sets the canvas, palette, type, drawing style, nav look and any extra motifs; when it disagrees with a default here, the theme wins. A theme that draws with vector shapes instead of tiles says so and replaces [Pixel tiles](#pixel-tiles).

## Steps

1. **Outline the pages.** Write one sentence per page before drawing, such as "requests arrive as tickets, get sorted into trays, and land on a calendar". Start with an overview page of the whole flow, then give each step, actor or tricky part its own page that zooms in and goes deeper. Aim for 3 to 6 pages; keep a single page only when the topic is truly one idea. Done when each sentence says something no other page says, and every element you plan to draw serves its page's sentence.
2. **Choose metaphors.** Map each actor and object to a plain physical thing from [Metaphors](#metaphors), and reuse the same sprite for the same thing on every page. Done when each one is something a child could name.
3. **Build the carousel** following [Pages](#pages), [Pixel tiles](#pixel-tiles) and the theme file. One self-contained HTML file, no external assets or fonts; the only script is the small inline carousel script. Done when every rule in the theme file holds on every page.
4. **Render it in the thread.** In T3 Code, pass the HTML to `html_preview` once per page, setting `data-page` on the deck to that page's index, and check each PNG: every theme rule holds, each sprite reads as the object it stands for, and every page that shows a flow has motion that acts it out (see [Motion](#motion)). Then call `html_render` once, with `data-page="0"` and the largest returned `contentHeight`. Elsewhere, save it as `visualize-<topic>.html` and link the absolute path. Done when the carousel is visible to the user and every page has passed its check.
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
- Each page has its own short title, so the page reads alone. Per page, one short title, a few small labels, nothing else.
- Draw the arrow buttons and the dots in the same style as the illustration, one dot per page, coloured as the theme says. Centre the nav below the pages with a page count such as `2 / 4` in secondary text. Give buttons no background or border.
- Put the background on the one canvas `<div>` and leave `html` and `body` transparent; `html_render` places the page on the thread background.
- Leave lots of empty space: the illustration takes about half the canvas, with wide margins between groups.

## Pixel tiles

Build every illustration from small square tiles. The theme sets the tile size, the gap between tiles and how shading works.

- Draw inline `<svg>` with one `<rect>` per tile, at multiples of the tile size. Keep the tile size the same across every page.
- Use `shape-rendering="crispEdges"` on the SVG.
- Keep sprites small and cute. Simple silhouettes, rounded by leaving off corner tiles.
- For larger sprites, write the sprite as rows of characters (`..bb..`) with a colour key, then generate the rects with a short script. Paste the static SVG output into the page so it renders without JavaScript.
- Leave at least two empty tiles between sprites so neighbouring shapes stay separate. Touching sprites merge into a different shape.
- Group each sprite in a `<g>` with an `id` so it can move as a unit.

## Motion

The carousel exists to teach, and motion is how it teaches. A reader who watches the request travel, the target light up and the result come back understands the flow before reading a label. Plan the motion in step 1, alongside each page's sentence.

- Give every page that shows a flow, a hand-off or a change of state one animation that acts out its sentence: the thing leaves, travels the path, arrives, and the target reacts. Only a page about a static structure stays still.
- Animate the page's sentence and nothing else. Supporting objects stay still, so the eye follows the one moving story.
- Order it as cause, then effect. Use `animation-delay` or shared keyframe percentages so the packet arrives before the target lights, and a reply returns only after the work is done.
- When the path splits, move along every branch at once, so fan-out and merge read at a glance.
- Keep loops slow and calm, 3 to 6 seconds, ending on a pause that holds the result long enough to read before the loop repeats.
- Use CSS keyframes on `transform`, `opacity`, `offset-distance` (for a packet following an `offset-path` around corners) or `stroke-dashoffset`. The theme says how motion looks, such as whole-tile steps or smooth glides.
- Wrap animation in `@media (prefers-reduced-motion: no-preference)`. Outside it, the still frame shows the end state: the path lit and the target active, with moving packets hidden.

## Metaphors

| Concept | Draw it as |
| --- | --- |
| Request, message, question | Speech bubble |
| Task, issue, job | Paper ticket |
| Queue, backlog, triage | Inbox tray, tickets stacked inside |
| Schedule, deadline, booking | Calendar grid, filled slots |
| Customer, account, tenant | Block, one tone per customer |
| Flow between steps | Row of dotted tiles, or a ticket moving along it |

Reach for the same kind of everyday object when a concept is missing from this table.

## Brand logos

When a real product appears (Linear, Slack, GitHub, Supabase), draw its logo in the same tile grid using its real shape and colour placement, so it is recognisable at a glance. Use the brand's actual colours for the logo only, even when the theme is monochrome. If you are unsure of the shape or colours, look the logo up before drawing it.
