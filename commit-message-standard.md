# Commit Message Standard

> This document is based on [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/). It standardizes Git commit messages in this repository so humans can understand changes quickly and tools can parse commits for changelogs, release notes, semantic versioning, CI/CD rules, and traceability.

## 1. Goals

Every commit message should be:

1. **Human-readable**: team members can quickly understand the change type, scope, and purpose.
2. **Machine-parseable**: automation can generate changelogs, release notes, release versions, or workflow triggers.
3. **Traceable**: requirements, bugs, PRs, issues, and breaking changes can be explicitly connected.
4. **Focused**: each commit should represent one coherent and explainable change.

## 2. Basic format

Use this format:

```text
<type>[optional scope][optional !]: <description>

[optional body]

[optional footer(s)]
```

In practical terms:

```text
feat(auth): add passkey login
fix(api): handle empty request body
docs: update deployment guide
feat(api)!: remove v1 user endpoint
```

## 3. Header rules

The header is the first line of the commit message and is required.

```text
<type>[optional scope][optional !]: <description>
```

### 3.1 `type` is required

`type` describes the primary category of the change.

This repository uses lowercase English commit types for consistency and stable tooling.

| Type | Meaning | Default SemVer impact | Example |
|---|---|---:|---|
| `feat` | New feature, new capability, or new user/system-visible behavior | MINOR | `feat(auth): add passkey login` |
| `fix` | Bug fix, incorrect behavior fix, or defect repair | PATCH | `fix(api): handle empty request body` |
| `docs` | Documentation-only change | None by default | `docs: update deployment guide` |
| `test` | Add or update tests | None by default | `test(order): add refund edge cases` |
| `refactor` | Code restructuring without behavior change | None by default | `refactor(user): simplify profile mapper` |
| `perf` | Performance improvement | None by default | `perf(search): reduce index lookup time` |
| `style` | Formatting, whitespace, lint-only, or style-only changes | None by default | `style: format checkout module` |
| `build` | Build system, dependency management, package configuration | None by default | `build: upgrade vite config` |
| `ci` | CI/CD, pipeline, or automation configuration | None by default | `ci: add pull request test job` |
| `chore` | Maintenance work that does not fit another category | None by default | `chore: remove unused script` |
| `revert` | Revert an earlier commit | Depends on reverted change | `revert: revert login throttling change` |

Rules:

- Use `feat` for new functionality or new user/system-visible behavior.
- Use `fix` for bug fixes.
- Other types are allowed, but they should not trigger SemVer version bumps by default unless they include a breaking change.
- If a change fits multiple unrelated types, split it into multiple commits.

### 3.2 `scope` is optional but recommended

`scope` describes the module, domain, or code area affected by the change.

Format:

```text
<type>(<scope>): <description>
```

Recommended scopes are short nouns, for example:

| Scope | Meaning |
|---|---|
| `auth` | Authentication, login, authorization |
| `api` | API layer or interface protocol |
| `db` | Database, migration, schema |
| `ui` | User interface |
| `checkout` | Checkout flow |
| `order` | Order domain |
| `payment` | Payment domain |
| `config` | Configuration |
| `deps` | Dependencies |
| `release` | Release-related change |
| `docs` | Documentation area |
| `test` | Test framework or test tooling |

Examples:

```text
feat(auth): add passkey login
fix(payment): prevent duplicate capture
test(api): add contract tests for orders
```

Scope rules:

- Prefer lowercase.
- Prefer nouns over long phrases.
- The scope should identify a repository area, business domain, or system module.
- If the correct scope is unclear, omit it rather than inventing a meaningless one.

### 3.3 `!` marks a breaking change

If the change introduces incompatible behavior, add `!` after `type` or `type(scope)` and before the colon.

```text
feat!: require OAuth for all API requests
feat(api)!: remove v1 user endpoint
fix(config)!: change default cache policy
```

When using `!`, the description should communicate the core impact. For details, also add a `BREAKING CHANGE:` footer.

```text
feat(api)!: remove v1 user endpoint

BREAKING CHANGE: Clients must migrate to `/v2/users` before deploying this release.
```

### 3.4 `description` is required

`description` is a short summary immediately after the colon and one space.

```text
fix(auth): reject expired reset tokens
```

Recommended rules:

- Be short, specific, and understandable.
- Describe what changed, not just “update” or “fix bug”.
- Use English descriptions in this repository unless a task explicitly requires another language.
- Do not end the description with a period.
- Do not include too many implementation details in the description; put details in the body.

Recommended:

```text
feat(report): add monthly revenue export
fix(auth): prevent expired token reuse
docs(api): document pagination parameters
```

