# PlanetScale theme

Dark engineering diagrams, after the illustrations in PlanetScale's [The lifecycle of a sharded Postgres query](https://planetscale.com/blog/the-lifecycle-of-a-sharded-postgres-query). Objects are thin isometric slabs drawn with 1px strokes and labelled in mono caps on their top face. Two highlight colours carry the meaning: yellow marks the active path, blue marks a category. This theme draws with vector shapes, not pixel tiles, and replaces [Pixel tiles](../SKILL.md#pixel-tiles).

## Canvas and type

- Canvas background `#111111` with a 1px `#242424` border, 32px padding and square corners. Nothing in this theme is rounded.
- Text `#e7e7e2`. Secondary text `#c4c6bd`. Faint text, such as hints, `#9c9d96`.
- Title font `"Satoshi", var(--font-sans, ui-sans-serif, system-ui, sans-serif)` at weight 500 with `letter-spacing: -0.025em`. Labels and code in `"SFMono-Regular", var(--font-mono, ui-monospace, Menlo, monospace)`. Neither font is loaded; the names only take effect where they are installed.
- Slab labels: mono, bold, uppercase, `letter-spacing: 0.08em`, laid onto the slab's top face (see [Slabs](#slabs)).
- A short code fragment, such as `WHERE id = $1`, may stand in for a label. Colour SQL keywords `#fbca00`, identifiers `#74c9f0` and the rest `#e7e7e2`.

## Palette

| Role | Stroke | Top face | Side faces | Text on it |
| --- | --- | --- | --- | --- |
| Neutral object | `#4b4d4a` | `#161616` | `#1a1a1a`, `#10110f` | `#e7e7e2` |
| Active, the thing this page is about | `#fbca00` | `#3a3100` | `#28230a` | `#fbca00` |
| Category, such as every primary | `#249bda` | `#0d3348` | `#092a3c` | `#e7e7e2` |
| Platform, a group outline | `#3a3c37` | none | none | `#c4c6bd` |

Use one active (yellow) object or path per page. Use blue only for a set of objects that share a role across pages. Everything else stays neutral grey.

## Slabs

Draw each object as a flat box in 2:1 isometric projection, with inline SVG `<polygon>`s and `shape-rendering="geometricPrecision"`.

- Project a point `(x, y, z)` to screen as `sx = (x - y)`, `sy = (x + y) / 2 - z`. Draw three faces per slab: top, left side and right side, with a thickness of 6 to 10px.
- Give every face a 1px stroke in the role's stroke colour and `stroke-linejoin: round`. Draw back-to-front: slabs further up and left first.
- Put the label on the top face with `transform="matrix(1 0.5 -1 0.5 tx ty)"` for text running along the x axis, or `matrix(1 -0.5 1 0.5 tx ty)` along the y axis, so it lies flat in the plane.
- A small tab, such as a sidecar or a status, is a tiny slab sitting on a bigger slab's top face.
- Group related slabs on a platform: a large unfilled rhombus in the platform stroke, with its name in mono caps along one edge.
- Generate the polygon points with a short script from `(x, y, w, d, h)` boxes, then paste the static SVG. Never place them by eye.

## Flat diagrams

For trees, sequences or anything read top to bottom, such as a parse tree, use flat boxes instead of slabs: a rectangle with a 1px stroke and a 4px extruded edge on the right and bottom, filled with a darker step of the role colour. Keep mono labels inside. Use one style per page, isometric or flat, not both.

## Connectors

- Connect objects with dotted 1px lines, `stroke-dasharray="2 4"`, in `#4b4d4a`. Draw the active path in `#fbca00`.
- Run connectors along the isometric axes, or straight down for vertical drops between levels. Never diagonal across the plane.

## Motion

Motion here is smooth and linear, not stepped.

- Animate the active path's dots by moving `stroke-dashoffset` in the direction of travel, so the path visibly flows.
- When the flow reaches a slab, switch it from neutral or blue to yellow as the dots arrive, and let its stroke pulse once.
- For a reply, run a second dotted path back the other way in `#74c9f0` after the target has lit, so request and reply read apart.
- Reveal each path when the flow reaches its start, so the still frame does not show the route before the request takes it.

## Nav

Arrows are small flat boxes with a 1px `#4b4d4a` stroke and an extruded edge, holding a mono `←` or `→` in faint text that turns `#fbca00` on hover. Dots are 6px squares, `#3a3c37` for other pages and `#fbca00` for the current one. Show the count in mono, such as `2 / 4`.
