# Figma-to-Prototype Platform Blueprint

This repo already demonstrates the renderer layer of a prototype: a state machine rail, a phone shell, a screen preview, and a specification panel. To turn it into a functional platform that uses real Figma designs, build a pipeline around that renderer instead of hand-coding each screen.

## Core principle

Use Figma as the visual source of truth, but do not treat raw Figma layers as your runtime. Import frames and node ids from Figma, then translate them into a semantic screen schema that your prototype engine can render, test, and version.

## Recommended architecture

1. Figma ingestion
   - Pull file metadata, node ids, text styles, colors, component sets, and exported assets.
   - Keep permanent links back to the design file, spec board, and state diagram.
2. Design normalization
   - Map each frame to a stable `screenId`.
   - Map named layers into slots like `status-card`, `expert-card`, `cta-primary`, or `timeline`.
   - Convert hotspots and annotations into actions or states.
3. Prototype schema
   - Store `screens`, `states`, `components`, `actions`, and `mockData` in JSON or TypeScript.
   - Keep the Figma node id on each screen so you can always trace runtime output back to design.
4. Runtime renderer
   - Render normalized screens inside reusable shells, like the current mobile mockup in `index.html`.
   - Drive transitions, overlays, and state changes from data instead of screen-specific HTML strings.
5. Review and publishing layer
   - Save versions, publish shareable URLs, show design diffs, and attach reviewer comments.

## Suggested stack

- Figma API plus webhooks for imports and updates
- A Node or Bun ingestion service
- SQLite or Postgres for screen schema and version history
- React plus Vite, or Next.js, for the interactive renderer
- Object storage for exported SVG or PNG assets

## Example schema

```json
{
  "flowId": "thank-you-pages",
  "screenId": "inspection-booked",
  "figmaNodeId": "274:133693",
  "design": {
    "figmaFileKey": "sqEfkaZxH4uaQcCKlDnILM",
    "device": "mobile",
    "componentMap": {
      "status-card": "Cards/Status/Primary",
      "expert-card": "Cards/Expert/Profile",
      "details-card": "Cards/Inspection/Details"
    }
  },
  "runtime": {
    "renderer": "thank-you-page",
    "stateKey": "inspection_booked",
    "timeline": ["booked", "assigned", "on_the_way"],
    "actions": {
      "trackExpert": {"type": "navigate", "target": "map"},
      "cancel": {"type": "openSheet", "sheet": "cancel-inspection"}
    }
  }
}
```

## How to evolve this repo

- Keep the overall shape of `index.html`: left rail for states, center preview, right rail for specs.
- Extract the hardcoded `SD`, `ETL`, `CC`, and `TS` objects into structured data modules.
- Replace string-built screens with reusable components and renderers.
- Add a Figma import layer that stores `figmaFileKey`, `figmaNodeId`, spec URLs, and exported assets for each screen.
- Add a small admin view where a user can import a Figma file and map frame layers to semantic slots.

## Practical MVP sequence

1. Import one Figma file family, such as thank-you pages.
2. Normalize those frames into a schema the renderer understands.
3. Render one clickable flow from that schema.
4. Add mock data, actions, and state transitions.
5. Add publishing, comments, and version history.

## What not to do

- Do not try to render the entire raw Figma layer tree directly as your long-term runtime.
- Do not couple visual layout changes to business state logic.
- Do not make every screen a one-off HTML file.

If you follow that structure, this repo can grow from a single handcrafted prototype into a reusable Figma-driven prototyping platform.
