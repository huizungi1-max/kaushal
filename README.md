# Kaushal — Engineering Portfolio

A single-page 3D portfolio for an embedded systems and digital hardware (FPGA / RTL) engineer.
The visitor moves through the profile one step at a time while a Three.js scene of nine plates
re-arranges itself around each section, with a hero eagle on the opening screen.

Built with Vite, TypeScript and Three.js. There is no UI framework, no backend and no runtime
data fetching: all content is rendered into static HTML at build time.

Live site: <https://tfyguiiu.vercel.app>

## Features

- **Guided 3D journey.** 23 steps across 7 sections (Origin, Capabilities, Skill Domains,
  Projects, Stack, Directions, Contact). Each step is a camera and plate composition defined in
  `src/world/compositions.ts`.
- **One gesture, one step.** Keyboard (arrows, Page Up / Down, Space, Home / End), mouse wheel,
  trackpad, touch swipe and drag are each turned into a single transition
  (`src/nav/input.ts`). The Skill Domains and Projects sections move sideways.
- **Deep links.** Each step has its own URL fragment, for example `#builds-03`. Opening such a
  link goes straight to that step; refreshing the page always returns to the first page.
- **Content as real HTML.** All copy comes from `src/content/content.ts` and is rendered into
  `index.html` at build time, so it is crawlable and readable by screen readers.
- **Works without WebGL or JavaScript.** The page falls back to a plain, readable document.
- **Accessibility.** Respects `prefers-reduced-motion`; controls are keyboard-focusable; the
  menu closes with Escape.
- **Light and dark themes.** The choice is saved in `localStorage`.
- **Optional sound.** Synthesised with the Web Audio API, off by default, no audio files.
- **Adaptive quality.** A quality tier is picked from the device and adjusted while running
  (`src/core/quality.ts`).
- **Eagle reference sheet.** `/eagle-sheet.html` shows the hero eagle from 14 views, with an
  orbit view per panel, a wing-beat animation and PNG export.

## Tech stack

| Area | Choice |
| --- | --- |
| Language | TypeScript (strict) |
| Build tool | Vite 8 |
| 3D | Three.js (WebGL renderer, CSS3D renderer for in-scene type) |
| Fonts | Archivo (variable), IBM Plex Mono, self-hosted via Fontsource |
| Visual checks | Playwright (headless Chromium) |
| Hosting | Vercel (live site); a GitHub Pages workflow is also included |

## Requirements

- Node.js 22 (the version used by the CI workflow)
- npm

No environment variables, API keys or external services are needed.

## Getting started

```bash
npm install
npm run dev        # http://127.0.0.1:5173
```

## Scripts

| Command | What it does |
| --- | --- |
| `npm run dev` | Start the Vite dev server on `127.0.0.1:5173` |
| `npm run build` | Type-check, then build the production site into `dist/` |
| `npm run preview` | Serve the built `dist/` on `127.0.0.1:4173` |
| `npm run typecheck` | Type-check only |
| `npm test` | Run the interaction smoke test (`scripts/interact.mjs`) against the dev server |
| `npm run shots` | Save screenshots of chosen steps (`scripts/shoot.mjs`) to `.shots/` |

The Playwright scripts need a Chromium build the first time:

```bash
npx playwright install chromium
```

## Project structure

```text
.
├── index.html              Main page shell; content is injected at build time
├── eagle-sheet.html        Eagle reference sheet page
├── public/                 Static files copied as-is (favicon, portrait photo)
├── scripts/                Playwright tools for testing and visual checks (not shipped)
├── src/
│   ├── main.ts             Entry point: styles, WebGL check, app start-up
│   ├── app.ts              Wires the world, director, input, UI, sound and theme together
│   ├── content/            Site copy (content.ts) and the build-time HTML renderer
│   ├── core/               Environment facts, render loop, maths, quality tiers
│   ├── nav/                Step list and gesture-to-intent input handling
│   ├── ui/                 Text choreography, menu, contact actions, headline fitting
│   ├── world/              Three.js scene: plates, camera rig, compositions, eagle, glow, type
│   ├── audio/              Optional synthesised sound
│   ├── sheet/              Eagle reference sheet
│   └── styles/             CSS, split by concern
├── vite.config.ts          Build config and the content-rendering plugin
└── .github/workflows/      GitHub Pages deploy workflow
```

## Editing content

Every word on the site lives in `src/content/content.ts`: name, academics, contact email,
skills, projects, stack and directions. The GitHub, LinkedIn and CV links under
`site.contact.links` are hidden until a URL is filled in. The portrait photo is
`public/portrait.jpg`.

## Testing and visual checks

The `scripts/` folder holds the tools used to develop the site. Each one starts its own Vite
server, so nothing needs to be running first.

| Script | Purpose |
| --- | --- |
| `interact.mjs` | Pass/fail smoke test of navigation, gestures, menu, eagle and console errors. Add `--dist` to test the production build. |
| `shoot.mjs` | Screenshots per step and viewport, e.g. `--steps 0,1,2 --vp 1440x900,390x844` |
| `audit.mjs` | Reports clipped, off-screen or overlapping text per step |
| `fallback.mjs` | Screenshots of the no-WebGL fallback |
| `intro.mjs` | Frames of the opening animation |
| `eagle.mjs` | Eagle close-ups at chosen points of its flight |
| `frame.mjs`, `transit.mjs` | Camera framing and clearance analysis |
| `sheet.mjs` | Combines screenshots into one contact sheet |

### URL parameters for captures

These are read by the site and used by the scripts above:

| Parameter | Effect |
| --- | --- |
| `step=N` | Start at step N (0–22) |
| `nointro` | Skip the opening animation |
| `q=low\|high` | Force a quality tier |
| `fixeddpr` | Turn off adaptive resolution |
| `reduced` | Behave as if reduced motion were requested |
| `theme=light\|dark` | Force a theme for the visit |
| `fly=0..1` | Hold the hero eagle at a point of its flight |
| `shot=az,el,dist,fov,sx,sy,dx,dy,dz` | Override the camera framing (dev server only) |

## Deployment

The output of `npm run build` is a static `dist/` folder that any static host can serve. Asset
paths are absolute (`/assets/...`), so the site expects to be served from the domain root.

- **Vercel (current live site):** framework preset Vite, build command `npm run build`,
  output directory `dist`.
- **GitHub Pages:** `.github/workflows/deploy.yml` builds and deploys on every push to `main`.
  To use it, set *Settings → Pages → Source* to **GitHub Actions**. Serving from a project
  path such as `/<repo>/` would also need `base` set in `vite.config.ts`.

## Browser support

The build targets ES2022 and needs WebGL for the 3D experience. Without WebGL the plain
document is shown instead. The automated checks run in headless Chromium at 1440×900 and
390×844; other browsers have not been verified by these scripts.

## Credits

- [Three.js](https://threejs.org/) — MIT License
- [Archivo](https://fontsource.org/fonts/archivo) and
  [IBM Plex Mono](https://fontsource.org/fonts/ibm-plex-mono), via Fontsource — SIL Open Font
  License 1.1
- [Vite](https://vite.dev/), [TypeScript](https://www.typescriptlang.org/) and
  [Playwright](https://playwright.dev/) for development

## License

No license has been chosen for this project yet, so all rights are reserved by the owner.
Third-party packages keep their own licenses, listed above.
