# Simlock theme

Dark and technical, after the [Simlock landing page](https://incubator.callstack.com/simlock/): a near-black console, one orange accent, white type at a few opacities, square corners, 1px hairlines, mono labels, and pixel sprites shaded with ordered dithering so they glow like a low-res screen.

## Canvas and type

- Canvas background `#0a0a0a` with a 1px `#ffffff29` border, 32px padding and square corners (`border-radius: 0`). Nothing in this theme is rounded.
- Text `#ffffff`. Secondary text `#ffffff9e`. Faint text, such as hints and inactive items, `#ffffff80`.
- Title font `"Switzer", var(--font-sans, ui-sans-serif, system-ui, sans-serif)` at weight 500 with `letter-spacing: -0.01em`. Labels in `"Geist Mono", var(--font-mono, ui-monospace, Menlo, monospace)`. Neither font is loaded; the names only take effect where they are installed.
- Start each page title with its index in mono orange, such as `[01]`, then the title in white.
- Labels: mono, 10 to 11px, uppercase, `letter-spacing: 0.08em`, in faint text.
- Tag chip for state, such as `LIVE`, `FREE` or `QUEUED`: a small block of `#ff7a1a` with `#0a0a0a` mono uppercase text, 10px, `padding: 0 3px`. At most one or two per page.

## Palette

One hue, orange, plus white and greys. Lean on tone and dither, not on more hues.

| Role | Colour |
| --- | --- |
| Highlight, top edge | `#ffecdf`, `#ffb27a` |
| Body, accent | `#ff7a1a` |
| Shade | `#cb5f10`, `#b93d0b` |
| Deep shade, glow | `#8b2e08`, `#3b200d` |
| Neutral objects | `#ffffff`, `#e9e9e9`, `#5a5a5a`, `#313131` |
| Surfaces, wells | `#1a1a1a`, `#161616`, `#111111` |

Draw the subject of each page in orange and supporting objects in white or grey, so the eye goes to the orange. Tell customers or accounts apart by tone (bright orange, deep orange, white, grey) and by a short mono label.

## Tiles

- Tile size 4 with no gap: each rect is 4×4, so sprites read as solid low-res shapes rather than a visible grid.
- Sprites 10 to 24 tiles across.
- Shade every orange sprite by light from the top: its top row of tiles in a highlight tone, the body in `#ff7a1a`, the lower third and the right edge mixed with a shade tone by ordered dithering.
- Ordered dithering: blend two tones by thresholding each tile against the 4×4 Bayer matrix `[0,8,2,10, 12,4,14,6, 3,11,1,9, 15,7,13,5]`, read row by row. For a tile at column `x`, row `y` with blend amount `t` from 0 to 1, use the second tone when `(m[(y%4)*4 + x%4] + 0.5) / 16 < t`. Do this in the generator script, never by eye.
- One glow per page: a dithered pool of `#3b200d` tiles, with `#8b2e08` near the centre, under or behind the page's focal sprite, fading out with distance. Keep it out from under text, chips and wire ends so they stay readable. This is the only texture allowed on the canvas.

## Frames and wires

- A group, such as a machine and its devices, may sit in a 1px `#ffffff14` frame with square corners and a small mono label in its top-left. No fills on frames, no shadows.
- Connect things with orthogonal 1px `#ffffff29` lines: horizontal, then vertical, then horizontal, never diagonal or curved. Mark the ends with 6px solid squares.

## Motion

The console should feel live. Everything moves in whole-tile jumps with `steps()` timing, like a low-res screen redrawing.

- **Packets:** a 2×2-tile orange block that follows the wire around its corners, with `offset-path` set to the wire's path, `offset-rotate: 0deg`, and `offset-distance` stepped one tile at a time. Fade it out the moment it arrives.
- **Arrival:** draw the target twice, the grey sprite underneath and the lit orange sprite on top. Switch the lit one on as the packet lands, along with any chip such as `LIVE` or `MERGE`.
- **Replies:** when a result comes back, send a second packet along the reversed path after the target has lit.
- **Glow:** let the dither glow breathe between about 55% and 100% opacity in 4 steps over 4 to 6 seconds.
- Under reduced motion, show the lit sprites and chips and hide the packets.

## Nav

Arrows drawn in faint white tiles that turn full white on hover. Dots are single squares, `#313131` for other pages and `#ff7a1a` for the current page. Show the count in mono with leading zeros, such as `02 / 04`.
