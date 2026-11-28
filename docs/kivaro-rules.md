# Kivaro ESLint Rules

This document summarizes the ESLint setup of kivaro, the earlier project. It serves as input for the linting decision of Run My Day. All file paths are relative to the kivaro repository root.

## Configuration

- Kivaro has one ESLint config for the whole monorepo.
- `eslint.config.ts` at the root only re-exports `@kivaro/static-checks/eslint`.
- The real config is `packages/static-checks/src/eslint-config.ts`, a flat config built with `defineConfig` from `eslint/config`.
- The config is split into helper modules in `packages/static-checks/src/`: `layer-imports.ts`, `layer-classes.ts`, `color-value-restrictions.ts`, `function-names.ts`, `syntax-restrictions.ts`, `playwright-configs.ts`, `repository-test-restrictions.ts`, `test-folder-configs.ts`, and `ticket-file-configs.ts`.
- No app or package has its own ESLint config, `.eslintrc*` file, or `eslintConfig` key in `package.json`.
- The custom rules live in a local plugin at `packages/static-checks/src/eslint-rules/local-plugin.ts`, registered under the prefix `local/`.

Packages and versions, from the root `package.json`:

| Package | Version | Use |
| --- | --- | --- |
| `eslint` | ^10.13.0 | Linter |
| `@eslint/js` | ^10.0.1 | `recommended` config |
| `typescript-eslint` | ^8.72.0 | `recommended` config and parser |
| `eslint-plugin-react-hooks` | ^7.1.1 | `flat.recommended` config for `.tsx` files |
| `@vitest/eslint-plugin` | ^1.6.28 | Test rules |
| `eslint-plugin-playwright` | ^2.12.2 | End-to-end test rules |
| `@eslint/markdown` | ^8.0.3 | Lints Markdown files with the `markdown/gfm` language |
| `eslint-config-prettier` | ^10.1.8 | Turns off formatting rules, applied last |
| `jiti` | ^2.7.0 | Loads the TypeScript config file |

Parser options and file scopes:

- `**/*.{js,cjs,mjs,ts,tsx}` uses `@eslint/js` recommended and `typescript-eslint` recommended.
- `**/*.tsx` uses the React Hooks recommended config.
- `**/*.{ts,tsx}` uses `parserOptions: { projectService: true }` for type-aware rules, and carries the main rule set.
- `**/*.test.{ts,tsx}` and `**/tests/**` add the Vitest and test rules.
- `e2e/**` adds the Playwright rules, except unit and boundary tests and two fixture files.
- `e2e-manual/**` declares browser globals such as `document`, `fetch`, and `localStorage`.
- `**/*.md` uses the `markdown/gfm` language with YAML frontmatter, except `backlog/archive/**` and `e2e-manual/results/**`.
- Ignored paths: `node_modules`, `dist`, `dist-local-prd`, `routeTree.gen.ts`, `apps/server/env.d.ts`, `components/ui` (generated UI components), `coverage`, the native mobile folders, and all `*.config.*` files except `apps/web/vite.config.ts`.
- The scripts are `lint` (`eslint . --fix`) and `lint:check` (`eslint . --max-warnings 0`).

## Built-in and plugin rules

### Core ESLint rules (all `**/*.{ts,tsx}` unless stated)

| Rule | Setting | What the rule enforces |
| --- | --- | --- |
| `no-console` | error | Code does not call `console`. Files matching `packages/static-checks/src/**/check-*.ts` may call `console.error`. |
| `max-depth` | error, 2 | Blocks nest at most two levels deep. |
| `no-lonely-if` | error | An `if` is not the only statement inside an `else`. |
| `no-else-return` | error | No `else` follows an `if` block that returns. |
| `complexity` | error, 10 | A function has a cyclomatic complexity of at most 10. |
| `max-params` | error, 3 | A function has at most three parameters. The rule is off in `apps/server/src/**/*.middleware.ts`. |
| `max-lines-per-function` | error, max 20, skip blank lines and comments | A function has at most 20 lines. The rule is off in test files. |
| `max-lines` | error, max 200, skip blank lines and comments | A file has at most 200 lines. |
| `max-nested-callbacks` | error, 3 (4 in test files) | Callbacks nest at most three levels deep. |
| `id-denylist` | error, `e`, `err`, `cb`, `fn`, `req`, `res` | Identifiers do not use these short forms. |
| `no-restricted-syntax` | error, selector lists | Bans the syntax patterns listed below. |
| `no-restricted-imports` | error, per layer | Restricts imports between server layers. The section "Boundary and architecture rules" lists the patterns. |
| `no-restricted-properties` | error | Repository boundary tests do not read the `database` property. |
| `max-classes-per-file` | error, 1 | A server layer file (service, controller, repository, router, client, middleware) has one class. |

