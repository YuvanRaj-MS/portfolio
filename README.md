# Yuvan Raj M S — Portfolio

Personal portfolio of an Embedded Systems & Hardware Engineer, built as one self-contained web page with an animated "swarm build" intro.

**Live site:** https://portfolio.rajyuvan212.workers.dev (hosted on Cloudflare)

---

## What it looks like

The page is styled like a circuit board: a dark grid, copper traces and solder pads, and IC-style labels such as `IC://ECE-2026 · STATUS ACTIVE`. The fonts are Space Mono for headings and labels and Inter for body text. The sections are:

| Section | Content |
|---|---|
| Hero | Name, role, location, contact links and a framed profile photo (`assets/yuvan-raj.jpg`) |
| Profile | A short summary |
| Pinout (Skills) | Technical and professional skills |
| Log (Experience) | PCB design, IT and web development internships |
| Build log (Projects) | SmartFarm 360 and the Automatic Medical Dispenser |
| Education & Awards | B.Tech ECE at SRM IST, Best Paper Award (ICMCCT '25) |
| Conferences, Workshops & Activities | Workshops, clubs, events and languages |
| Footer | Call to action and contact links |

## How the swarm-build intro works

When the page loads, a team of 14 coloured cursor "agents" builds the page in front of the visitor, the way designers work together in a design tool.

1. **Scan (about 0.6 s).** A dark screen shows a small logo tile. The agents circle it in two spinning arcs while the status bar at the bottom counts layers: `Scanning page… N layers`.
2. **Build.** The dark screen fades away and the page appears empty: every section, heading and paragraph is hidden. Each free agent picks the nearest unbuilt element and flies to it, then:
   - draws a **selection box** around the element, with white corner handles and a size label such as `704 × 80`;
   - **types text in** letter by letter, line by line, with a blinking caret and an I-beam cursor;
   - **wipes blocks in** from left to right (for example the copper solder pads);
   - for **containers** (sections, cards, the hero frame), turns their borders and backgrounds back on.

   Rulers appear along the top and left edges, dashed guide lines line up with each box, and every agent shows a coloured name tag (Ari, Bex, Cal…) while it works.
3. **Scroll to build.** A red **BUILD LINE** sits at 86 % of the screen height. Anything below it waits, so the rest of the page builds as you scroll down. At the very bottom of the page, whatever is left on screen is built too.
4. **Idle.** Agents with nothing to do form a slowly turning ring around your mouse pointer. If the mouse hasn't moved, they gather in an empty spot that never covers the profile photo: the side margin on wide screens, under the photo on mid-size screens, and beside it on phones.
5. **Done.** The status bar reads `Rebuilt N layers in X.Xs` and offers **Replay**.

### Controls

| Action | Result |
|---|---|
| `Esc` | Skip the build and show the whole page immediately |
| **×** in the status bar | Skip the build and hide the status bar |
| **Tour** | Auto-scroll down the page while the agents build it |
| **Replay** (after the build) | Jump to the top and run the whole intro again |

### Accessibility and fallbacks

- If the visitor's system asks for **reduced motion**, the intro is skipped and the page shows at once.
- With **JavaScript turned off**, the overlay never appears and the page is fully readable.
- Every overlay element (the dark screen and the cursor canvas) is hidden from screen readers and lets clicks pass through. Elements are hidden with `clip-path` rather than removed, so the layout never jumps.
- On phones the status bar spans the screen width and nothing scrolls sideways.

## How it's built

Everything is in **`index.html`**: plain HTML, CSS and vanilla JavaScript, with no frameworks, build step or dependencies (only Google Fonts are loaded from outside).

Under the hood:

- A full-screen `<canvas>` above the page draws the agents, selection boxes, rulers, guides and the BUILD LINE on every animation frame.
- On start, the script finds the page's elements (the "layers") and marks each with a `data-swarm` attribute (`text`, `leaf` or `frame`). CSS rules for that attribute hide them until they are built.
- Text is revealed with an animated `clip-path` polygon that follows the element's real line boxes, measured with `Range.getClientRects()`, so typing wraps exactly like the text.
- The agents move on simple spring physics (stiffness plus damping, two sub-steps per frame), so they overshoot and settle naturally.
- The timings: a box grows in about 113 ms (ease-in-out cubic), a block wipes in about 127 ms, and text types in 87–567 ms depending on its length. Finished boxes fade out within about half a second.

### Customising

All the settings are in the `CFG` object at the top of the swarm script in `index.html`:

| Setting | Default | What it does |
|---|---|---|
| `agents` | `14` | Number of cursor agents (1–20) |
| `speed` | `3` | Overall tempo; higher is faster |
| `buildLine` | `0.86` | Height of the BUILD LINE as a fraction of the screen |
| `ringRadius` | `64` | Size of the idle ring around the pointer |
| `accent` / `chip` | copper | Colours of the selection boxes and size labels |

To control which elements get built, edit `TEXT_SEL` (elements that are typed in) and `LAYER_SEL` (everything that is built).

## Running it locally

There's nothing to install. Open `index.html` in a browser, or serve the folder:

```bash
python3 -m http.server 8000
```

Then visit http://localhost:8000.

## Hosting

The site runs on [Cloudflare Workers](https://developers.cloudflare.com/workers/static-assets/) as a Worker named `portfolio` that serves the static files (`index.html` and `assets/`) from Cloudflare's global network.

Pushing to GitHub does **not** redeploy it by itself. To publish changes, either redeploy the `portfolio` Worker, or connect this repository to it once in the Cloudflare dashboard (Workers & Pages → `portfolio` → Settings → Builds) so that every push to `main` deploys automatically.

## Credits

The intro's motion is modelled on the "Swarm Build" animation in the [Cursor](https://www.framer.com/marketplace/templates/cursor/) Framer template by Will Maruiti. It was rebuilt from scratch in vanilla JavaScript and restyled to match this portfolio's copper circuit-board look.
