# Changelog

## Unreleased
- **Breaking:** renamed the plugin from `ui-component-tdd` to
  `claudius-guifex`. The command is now `/guifex`, skills are invoked as
  `/claudius-guifex:<skill>`, and the consumer config file moved from
  `.claude/ui-component-tdd.json` to `.claude/claudius-guifex.json` (no
  fallback — rename your config file). Uninstall the old plugin and reinstall as `claudius-guifex@tomdavis`.
- Initial extraction of the UI Component TDD skill suite from the home-organization repo.
- Add a `renderer` config option (`"storybook"` | `"playwright"`) so Gate 2 and
  the PREVIEW phase can run without Storybook. Renamed
  `comparing-mockups-to-storybook` to `fidelity-storybook` and added
  `fidelity-playwright` and `writing-component-playwright-harness`.