The `no-restricted-syntax` list for all TypeScript source files rejects:

- A `??` chain inside another `??`, and an `||` chain inside another `||`.
- A function call inside a logical expression that is returned.
- An `if` statement nested inside another `if` statement.
- A function call inside a compound `if` condition.
- Four calls nested as arguments, such as `a(b(c(d())))`.
- An arrow function assigned to a variable whose name does not start with a capital letter or an approved verb.
- Generator functions and `yield` expressions.
- A ternary passed as a call argument.
- A ternary whose branches are anything other than identifiers or literals.

Extra `no-restricted-syntax` entries apply to some files:

- Server controllers and middleware may not call `.parse` or `.safeParse`. Request parsing belongs in the request file of the feature.
- `packages/api-contract/src/**/*.ts` may not declare interfaces or type literals. Each contract shape is a Zod schema.
- `apps/web/src/**` and `apps/web/vite.config.ts` may not read the Vite mode (`import.meta.env.MODE` or a `mode` identifier). The build must behave the same in every Vite mode.

### typescript-eslint

| Rule | Setting | What the rule enforces |
| --- | --- | --- |
| `@typescript-eslint/no-floating-promises` | error | Each promise is awaited or handled. |
| `@typescript-eslint/no-unused-vars` | error, `argsIgnorePattern: '^_'`, `varsIgnorePattern: '^_'` | Unused variables are errors unless the identifier starts with `_`. |
| `@typescript-eslint/no-explicit-any` | error | Code does not use the `any` type. |
| `@typescript-eslint/no-magic-numbers` | error, ignore `0`, `1`, `-1` | Other number literals must be named constants. The rule is off in test files. |
| `@typescript-eslint/naming-convention` | error, function selector with a custom regex | A function identifier starts with a capital letter or with one of about 90 approved verbs, such as `get`, `is`, `has`, `create`, `parse`, and `render`. In server layer files, a class also ends with the layer suffix, such as `UserService` in a `.service.ts` file. |

### Vitest (`**/*.test.{ts,tsx}` and `**/tests/**`)

| Rule | Setting | What the rule enforces |
| --- | --- | --- |
| `vitest/max-expects` | error, max 1 (off in `e2e/**`) | Each test has at most one `expect`. |
| `vitest/max-nested-describe` | error, max 2 | `describe` blocks nest at most two levels deep. |
| `vitest/valid-title` | error, `mustNotMatch` for titles over 40 characters | An `it` or `test` title has at most 40 characters. |

### Playwright (`e2e/**`)

- `playwright/no-raw-locators` is set to error with `allowed` set to `html`, `body`, `link[rel="manifest"]`, `audio`, `svg`, `[aria-current="page"]`, and `[aria-current="true"]`. Tests locate elements by role or text, and only these raw selectors are allowed.
- These rules are set to error. They ban unreliable waits, skipped or focused tests, conditionals, and weak assertions, and they require web-first assertions:
  - Structure: `consistent-spacing-between-blocks`, `max-nested-describe`, `no-duplicate-hooks`, `no-export`, `prefer-hooks-in-order`, `prefer-hooks-on-top`, `require-hook`, `valid-describe-callback`.
  - Waiting and timing: `missing-playwright-await`, `no-networkidle`, `no-wait-for-navigation`, `no-wait-for-selector`, `no-wait-for-timeout`, `no-action-timeout`, `no-magic-timeouts`, `no-useless-await`, `require-to-pass-timeout`.
  - Test selection: `no-focused-test`, `no-skipped-test`, `no-slowed-test`, `no-duplicate-slow`, `no-commented-out-tests`, `no-page-pause`, `require-annotation-reason`, `valid-test-tags`.
  - Logic in tests: `no-conditional-expect`, `no-conditional-in-test`, `no-standalone-expect`, `no-test-return-statement`, `no-nested-step`, `no-unsafe-references`, `no-eval`.
  - Locators: `no-element-handle`, `no-force-option`, `no-nth-methods`, `no-unused-locators`, `prefer-locator`, `prefer-native-locators`.
  - Assertions: `expect-expect`, `max-expects`, `no-unnecessary-assertions`, `no-useless-not`, `prefer-comparison-matcher`, `prefer-ending-with-an-expect`, `prefer-equality-matcher`, `prefer-strict-equal`, `prefer-to-be`, `prefer-to-contain`, `prefer-to-have-count`, `prefer-to-have-length`, `prefer-web-first-assertions`, `require-to-throw-message`, `valid-expect`, `valid-expect-in-promise`.
  - Titles: `no-identical-title`, `no-template-literal-title`, `prefer-lowercase-title`, `valid-title`.
