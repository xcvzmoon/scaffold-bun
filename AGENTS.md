# AGENTS.md

## Purpose

- This repository is a minimal Bun + TypeScript scaffold.
- Use this file as the primary repository-specific instruction set for coding agents.
- The repo currently has no `.cursor/rules/`, no `.cursorrules`, and no `.github/copilot-instructions.md`.
- If any of those files are added later, merge their guidance with this document rather than contradicting it.

## Project Snapshot

- Package manager: `bun` (`packageManager` is `bun@1.3.11`).
- Runtime/module system: ESM (`"type": "module"`).
- Language: TypeScript with strict compiler settings.
- Formatter/linter: `oxfmt` and `oxlint`.
- Test runner: `vitest`.
- Bundler/build tool: `tsdown`.
- Source layout is intentionally small and simple.

## Repository Layout

- `src/index.ts`: main runtime entrypoint.
- `src/*.ts`: source modules.
- `tests/*.test.ts`: Vitest test files.
- `tsconfig.json`: TypeScript compiler behavior.
- `tsdown.config.ts`: build configuration.
- `.oxfmtrc.json`: formatting and import sort rules.
- `.oxlintrc.json`: lint rules.
- `.github/workflows/ci.yaml`: CI behavior.
- `dist/`: build output.

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
- Generate changelog output: `bun run changelog`
- Bump changelog version: `bun run changelog:bump`
- Create changelog release notes: `bun run changelog:release`

## Test Commands

- Run all tests once: `bun run test`
- Run a single test file: `bun run test -- tests/sum.test.ts`
- Equivalent direct Vitest command: `bunx vitest run tests/sum.test.ts`
- Run tests matching a name substring: `bun run test -- tests/sum.test.ts -t "adds"`
- The `test` script already includes `--run`, so do not append another `--run` unless you intentionally bypass the script and call Vitest directly.

## Build and CI Notes

- The build command runs `bunx --bun tsdown`.
- `tsdown.config.ts` builds from `./src/index.ts`.
- The build emits declaration files and minified output.
- `bun` is marked as external in the bundle.
- Husky is installed through the `prepare` script.
- The current `.husky/pre-commit` hook runs `bun run fmt`, `bun run lint`, and `bun run test`.
- CI currently runs `bun install`, `bun run lint`, and `bun run build`.
- CI does not currently run `bun run test`.
- If you change runtime behavior, run tests locally even though CI will not catch test regressions yet.

## Formatting Rules

- Run `bun run fmt` after editing TypeScript or JSON-like files.
- `bun run fmt` writes changes first and then runs a check; expect it to modify files.
- Oxfmt is configured for single quotes.
- Let Oxfmt own whitespace, commas, semicolons, and wrapping decisions.
- Imports are sorted automatically.
- Import order is `import type`, built-in/external values, internal values, parent/sibling/index values, then unknown imports.
- `newlinesBetween` is disabled, so do not add blank lines between import groups just for style.
- `sortPackageJson` is disabled, so do not reorder `package.json` fields unless there is a reason.

## TypeScript Expectations

- Assume strict TypeScript everywhere.
- `noUncheckedIndexedAccess` is enabled, so indexed access often yields `T | undefined`.
- `noImplicitOverride` is enabled.
- `moduleResolution` is `bundler` and `verbatimModuleSyntax` is enabled.
- `noEmit` is enabled in TypeScript; builds come from `tsdown`, not `tsc`.
- This is an ESM-only repo; do not introduce CommonJS patterns.
- Avoid `require(...)`, `module.exports`, and similar legacy module code.
- `allowImportingTsExtensions` is enabled, but the existing code uses extensionless relative imports.
- Follow existing local import style unless a file already establishes a different pattern.
- Prefer straightforward functions and modules over classes.
- Avoid `namespace` and `const enum`.
- Prefer unions, literal types, and simple objects over elaborate type machinery.
- Avoid `any`, unsafe assertions, and non-null assertions when better narrowing is possible.
- Prefer `readonly` data when it clarifies API intent.
- Do not reassign function parameters.

## Imports and Exports

