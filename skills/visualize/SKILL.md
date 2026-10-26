---
name: visualize
description: Visualize an idea, flow or system as a calm pixel-art illustration rendered in the thread. Use when asked to visualize, illustrate, sketch or "show me" something visually.
---

# Visualize

Draw one idea as a single HTML page holding inline SVG pixel art, then render it in the thread.

## Steps

1. **Find the one idea.** Write it as one sentence before drawing, such as "requests arrive as tickets, get sorted into trays, and land on a calendar". Done when every element you plan to draw serves that sentence.
2. **Choose metaphors.** Map each actor and object to a plain physical thing from [Metaphors](#metaphors). Done when each one is something a child could name.
3. **Build the page** following [Visual style](#visual-style) and [Pixel tiles](#pixel-tiles). One self-contained HTML file, no external assets, fonts or scripts. Done when every rule in Visual style holds.
4. **Render it in the thread.** In T3 Code, pass the HTML to `html_preview`, check the PNG: every Visual style rule holds, and each sprite reads as the object it stands for. Then call `html_render` with the returned `contentHeight`. Elsewhere, save it as `visualize-<topic>.html` and link the absolute path. Done when the visual is visible to the user.
5. **Caption it** in one or two sentences below the render: what the picture says, read left to right.

## Visual style

- Background `#f1f0ec`, warm off-white. Put it on one canvas `<div>` with 32px padding and `border-radius: var(--radius, 12px)`, and leave `html` and `body` transparent; `html_render` places the page on the thread background.
- Text `#393a38`, dark charcoal. Secondary text `#736f65`.
- Illustration palette, muted:
  - blue `#6b8cae`
  - forest green `#3f6b4a`
  - mustard yellow `#d4a53c`
  - warm grey `#b5aea2`
  - Use a lighter or darker step of the same hue for a second tone. Keep to these four families plus brand colours.
- Sans-serif text: `var(--font-sans, ui-sans-serif, system-ui, sans-serif)`. One short title, a few small labels, nothing else.
- Lots of empty space: let the illustration occupy about half the canvas, with wide margins between groups.
- Flat colour only. Elements sit directly on the background, with no cards, borders around groups, shadows, gradients, textures or patterned backgrounds.

## Pixel tiles

Build every illustration from small square tiles with a thin gap between them, so the grid stays visible.

- Draw inline `<svg>` with one `<rect>` per tile: a tile size of 8 and a gap of 1 means each rect is 7×7 at multiples of 8. Keep the tile size the same across the whole image.
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