- These rules are set to off: `no-hooks`, `no-get-by-title`, `no-restricted-matchers`, `no-restricted-locators`, `no-restricted-roles`, `require-soft-assertions`, `require-tags`, `require-top-level-describe`.

### React Hooks and shared configs

- `eslint-plugin-react-hooks` uses its `flat.recommended` config for `.tsx` files, with no rule overrides.
- `@eslint/js` recommended and `typescript-eslint` recommended apply with no overrides beyond the rules above.
- `eslint-config-prettier` comes last and turns off rules that conflict with Prettier.

## Custom rules

Kivaro has 25 custom rules in `packages/static-checks/src/eslint-rules/`. Each rule has a unit test in `packages/static-checks/tests/eslint-rules/<rule>.unit.test.ts`. Rule tests use ESLint's `RuleTester`. Nine rules are built with a `createSelectorRule` helper (`selector-rule.ts`) that turns a list of AST selectors and messages into a rule. None of the custom rules take options unless stated.

### Code structure rules

**`local/max-lines-per-test`** (`max-lines-per-test.ts`)
- Limits the lines of code in each `it` or `test` callback.
- Setting: `['error', 30]` in test files. The option is a required integer for the line limit.
- Rejects: an `it('adds a task', () => { ... })` callback with 31 lines of code.
- Allows: the same callback with 30 or fewer lines.

**`local/no-blank-lines-in-short-functions`** (`no-blank-lines-in-short-functions.ts`)
- Disallows blank lines inside a function body with few lines of code. The rule auto-fixes by removing the blank line.
- Option: an integer for the line count, default 3.
- Rejects: `function add(a, b) {\n  const sum = a + b;\n\n  return sum;\n}`.
- Allows: the same function without the blank line.

**`local/private-functions-below-exports`** (`private-functions-below-exports.ts`)
- Requires private (not exported) functions to come after the last export of the file. The rule auto-fixes by moving the functions, with their comments and `eslint-disable` and `eslint-enable` pairs.
- Rejects: `function helper() {}\nexport function run() { helper(); }`.
- Allows: `export function run() { helper(); }\nfunction helper() {}`.
- A function read at module load time stays above the exports.

**`local/require-destructured-params`** (`require-destructured-params.ts`)
- Requires a function with many parameters to take one destructured object instead.
- Option: an integer for the minimum parameter count, default 3.
- Rejects: `function move(from, to, speed) {}`.
- Allows: `function move({ from, to, speed }) {}`, a rest parameter, and a callback passed directly as a call argument.
- The rule is off in server middleware files.

**`local/no-empty-returns`** (`no-empty-returns.ts`)
- Disallows functions that return `null` or `undefined`.
- Scope: `apps/server/src`, `packages/static-checks/src`, `packages/backlog-tools/src`, and `packages/factory/src`.
- Rejects: `function find(): User | null { return null; }`.
- Allows: returning an empty list, throwing an error, or a type predicate.

**`local/require-layer-class`** (`require-layer-class.ts`)
- Requires a class in each server service, controller, repository, router, or client file.
- Scope: `apps/server/src/**/*.{service,controller,repository,router,client}.ts`.
- Rejects: a `user.service.ts` file with only functions.
- Allows: `export class UserService { ... }`.

**`local/require-literal-log-message`** (`require-literal-log-message.ts`)
- Requires a string literal as the message of `logDebug`, `logInfo`, `logWarn`, and `logError` calls.
- Rejects: ``logInfo(`Saved ${count} tasks`)``.
- Allows: `logInfo('Saved tasks', { count })`.

