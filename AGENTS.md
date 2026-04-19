# AGENTS.md

## Purpose

- Use this file as the primary repository-specific instruction set for coding agents.
- Keep it aligned with the real repository state. If other agent rule files are added later, merge guidance instead of creating conflicts.

## Project Snapshot

- Package manager: `bun` (`packageManager` is `bun@1.3.12`).
- Runtime/module system: Bun + ESM (`"type": "module"`).
- Language: TypeScript with strict mode enabled.
- Formatter: `oxfmt`.
- Linter: `oxlint` with type-aware checking.
- Test runner: `vitest`.
- Build tool: `tsdown`.
- Git hooks: Husky with a pre-commit hook that runs format, lint, and tests.
- Current codebase shape is small: one source entrypoint and one test file.

## Repository Layout

- `src/index.ts`: main source entrypoint and current exported API.
- `tests/*.test.ts`: Vitest test files.
- `package.json`: scripts, toolchain, lint-staged config, and package metadata.
- `tsconfig.json`: compiler behavior.
- `tsdown.config.ts`: build configuration.
- `.oxfmtrc.json`: formatter and import sorting rules.
- `.oxlintrc.json`: lint rules.
- `.husky/pre-commit`: local pre-commit checks.
- `.github/workflows/ci.yaml`: CI workflow.
- `dist/`: build output.
- `coverage/`: test coverage output when generated.

## Core Commands

- Install dependencies: `bun install`
- Start dev mode: `bun run dev`
- Build production output: `bun run build`
- Run built output: `bun run start`
- Install git hooks: `bun run prepare`
- Format code: `bun run fmt`
- Lint code: `bun run lint`
- Run tests: `bun run test`
- Run verbose tests: `bun run test:verbose`
- Run tests with tree reporter: `bun run test:tree`
- Dry-run release notes: `bun run release:dry`
- Create patch release notes: `bun run release:patch`
- Create minor release notes: `bun run release:minor`
- Create major release notes: `bun run release:major`

## Build And CI Notes

- The build command is `bunx --bun tsdown`.
- `tsdown.config.ts` currently builds from `./src/index.ts` with `minify: true`.
- The start script runs `bun ./dist/index.mjs`.
- CI currently runs `bun install`, `bun run lint`, and `bun run build`.
- CI does not currently run tests.
- The local pre-commit hook runs `bun run fmt`, `bun run lint`, and `bun run test`.
- `lint-staged` runs `oxfmt` for `*.{js,ts,yaml,yml}` and `oxlint` for `*.{js,ts}`.

## Formatting And Linting

- Let `oxfmt` control whitespace, wrapping, semicolons, and quote style.
- `oxfmt` is configured for single quotes.
- Imports are sorted automatically.
- Import order is `import type`, built-in or external values, internal values, parent or sibling or index values, then unknown imports.
- `newlinesBetween` is disabled, so do not add blank import-group separators just for style.
- `sortPackageJson` is disabled, so do not reorder `package.json` fields unless there is a reason.
- `CHANGELOG.md` is ignored by `oxfmt`.
- `oxlint` is type-aware and strict; expect it to enforce complexity, naming, import, and safety rules.

## TypeScript Expectations

- Assume strict TypeScript everywhere.
- `noUncheckedIndexedAccess` is enabled.
- `noImplicitOverride` is enabled.
- `moduleResolution` is `bundler` and `verbatimModuleSyntax` is enabled.
- `noEmit` is enabled in TypeScript; builds come from `tsdown`, not `tsc`.
- `allowImportingTsExtensions` is enabled, but follow the existing local import style unless a file already establishes something else.
- This is an ESM repo; do not introduce CommonJS patterns.
- Avoid `require(...)`, `module.exports`, `namespace`, and `const enum`.
- Prefer simple types, unions, and small helpers over elaborate type machinery.
- Avoid `any`, unnecessary assertions, and non-null assertions when narrowing is possible.

## Code Style

- Prefer named exports for reusable source modules.
- Use default exports only where the ecosystem expects them, such as config files.
- Use `import type` for type-only imports.
- Prefer relative imports inside the repo; no path alias is configured.
- Avoid barrel files; lint rules forbid them.
- If you import a Node builtin, prefer the `node:` protocol.
- Use `camelCase` for variables and functions.
- Use `PascalCase` for types, interfaces, and classes.
- Use `UPPER_SNAKE_CASE` only for true constants.
- Keep filenames simple and consistent with the existing lowercase style.
- Prefer `const` over `let` whenever possible.
- Prefer `for...of` over `forEach` when a loop is clearer.
- Avoid `reduce` when a loop or clearer helper communicates intent better.
- Avoid in-place `sort()` and `reverse()` on shared arrays unless you copy first.
- Keep control flow shallow and readable.
- Throw `Error` instances with useful messages.
- Treat caught errors as `unknown` until narrowed.
- Do not leave debug logging in committed code.
- Keep comments rare, short, and useful.

## Testing Guidance

- Use Vitest for tests.
- Import test APIs from `vitest`.
- Prefer small, deterministic, behavior-focused tests.
- Use clear test names that describe behavior.
- Put tests in `tests/` with the `*.test.ts` suffix.
- The current tests import from `../src`, so preserve that style unless the repo changes.
- When behavior changes, add or update tests.
- For quick feedback, run the smallest relevant test command first.

## What To Avoid

- Do not add a new formatter or linter unless the task explicitly requires it.
- Do not move tests into new directories without updating the test config includes.
- Do not commit generated coverage artifacts.
- Do not bypass `bun`; repository scripts and dependency metadata assume it.
- Do not introduce inconsistent naming or switch between type styles arbitrarily inside the same file.

## Editing Guidance

- Make the smallest correct change.
- Do not polish unrelated code.
- Do not remove correct comments or documentation.
- Do not rename broad parts of the codebase unless required.
- Do not expand a change into a repo-wide refactor unless necessary.
- Prefer leaving correct existing code in place.
- When touching production-sensitive code, prioritize reliability over clever abstractions.
- Read the touched file and nearby tests before editing.
- Do not add dependencies unless the task genuinely requires them.
- Do not introduce abstraction layers without immediate payoff.

## Before Finishing

- Run `bun run fmt` if you changed formatting significantly.
- Run `bun run lint` or at least targeted `oxlint` on touched files.
- Run targeted tests when tests exist.
- For runtime-sensitive changes, prefer a narrow smoke check over broad refactors.
- If you changed build or runtime behavior, ensure `bun run build` still works.
- If you skip a relevant validation step, say why.

## Current Reality

- This repo is still a small Bun + TypeScript package, not a large application.
- Prefer direct, simple code over framework-heavy patterns or premature abstraction.
- Keep this file synchronized with the actual repo setup as scripts, tooling, and structure evolve.
