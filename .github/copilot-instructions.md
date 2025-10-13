<!-- .github/copilot-instructions.md -->

# Guidance for AI code agents working on this repository

Summary
- This repo is reveal.js: an HTML presentation framework. Primary runtime targets are browsers. The project builds two JS bundles (UMD for older browsers and an ESM bundle for modern browsers), several plugin bundles, and compiled CSS themes. Focus changes on `js/`, `plugin/`, and `css/`.

What to know up front
- Entry points:
  - Library entry: `js/index.js` (exports the Reveal API and wraps `js/reveal.js`).
  - Plugins live under `plugin/<name>/plugin.js` and are bundled both as UMD and ESM (see `gulpfile.js` plugin list).
  - Styles: `css/reveal.scss` (core) and `css/theme/source/*.scss` (themes).
- Build tooling: `gulp` orchestrates build, dev server, tests and packaging. Key scripts in `package.json`:
  - `npm run start` -> `gulp serve` (dev server + livereload)
  - `npm run build` -> `gulp build` (produces `dist/`)
  - `npm test` -> `gulp test` (eslint + QUnit headless via puppeteer)
- Node engine: >=18.0.0 (see `package.json`).

Architecture and important patterns
- Bundling targets:
  - UMD (broad browser support) and ES module (modern browser) bundles are produced by Rollup tasks defined in `gulpfile.js` (`js-es5`, `js-es6`). Keep compatibility concerns in `babelConfig` and `babelConfigESM` there.
- Single vs multiple presentation API:
  - `js/index.js` exposes both a new multi-instance `Deck` export (default) and a backwards-compatible single-instance API via `Reveal.initialize`. When modifying API surface, update both uses.
- Plugins:
  - Each plugin follows the pattern: default export is a factory returning a `Plugin` object with `id` and lifecycle methods (e.g., `init(reveal)`). Example: `plugin/highlight/plugin.js`.
  - Plugins are bundled twice (UMD + ESM) and live-distributed under `plugin/<name>/{name}.js` and `{name}.esm.js`.
- Styles:
  - SASS sources live under `css/` and `css/theme/source/`. `gulpfile.js` uses a custom `compileSass()` transform that calls `sass.render`. When editing theme variables, prefer `css/theme/template/*.scss` for shared mixins/settings.

Testing, linting, and CI-relevant notes
- Tests:
  - Unit/test pages live in `test/*.html` and are run in CI via `node-qunit-puppeteer` in `gulpfile.js` `qunit` task. Tests are executed against a local `gulp-connect` server on port 8009; tests require a browser-like environment (puppeteer).
- Linting:
  - ESLint is configured in `package.json` and run by the `eslint` gulp task that inspects `./js/**` and `gulpfile.js`.
- CI / badges:
  - Repo README references GitHub Actions; expect `npm test` to be used in CI.

Conventions and gotchas specific to this project
- DOM-first APIs: Many plugin hooks and core APIs expect a DOM element (e.g., Reveal constructor receives `.reveal` element). Work in the browser context when reasoning about DOM operations.
- Polyfills and targets: The build differentiates ESM and UMD via `babelConfigESM` targets — don't add polyfills that force older-browser polyfilling into the ESM bundle.
- Plugin registration: Plugins call `reveal.registerPlugin(id, plugin)` or are registered by including the plugin bundle. When adding plugin features, ensure `id` is unique and follow lifecycle (`init`, event listeners, cleanup).
- Tests depend on static server: `gulp qunit` starts a local server; tests failing locally often indicate path/root mismatches. Use `--root` arg (gulp `root` flag) if running tests from a different directory.

Files to inspect when implementing changes
- Core: `js/reveal.js`, `js/index.js` (entry), `js/*` (utilities in `utils/`)
- Build: `gulpfile.js`, `package.json` (scripts and engine), `rollup` plugins usage
- Plugins: `plugin/*/plugin.js` (highlight, markdown, math, notes, search, zoom) for canonical patterns
- Styles: `css/reveal.scss`, `css/theme/source/*`, `css/theme/template/*`
- Tests: `test/*.html` and `test/assets/`

How to run locally (quick commands)
Use PowerShell or a POSIX shell. Ensure Node >= 18 is installed.

Install deps and start dev server:
```powershell
npm install
npm run start
```

Build artifacts:
```powershell
npm run build
```

Run test suite:
```powershell
npm test
```

Small implementation contract (for code agent tasks)
- Inputs: change file(s) under `js/`, `plugin/`, or `css/`
- Outputs: updated source, updated `dist/` after `npm run build` (CI runs builds)
- Error modes: ESLint failures (run `npm test`), QUnit failures (test HTML pages)
- Success: `gulp build` completes and `npm test` exits 0

Examples (copyable patterns)
- Register plugin in code: plugins export default a factory that returns the `Plugin` object with `id` and `init(reveal)`.
  - See `plugin/highlight/plugin.js` for parsing `data-*` attributes and attaching event listeners.
- Bundling: follow `gulpfile.js` when adding new entrypoints — add rollup input and write both `.esm.js` and `.js` outputs.

When to ask humans
- Changes to public API (`js/reveal.js`, `js/index.js`), build targets, or browser support — ask before altering babel/rollup or `package.json` engines.
- Adding/removing plugins that affect packaging or distribution files in `dist/`.

Quick references
- Build tasks: `gulp build`, `gulp js`, `gulp plugins`, `gulp css-themes`, `gulp css-core`
- Dev server/watch: `gulp serve` (supports `--root`, `--port`, `--host` flags)
- Tests: `gulp test` runs `eslint` then `qunit` (connect on port 8009)

End
Please review these instructions and tell me any missing details you want included or local workflows you rely on so I can refine this file.