**`local/no-color-values`** (`no-color-values.ts`)
- Disallows hex, `rgb`, `rgba`, `hsl`, `hsla`, and `oklch` color values outside the theme files.
- Scope: `apps/web/src/**` and `apps/mobile/src/**`, except `apps/mobile/src/theme/use-theme-colors.ts`.
- Rejects: `const style = { color: '#ff0000' };`.
- Allows: a color read from the theme, such as `colors.primary`.

### Naming rules

**`local/no-abbreviated-units`** (`no-abbreviated-units.ts`)
- Disallows abbreviated units, such as `Ms`, `Sec`, `Min`, `Hr`, `Kb`, `Mb`, and `Gb`, at the end of declared names.
- Rejects: `const timeoutMs = 500;` and `const TIMEOUT_MS = 500;`.
- Allows: `const timeoutMilliseconds = 500;`.

**`local/no-record-or-row-type-names`** (`no-record-or-row-type-names.ts`)
- Disallows `Record` and `Row` in class, type, interface, and enum names.
- Rejects: `interface UserRow {}`.
- Allows: `interface User {}`.

**`local/no-without-type-names`** (`no-without-type-names.ts`)
- Disallows `Without` in class, type, interface, and enum names.
- Rejects: `type FileWithoutReadAt = {}`.
- Allows: `type ScannedFile = {}`.

**`local/no-vague-name-nouns`** (`no-vague-name-nouns.ts`)
- Disallows the vague nouns `Hit`, `Hits`, `Outcome`, and `Outcomes` as words in any declared name.
- Rejects: `const valueHit = find();`.
- Allows: `const envValueLocation = find();`.

**`local/require-boolean-function-name`** (`require-boolean-function-name.ts`)
- Requires a function that returns `true` or `false` to have a name that starts with `is` or `has`.
- The rule detects a boolean function by a `boolean` return type, a returned comparison or negation, or a returned call to a boolean method such as `includes`.
- Rejects: `function checkReady(task) { return task.done === true; }`.
- Allows: `function isReady(task) { return task.done === true; }`.

### Comment and prose rules

**`local/no-comments`** (`no-comments.ts`)
- Disallows comments other than `eslint-` directives, `@ts-` directives, and shebang lines.
- Option: `{ allowInTestBodies: boolean }`. Test files set the option to `true` to allow step comments inside `it` and `test` bodies.
- Rejects: `// add the task` above a statement in source code.
- Allows: `// eslint-disable-next-line no-console`.

**`local/no-step-kind-comments`** (`no-step-kind-comments.ts`)
- Disallows a test comment that says only `setup`, `action`, or `check`.
- Rejects: `// setup`.
- Allows: `// create a task with a due date`.

**`local/no-bare-pronouns`** (`no-bare-pronouns.ts`)
- Disallows the pronouns `it`, `its`, `them`, `they`, and `their` in comments and in `it`, `test`, and `describe` titles.
- Rejects: `it('returns it when they match')`.
- Allows: `it('returns the task when the ids match')`.

**`local/no-forbidden-words`** (`no-forbidden-words.ts`)
- Disallows a fixed list of words in comments and test titles.
- The words include "by hand", "cover", "guess", "holds", "keep", "leave", "lists", "named", "naming", "nobody", "skip", and "whose", with their other verb forms.
- Rejects: `it('skips empty rows')`.
- Allows: `it('ignores empty rows')`.

**`local/no-run-on-sentences`** (`no-run-on-sentences.ts`)
- Limits each sentence in comments and test titles by word count and by clauses joined with a comma plus "which", "so", "and", or "but".
- Option: a required object `{ maxJoinedClauses, maxWords }`. Kivaro sets `{ maxJoinedClauses: 1, maxWords: 30 }`.
- Rejects: `// Saves the task, and logs the id, so the user sees it.`
- Allows: `// Saves the task and logs the id.`

### Test structure rules (test files)

**`local/no-code-in-describe-body`** (`no-code-in-describe-body.ts`)
- Allows only `describe` and `it` calls in a `describe` body.
- Rejects: `describe('when empty', () => { const list = []; it(...); })`.
- Allows: setup moved into a helper or a top-level hook.

**`local/require-describe-condition`** (`require-describe-condition.ts`)
- Requires each `describe` title to start with "when", "with", or "without".
- A top-level `describe` that contains only `describe` calls may state the subject instead.
- Rejects: `describe('task list', () => { it(...); })`.
- Allows: `describe('when the list is empty', () => { it(...); })`.

