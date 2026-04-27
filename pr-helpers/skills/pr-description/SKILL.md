---
name: pr-description
description: >-
  Drafts and refines pull request descriptions with consistent structure: context,
  motivation, summary of changes, and links to tickets or docs. Use when opening
  or updating a PR, writing a merge request description, or when the user asks
  for PR text, release notes from a branch, or a changelog-style summary of work.
license: MIT
---

# Pull request description

Write PR descriptions so reviewers understand **why** the change exists, **what** changed, and **where** to look next. Prefer plain language; avoid dumping raw diffs into prose.

## When to apply

- User asks for a PR description, MR body, or “what should I put in the pull request?”
- Summarizing work on a branch before opening a PR
- Rewriting a vague or empty PR template into something reviewable

## Required sections

Use this order. Omit a section only if it truly adds nothing (e.g. no ticket yet—say “None” or “TBD” in **Links & tracking**).

### 1. Summary

One short paragraph: what this PR does in product or technical terms.

### 2. Context

Background the reviewer needs: prior behavior, constraints, related systems, or user-facing problem. Not a repeat of the diff—explain **situation**.

### 3. Why

Motivation: problem being solved, risk being reduced, or requirement being met. If there was an alternative approach, one line on why this one was chosen (optional).

### 4. What changed

Bullets grouped by area (e.g. **API**, **UI**, **infra**). Call out breaking changes, migrations, or feature flags explicitly.

### 5. Links & tracking

- Issue / ticket / Linear / Jira (with IDs or URLs)
- Design docs, RFCs, or specs
- Related PRs (stacked or follow-ups)

### 6. How to test (when applicable)

Steps or checklist so a reviewer can validate behavior locally or in staging.

## Markdown template

Copy and fill:

```markdown
## Summary

[One paragraph: what this PR does.]

## Context

[Why this work exists; what was wrong or missing before.]

## Why

[Motivation and any notable tradeoffs.]

## What changed

- [Area]: [concise bullet]
- …

## Links & tracking

- Ticket: [URL or ID]
- Docs / design: [links]
- Related PRs: [links]

## How to test

1. …
2. …
```

## Title format

Always use `type(scope): short description`.

- **type** must be one of: `feat`, `chore`, `fix`, `refactor`
- **scope** is the topic, feature, package, or area touched (e.g. `pr-description`, `pr-helpers`, `auth`)
- **description** is a few words in imperative mood, lowercase, no trailing period

Examples:

- `feat(pr-description): add pr naming specifics`
- `refactor(pr-helpers): combine two skills into a single plugin`
- `fix(auth): handle expired refresh tokens`
- `chore(deps): bump eslint to v9`

Pick the type by intent: `feat` for new behavior, `fix` for bug fixes, `refactor` for behavior-preserving restructuring, `chore` for tooling, deps, configs, or housekeeping.

## Quality bar

- **Title**: Follows the `type(scope): description` format above. Reject vague titles like `Fix stuff` or `WIP`.
- **Scope**: Matches the diff; if the PR is large, say so and point to commits or areas.
- **Sensitive changes**: Security, data, or rollout notes called out in **What changed** or **How to test**.

## Anti-patterns

- Only listing file names with no explanation
- “WIP” or “misc fixes” as the whole description
- Missing ticket link when one exists