- Prefer named exports for reusable source modules.
- Use default exports only where the ecosystem expects them, such as config files.
- Use `import type` for type-only imports.
- Keep imports deduplicated and minimal.
- Prefer relative imports inside the repository; no path alias is configured.
- Avoid barrel files; the lint config forbids them.
- Avoid side-effect-only imports unless they are truly required.
- Avoid self-imports and circular dependency tricks.
- If you import a Node builtin, prefer the `node:` protocol.

## Naming and File Conventions

- Use `camelCase` for variables and functions.
- Use `PascalCase` for types, interfaces, and classes.
- Use `UPPER_SNAKE_CASE` only for true constants.
- Keep filenames simple and consistent with the current lowercase scaffold style.
- Put entry code in `src/index.ts`.
- Put general modules in `src/`.
- Put tests in `tests/` with the `*.test.ts` suffix.
- Prefer one main responsibility per file.

## Lint-Driven Style Guidance

- Prefer `const` over `let` whenever a value does not change.
- Use `===` and `!==`.
- Prefer template literals over string concatenation when interpolation is involved.
- Prefer optional chaining and nullish coalescing over verbose guards.
- Prefer object spread over `Object.assign` for plain object merges.
- Prefer `find`, `some`, `flat`, and `flatMap` when they express intent clearly.
- Avoid `forEach` and `reduce` when a loop or clearer helper is easier to read.
- Prefer `for...of` for straightforward iteration.
- Avoid in-place `sort()` and `reverse()` on shared arrays unless you copy first.
- Keep control flow shallow; the lint config is strict about nesting and complexity.
- Use exhaustive `switch` handling for discriminated unions.
- Avoid shadowing, duplicate imports, and empty object/function placeholders.
- Prefer `// @ts-expect-error` over `// @ts-ignore` only when suppression is unavoidable.

## Async and Error Handling

- Prefer `async`/`await` over `.then()` chains.
- Avoid mixing promises and callback-style APIs.
- Avoid `await` inside loops unless sequential behavior is required.
- Return consistent types from all branches.
- Throw `Error` instances, not strings or plain objects.
- Include a useful message when throwing.
- Preserve the original error or cause when wrapping failures.
- In `catch` blocks, treat the error as `unknown` and narrow before reading from it.
- Do not silently swallow failures.
- Do not use `process.exit()` from library code.

## Logging and Comments

- `no-console` is enabled.
- If a CLI entrypoint genuinely needs console output, keep the exception narrow and explicit.
- `src/index.ts` currently demonstrates this with `/* oxlint-disable no-console */`.
- Do not leave debug logging in library code or tests.
- Keep comments rare and useful.
- Avoid inline `//` comments in TS/JS; `no-inline-comments` is enabled.
- If a comment is needed, prefer a short block comment above the code it explains.

## Testing Guidance

- Use Vitest for unit tests.
- Import test APIs from `vitest`.
- Prefer small, deterministic, behavior-focused tests.
- Use clear test names that describe behavior, not implementation.
- Current tests import source via relative paths such as `../src/sum`.
- For a new module like `src/foo.ts`, add a corresponding `tests/foo.test.ts` when behavior changes.
- There is no custom Vitest setup file in this scaffold.
- For quick feedback, run only the affected test file first.
- Before finishing a behavior change, run at least targeted tests and `bun run lint`.

## Agent Workflow

- Read the touched file and nearby tests before editing.
- Prefer the smallest correct change.
- Do not add dependencies unless the task genuinely requires them.
- Do not introduce abstraction layers in a tiny file without immediate payoff.
- When behavior changes, add or update tests.
- After meaningful changes, run the smallest validating command set that covers the edit.
- Good default validation for a local logic change: `bun run lint` and a targeted Vitest command.
- Good default validation for broad changes: `bun run lint`, `bun run test`, and `bun run build`.
- If you skip a relevant validation command, state why.

## Current Reality

- This repo is currently a scaffold, not a large mature application.
- Most style conventions come from the formatter, linter, and compiler config more than from a large code corpus.
- Prefer the established minimal scaffold style over framework-heavy patterns.
- Keep this file synchronized with future Cursor or Copilot rule files if they are added.