**`local/no-restricted-test-syntax`** (`no-restricted-test-syntax.ts`)
- Disallows `if`, ternaries, `switch`, and the `&&`, `||`, and `??` operators in test files. Tests use `node:assert` for narrowing.
- Disallows a call result passed directly into another call, and a callback written inline inside `expect(...)`.
- Disallows parameterized tests with `it.each`, `test.each`, `describe.each`, and `.for`.
- Rejects: `expect(parse(input)).toEqual(task);`.
- Allows: `const parsed = parse(input);\nexpect(parsed).toEqual(task);`.

### Markdown rules (`**/*.md`)

The Markdown rules skip ticket files with `status: Done` in the frontmatter.

**`local/no-bare-pronouns-markdown`** (`no-bare-pronouns-markdown.ts`)
- Applies the bare pronoun check to Markdown text and YAML frontmatter.

**`local/no-forbidden-words-markdown`** (`no-forbidden-words-markdown.ts`)
- Applies the forbidden word check to Markdown text and YAML frontmatter. URLs are excluded.

**`local/no-run-on-sentences-markdown`** (`no-run-on-sentences-markdown.ts`)
- Applies the run-on sentence check to Markdown paragraphs, table cells, and frontmatter values.
- Option: `{ maxJoinedClauses: 1, maxWords: 30 }`, the same as the code rule.

**`local/no-skill-step-numbers-markdown`** (`no-skill-step-numbers-markdown.ts`)
- Disallows a ticket that cites a skill step by number.
- Scope: `backlog/tasks/**/*.md` and `backlog/drafts/**/*.md`.
- Rejects: "Follow step 3 of the release skill."
- Allows: "Follow the 'Tag the release' step of the release skill."

## Boundary and architecture rules

- Kivaro enforces the server layers with `no-restricted-imports` patterns in `packages/static-checks/src/layer-imports.ts`.
- Kivaro does not use `eslint-plugin-boundaries`, `import/no-restricted-paths`, or dependency-cruiser.
- The layers are files in `apps/server/src/` with the suffixes `.controller.ts`, `.service.ts`, `.repository.ts`, `.middleware.ts`, and `.domain.ts`.

| Layer file | May not import | Reason in the error message |
| --- | --- | --- |
| `*.controller.ts` | `*.repository`, `zod` | Import the repository port from the domain file. Parse the request in the request file of the feature. |
| `*.service.ts` | `*.controller` | Only the container imports a controller. |
| `*.repository.ts` | `*.repository`, `*.controller`, `*.service` | A repository does not call a service. |
| `*.middleware.ts` | `*.repository`, `*.controller`, `zod` | Same reasons as the controller. |
| `*.domain.ts` | any relative import that is not another `.domain` file | A domain file imports only other domain files. |

The allowed direction is controller to service to domain, with the repository implementing a port declared in the domain file. Only the container wires controllers. Related layer rules:

- `apps/server/tests/**/*.repository.boundary.test.ts` may not import `drizzle-orm` or `src/database/*` (except `database.client`), and may not read a `database` property. A repository test calls repository methods only.
- Each server layer file has exactly one class, with the class suffix that matches the file suffix (`max-classes-per-file` and `@typescript-eslint/naming-convention`).
- `local/require-layer-class` requires the class in service, controller, repository, router, and client files.

## Related tooling

- Prettier: shared config in `packages/static-checks/prettier.json` (single quotes, semicolons, width 100, `trailingComma: es5`, `arrowParens: avoid`, `proseWrap: always`), referenced from the root `package.json` `prettier` key, with `.prettierignore` at the root.
- lint-staged: the root `package.json` runs Prettier, `eslint --fix`, Prettier again, `eslint --max-warnings 0`, and `secretlint` on every staged file.
- Husky: `.husky/pre-commit` runs custom checks, lint-staged, the type check, and the unit tests. `.husky/commit-msg` runs a custom commit message check. `.husky/pre-push` blocks pushes to `main` and `develop`. `.husky/post-checkout` and `.husky/post-merge` reinstall packages.
- commitlint: not used. Commit messages are checked by the custom script `check-commit-message` in `packages/static-checks`.
- Knip: not used.
- secretlint: `secretlint` ^13.0.7 with `@secretlint/secretlint-rule-preset-recommend` runs in lint-staged.