Not recommended:

```text
update
fix bug
feat: big update
fix: auth and docs and ci
```

## 4. Body rules

The body is optional and explains context for more complex changes.

Add a body when any of the following is true:

- The reason for the change is not obvious.
- The change affects architecture, data models, APIs, permissions, or business rules.
- The fix addresses a complex bug.
- There are migration, rollback, compatibility, or risk notes.
- A tradeoff or alternative needs to be explained.

The body must be separated from the header by a blank line.

Example:

```text
fix(order): prevent duplicate refund creation

The refund handler could process two retry events for the same payment event.
This change stores the provider event ID before executing the refund mutation so
later retries are ignored.
```

A good body answers:

1. Why is this change needed?
2. What behavior changed?
3. Are there important edge cases, limitations, or risks?
4. Are there migration, rollback, or compatibility notes?

## 5. Footer rules

Footers are optional and record structured metadata such as breaking changes, issue links, PR references, reviewers, or co-authors.

Footers must be separated from the body by a blank line. If there is no body, footers must be separated from the description by a blank line.

Recommended footer format:

```text
Token: value
```

Common footers:

```text
Refs: #123
Fixes: #456
Closes: #789
Reviewed-by: Jane Doe
Co-authored-by: Jane Doe <jane@example.com>
BREAKING CHANGE: The public API now requires an explicit tenant ID.
```

Footer token rules:

- Avoid spaces in footer tokens; use hyphens, for example `Reviewed-by`.
- `BREAKING CHANGE` is the special exception; it may contain a space and must be uppercase.
- `BREAKING-CHANGE` is equivalent, but this repository standardizes on `BREAKING CHANGE`.

## 6. Breaking change rules

A breaking change is a change that breaks existing callers, users, integrations, deployment flows, or compatibility assumptions.

Common breaking changes include:

- Removing or renaming a public API.
- Changing API request or response structures.
- Removing a configuration option or changing a default configuration value.
- Changing a database schema in a way that requires migration.
- Removing a compatibility path, legacy protocol, or old behavior.
- Changing the permission model, authentication method, or security constraint.
- Changing package exports, command-line arguments, or environment variable semantics.

A breaking change must be declared with at least one of these methods.

Method 1: use `!` in the header.

```text
feat(api)!: remove v1 user endpoint
```

Method 2: use a `BREAKING CHANGE:` footer.

```text
feat(api): remove v1 user endpoint

BREAKING CHANGE: Clients must migrate to `/v2/users`.
```

Recommended: use both `!` and `BREAKING CHANGE:` for clarity.

```text
feat(api)!: remove v1 user endpoint

BREAKING CHANGE: Clients must migrate to `/v2/users` before deploying this release.
```

## 7. SemVer mapping

When the project uses Semantic Versioning, map commits to version increments as follows:

| Commit content | Recommended version increment |
|---|---:|
| Contains `BREAKING CHANGE` or `!` in the header | MAJOR |
| `feat` without breaking change | MINOR |
| `fix` without breaking change | PATCH |
| `docs`, `test`, `refactor`, `style`, `build`, `ci`, `chore`, `perf` | No default SemVer bump unless breaking |

Notes:

- Whether `perf` triggers a version bump depends on the project release policy. Conventional Commits 1.0.0 explicitly defines the SemVer relationship for `feat`, `fix`, and breaking changes.
- Application projects that do not expose public APIs should still keep this mapping for release notes and change-risk assessment.

## 8. Examples

### 8.1 Feature

```text
feat(auth): add passkey login
```

```text
feat(report): add monthly revenue export

Adds a CSV export endpoint for monthly revenue reports and wires the action into
the admin reporting page.
```

### 8.2 Bug fix

```text
fix(api): handle empty request body
```

```text
fix(order): prevent duplicate refund creation

The refund handler could process two retry events for the same provider event.
The handler now records the event ID before executing the refund mutation.

Fixes: #482
```

### 8.3 Documentation

```text
docs: update deployment guide
```

```text
docs(api): document pagination parameters
```

### 8.4 Tests

```text
test(order): add refund edge cases
```

```text
test(auth): cover expired password reset tokens
```

### 8.5 Refactor

```text
refactor(user): simplify profile mapper
```

```text
refactor(api): extract request validation middleware

Moves repeated validation logic into a shared middleware without changing API behavior.
```

### 8.6 Performance

```text
perf(search): reduce index lookup time
```

### 8.7 CI

```text
ci: add pull request test job
```

```text
ci(release): publish artifacts after tag builds
```

### 8.8 Breaking change

```text
feat(api)!: remove v1 user endpoint

BREAKING CHANGE: Clients must migrate to `/v2/users` before deploying this release.
```

