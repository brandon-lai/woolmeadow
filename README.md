# Woolmeadow

A tiny floating meadow of sheep, in one HTML file. Click a sheep, then make it idle, graze, walk, jump, move, duplicate or vanish.

Live: https://woolmeadow.vercel.app

## What it is

`index.html` is the whole product. It loads three.js r160 (and its OrbitControls / BufferGeometryUtils addons) from jsDelivr through an import map, so it needs no build step and no other assets. Everything else, including geometry, colours, water, sky, sounds and the favicon, is generated in the file.

Open it directly, or serve the folder (`python3 -m http.server`).

## Decisions

- **Name.** "Woolmeadow" is short and says what it is. It's deliberately distinct from the separate Flock project (a shared multiplayer pasture).
- **Where sheep may stand.** One function, `walkable(x, z)`, rejects anything within a margin of the stream bank, the road, the island edge, trees, bushes, rocks, fences and the origin marker. The stream and the road split the grass into four patches, and `regionOf` names them. A sheep only walks to a target in its own patch, along a straight segment sampled every 0.2 units for walkability, so it can never wade the stream or cross the road. The bridge is scenery only, because the brief allowed keeping sheep off it.
- **Move across water or road.** Move walks the sheep there if the destination is on its own patch. Anywhere else, it poofs out and pops back in at the destination. Clicking the road, the stream or a cramped spot is refused with a toast.
- **Measurement readout.** The toolbar shows the sheep's live height above the grass (the top of its wool and head bounding boxes, so it changes as it grazes and jumps), its ground distance to the pink-flagged origin marker, and its x/z. A dashed line on the ground connects the sheep to the marker while it's selected.
- **Cloud shadows.** These are not shadow-map casters. A few lines injected into the ground materials' shaders darken soft discs where each high cloud projects along the sun direction. They stay soft and cost almost nothing.
- **Camera.** A perspective camera with a 30° FOV and limits on damping, zoom and polar angle. Panning is clamped to the island. On portrait screens the island's ends are allowed to crop so the sheep stay big enough to tap.
- **Sound.** WebAudio synths only: a formant-filtered "baa" on select, pops, a boing for jumps and a bonk on refusal. Sound starts on the first gesture, and the mute choice is remembered in `localStorage`.
- **Limits.** At most 30 sheep, so it stays at 60fps on a laptop. Pixel ratio is capped at 2.

## Test hook

`window.__meadow` exists only for automated checks. It includes `simulate(seconds)`, which advances the world without rendering and counts any frame where a sheep is on forbidden ground or has changed patch. A 240-second run reports `{bad: 0, regionChanges: 0}`.
