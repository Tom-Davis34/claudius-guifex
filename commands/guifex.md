Drive the design-first UI TDD workflow for a single React component: $ARGUMENTS

## Configuration

Read `.claude/claudius-guifex.json` from the repo root before doing anything
else. If it is missing, STOP and ask the user to create it with this shape:

```json
{
  "componentsDir": "src/components",
  "tokensStylesheet": "src/styles/tokens.css",
  "testCommand": "your-test-command",
  "typecheckCommand": "your-typecheck-command",
  "renderer": "storybook",
  "storybookCommand": "your-storybook-command",
  "storybookUrl": "http://localhost:PORT",
  "storybookMcpUrl": "http://localhost:PORT/mcp"
}
```

If your repo uses `"renderer": "playwright"` instead, see README.md §3 for
the equivalent shape (no Storybook keys, no MCP URL).

Below, `<componentsDir>`, `<testCommand>`, etc. mean the corresponding values
from that file.

This command extends superpowers:test-driven-development with two gates. Follow
the phases in order. The gates are hard stops — the human signs off at each.

## Phases

1. **AUTHOR.** Create the component folder
   `<componentsDir>/<Component>/`. Using the `/claudius-guifex:writing-component-specs`
   skill, write `<Component>.spec.md` (States table + Gherkin stories). Using the
   `/claudius-guifex:writing-component-mockups` skill, write one `mockups/<state>.html` per state
   id.

   The spec's `## Responsive` section and fluid mockups are part of AUTHOR —
   not optional extras.

2. **GATE #1 — design review.** Use the `/claudius-guifex:reviewing-component-design` skill.
   Dispatch the design reviewer, surface its verdict, and STOP for the human's
   sign-off. **Write no test or production code until signed off.**

3. **RED.** Using superpowers:test-driven-development, write
   `<Component>.test.tsx` with **≥1 test per state id and ≥1 test per story id**,
   the id embedded in each test name (`state:<id>` / `US-N:`). Before moving on,
   verify the count: every States-table id and every `US-N` has a matching test.
   Run `<testCommand>` and watch the new tests FAIL.

   Do not write responsive/viewport tests here — jsdom cannot do layout, so
   they would assert class names, not behaviour. Responsiveness is verified
   at both gates, where rendering is real.

4. **GREEN.** Write the minimal `<Component>.tsx` (+ `<Component>.module.css`,
   `index.ts` re-export) to pass. Run `<testCommand>` until green. Run
   `<typecheckCommand>`.

   **Responsive Iron Law** — the mockup is fluid; the component must be too:
   - Mobile-first: author the narrow layout, widen with `min-width` queries.
   - No fixed px widths/heights on layout containers; `max-width`, `%`,
     `clamp()`, `minmax()`.
   - `flex-wrap` on any row that can get tight; `min-width: 0` on flex
     children holding text.
   - `img`/`video`: `max-width: 100%`, `height: auto`.
   - Never absolute positioning for layout.

5. **REFACTOR.** Clean up; stay green.

6. **PREVIEW.** Depends on `<renderer>`:
   - `storybook` — write `<Component>.stories.tsx` with one story per state
     id. `<testCommand>` runs story play-tests in the Storybook browser
     project — keep it green.
   - `playwright` — using the
     `/claudius-guifex:writing-component-playwright-harness` skill,
     scaffold the project-wide harness route if it doesn't exist yet, then
     write `<Component>.harness.tsx` with one entry per state id.

7. **GATE #2 — fidelity review.** Depends on `<renderer>`:
   - `storybook` — use the `/claudius-guifex:fidelity-storybook` skill.
     Start `<storybookCommand>`, dispatch the fidelity reviewer (structural
     via Storybook MCP + visual via Playwright).
   - `playwright` — use the `/claudius-guifex:fidelity-playwright` skill.
     Start `<harnessCommand>`, dispatch the fidelity reviewer (structural +
     visual, both via Playwright against the harness route).

   Either way: surface the per-state `structure | visual | responsive` table
   (each state compared at widths 320/768/1280 with an element-level
   overflow sweep), and STOP for the human's sign-off. Fix the component on
   mismatch or responsive failure. **Not done until signed off.**

## Rules
- Existing flat components: migrate into the folder only when running this on
  them; add `index.ts` re-export so imports stay `components/<Component>`.
- Run everything through the project's configured commands, never raw npm/npx.
- Both gates: the subagent advises, the human signs off. Never auto-proceed.
- "Looks perfect at one width" is not done. Gate 2 checks 320/768/1280; a
  fixed-width component will mismatch its fluid mockup as width grows.