### 8.9 Revert

```text
revert: revert login throttling change

This reverts commit 1a2b3c4 because it blocks legitimate login retries from mobile clients.
```

## 9. Anti-patterns and corrections

| Not recommended | Problem | Recommended |
|---|---|---|
| `update` | Missing type; cannot be parsed | `chore: update project metadata` |
| `fix bug` | Missing standard format and context | `fix(auth): prevent expired token reuse` |
| `feat: big update` | Too vague | `feat(report): add monthly revenue export` |
| `fix: auth and docs and ci` | Multiple unrelated changes in one commit | Split into `fix(auth): ...`, `docs: ...`, `ci: ...` |
| `Feat(Auth): Add Login` | Inconsistent casing | `feat(auth): add login` |
| `feat(api): remove v1 endpoint` | Breaking change not declared | `feat(api)!: remove v1 endpoint` plus a footer |

## 10. Repository practices

### 10.1 One commit should express one intent

Recommended split:

```text
fix(auth): reject expired reset tokens
test(auth): add expired reset token coverage
docs(auth): document reset token expiration
```

Not recommended:

```text
fix(auth): fix reset token and add docs and tests and ci changes
```

### 10.2 Squash commit messages must also follow this standard

If a PR contains temporary commits such as:

```text
wip
try again
fix tests
```

The final squash merge commit must be rewritten into a standard message:

```text
fix(auth): reject expired reset tokens
```

### 10.3 Split PRs or commits when unrelated changes are mixed

If one PR contains several unrelated changes, split the PR when possible. If they must stay in one PR, each commit should still be independent and standard-compliant, and the PR description should explain why the work is bundled.

### 10.4 AI-generated or Codex-generated changes must also comply

When Codex, subagents, or other AI tools generate code changes, the final commit message remains the committer's responsibility and must satisfy:

- Correct `type`.
- Reasonable `scope`.
- Clear `description`.
- Explicit breaking change marker when needed.
- Relevant issue, PR, test, or release information in the body or footer when useful.

## 11. Commit message checklist

Before committing, check:

- [ ] Does the first line match `<type>[optional scope][optional !]: <description>`?
- [ ] Is `type` one of the allowed types in this standard?
- [ ] Does a new feature use `feat`?
- [ ] Does a bug fix use `fix`?
- [ ] Is a breaking change marked with `!` or `BREAKING CHANGE:`?
- [ ] Does the scope accurately describe the module or domain?
- [ ] Is the description specific rather than `update`, `change`, or `fix bug`?
- [ ] Does a complex change include a body explaining reason, impact, and risk?
- [ ] Are `Refs:`, `Fixes:`, `Closes:`, or similar footers needed?
- [ ] Could multiple intentions be split into multiple commits?

## 12. Optional automated validation

You may use commitlint to validate commit messages. The following is an optional configuration example.

### 12.1 Install

```bash
npm install --save-dev @commitlint/cli @commitlint/config-conventional
```

### 12.2 `commitlint.config.cjs`

```js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [
      2,
      'always',
      [
        'feat',
        'fix',
        'docs',
        'test',
        'refactor',
        'perf',
        'style',
        'build',
        'ci',
        'chore',
        'revert',
      ],
    ],
    'type-case': [2, 'always', 'lower-case'],
    'scope-case': [2, 'always', 'lower-case'],
    'subject-empty': [2, 'never'],
    'subject-full-stop': [2, 'never', '.'],
    'header-max-length': [2, 'always', 100],
  },
};
```

### 12.3 Validate a message manually

```bash
npx commitlint --from HEAD~1 --to HEAD
```

## 13. Recommended Codex prompt for commit messages

When asking Codex to generate a commit message for a set of changes, use:

```text
Based on this repository's commit-message-standard.md, generate a Conventional Commits style commit message for the current staged changes.

Requirements:
- Use the format: <type>[optional scope][optional !]: <description>
- Decide whether a body and footer are needed.
- If there is a breaking change, mark it with ! or BREAKING CHANGE:.
- If the changes contain multiple unrelated intentions, recommend splitting them into multiple commits.
- Output one recommended commit message and briefly explain the choices for type, scope, and description.
```

## 14. Quick templates

### Simple change

```text
<type>(<scope>): <description>
```

### Change with body

```text
<type>(<scope>): <description>

<Explain why this change is needed and what behavior changed.>
```

### Change with issue reference

```text
<type>(<scope>): <description>

Refs: #<issue-number>
```

### Breaking change

```text
<type>(<scope>)!: <description>

BREAKING CHANGE: <Explain the incompatible behavior and required migration.>
```
