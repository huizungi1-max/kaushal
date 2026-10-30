# Contributing

Thanks for working on this project. These notes cover the local workflow and the checks a
change should pass before it is merged.

## Setup

```bash
npm install
npx playwright install chromium   # needed once, for the test and capture scripts
npm run dev                       # http://127.0.0.1:5173
```

Use Node.js 22, the version the CI workflow uses.

## Where things live

- **Copy and contact details:** `src/content/content.ts` only. The HTML is generated from it at
  build time; do not edit text into `index.html`.
- **Camera and plate layout per step:** `src/world/compositions.ts`.
- **Gesture handling:** `src/nav/input.ts`. Keep the rule that one gesture gives one step.
- **Styles:** `src/styles/`, one file per concern.

See the project structure section of `README.md` for the full layout.

## Before opening a pull request

1. `npm run build` succeeds. It type-checks with strict settings, including unused locals and
   parameters, then builds `dist/`.
2. `npm test` reports `ALL PASSED`. Run `node scripts/interact.mjs --dist` as well to check
   the production build.
3. For visual changes, compare screenshots before and after at desktop and mobile sizes:

   ```bash
   node scripts/shoot.mjs --steps 0,1,2 --vp 1440x900,390x844 --out .shots
   ```

4. Text layout changes: run `node scripts/audit.mjs --vp 1440x900,390x844` and check that
   no new issues appear.
5. Check the no-WebGL fallback with `node scripts/fallback.mjs` if you changed the page
   structure or `src/main.ts`.

## Conventions

- TypeScript in strict mode; no UI framework.
- Two-space indentation, single quotes, semicolons, trailing commas, as in the existing code.
- Animate only `transform` and `opacity` for text, and respect `prefers-reduced-motion`.
- Never commit secrets or `.env` files. The site needs no environment variables.
- Branch names in use: `feature/...`, `fix/...`, `revert/...`, `chore/...`.
