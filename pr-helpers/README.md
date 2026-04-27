# pr-helpers

Skills that help you write and ship pull requests on GitHub.

## What's included

### `pr-description`

Drafts a PR description with a consistent structure: **Summary**, **Context**, **Why**, **What changed**, **Links & tracking**, and (when applicable) **How to test**. Avoids raw diff dumps and forces the description to actually explain *why* the change exists.

**Use it when:**
- You're opening a new PR or MR and need a body
- An existing PR has a vague or empty description
- You want a changelog-style summary of a branch's work
- The user asks for "PR text" or "what should I put in the pull request?"

### `pr-comment-resolver`

Walks through every unresolved piece of PR feedback — line comments, issue comments, and review summaries — assesses each one critically (address / defer / disagree), confirms the plan with you, then fixes source files (not generated artifacts), runs lint/format, commits, pushes, and replies on the right GitHub thread.

**Use it when:**
- A PR has reviewer feedback you need to work through
- You want to respond to code review comments without re-replying to ones already handled
- A bot or reviewer left a review summary requesting changes
- The user says "address the PR comments" or "fix the review feedback"

## When to use which

| Situation | Skill |
|---|---|
| Opening a PR | `pr-description` |
| Rewriting a weak PR body | `pr-description` |
| Reviewer left comments to address | `pr-comment-resolver` |
| Bot review summary needs responses | `pr-comment-resolver` |

The two skills are independent — invoke whichever matches the task.
