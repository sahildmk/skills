---
name: pr-comment-resolver
description: |
  Resolve pull request feedback by critically assessing each item, fixing source files (not generated files), regenerating derived artifacts, running lint/format, committing, pushing, and replying on the appropriate GitHub thread. Use when the user asks to address PR feedback, respond to reviewer suggestions, fix issues from code review, or resolve GitHub review comments, PR issue comments, or review summaries.
---

# PR Comment Resolver

Resolve unaddressed PR feedback on a GitHub PR by fixing the underlying source code, verifying the fix, committing, pushing, and replying on the appropriate thread.

## Inputs

The user will provide one of:
- A PR number (e.g. `#689`)
- A PR URL (e.g. `https://github.com/org/repo/pull/689`)
- A request to look at "the PR" (infer from the current branch)

If no specific comment is mentioned, address all unresolved PR feedback.

## Workflow

### 1. Identify the PR and fetch PR feedback

```bash
# If on a feature branch, find the associated PR
gh pr view --json number,url

# Get the authenticated user's login (used to detect our own replies)
gh api user --jq '.login'

# Fetch line review comments
gh api repos/{owner}/{repo}/pulls/{pr_number}/comments

# Fetch top-level PR issue comments
gh api repos/{owner}/{repo}/issues/{pr_number}/comments

# Fetch review summaries / approvals / change requests
gh api repos/{owner}/{repo}/pulls/{pr_number}/reviews
```

#### Filtering out already-handled feedback

1. **Exclude feedback we already replied to.**
   - For line review comments: skip any top-level comment (no `in_reply_to_id`) that has a child reply with `user.login` matching the authenticated user's login.
   - For issue comments and review summaries: skip any item if there is a later PR issue comment from the authenticated user's login that clearly responds to it, references it, or states it was fixed/considered.
2. **Do not filter by commit history or prior agent runs.** Fetch the current PR feedback from all three endpoints and process it all, then apply only the reply-based filtering above. Do not rely on `git log`, commit authorship, `Co-Authored-By: Claude`, or `?since=` timestamps to decide what to ignore.

Assess all non-outdated reviewer or bot feedback across these surfaces, including issue comments, review summaries, minor comments, and nitpicks. Do not skip something just because it seems small; decide whether to **address**, **defer**, or **disagree** in step 2.

**If no unaddressed feedback remains after filtering, report this to the user and stop.**

### 2. Assess each feedback item

**Do not blindly action every item.** For each feedback item:

1. Read the comment/review body and understand the concern
2. Assess: Is it correct? Does it apply? Should it be fixed now or deferred?
3. Decide: **address**, **defer**, or **disagree**
4. If addressing: identify the **source file** (never generated files like `openapi.json`, `types.d.ts`, build artifacts)
5. Read the source file for context and determine the minimal fix

### 3. Confirm plan with the user

Once you have assessed all feedback items, **present your plan to the user before making any changes**. Use the ask question tool (or equivalent structured prompt) to show:

- For each feedback item: a brief summary of the reviewer's concern and your proposed action (**address**, **defer**, or **disagree**) with a one-line rationale
- If addressing: which source file you intend to edit and a short description of the fix

Ask the user to confirm the plan or adjust individual items. Only proceed with fixes after receiving confirmation. This prevents wasted effort on misunderstood feedback and keeps the user in control.

### 4. Apply the fix

- Edit the **source file** only. If the issue is in a generated file, find and fix the source that generates it.
- Keep changes minimal and focused — fix exactly what the comment asks for, nothing more.

### 5. Regenerate derived artifacts

If the project has generated files affected by the change (e.g. OpenAPI specs, type definitions, compiled output), run the appropriate regeneration command. Check CLAUDE.md or project docs for the correct command (e.g. `bun run api:sync`).

### 6. Verify the fix

- Confirm the generated output no longer has the reported issue
- Run the project's formatter and linter (e.g. `bun run format && bun run lint`)
- Run relevant tests if applicable

### 7. Commit and push

Stage only the relevant files (source + regenerated artifacts). Write a clear commit message:

```
fix: <concise description of what was fixed>

<brief explanation of root cause and what changed>

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

Push to the current branch.

### 8. Reply on the correct GitHub thread

Reply on the most specific available GitHub surface for that feedback:

- **Line review comment:** reply directly on the review comment thread so the reviewer sees it inline.
- **PR issue comment:** reply with a new PR issue comment that references the original feedback.
- **Review summary:** reply with a new PR issue comment that references the review summary and states what was addressed, deferred, or disagreed with.

**Replies must be very short, concise, and clear.** No fluff, no preamble. A few words to a single sentence is ideal. Examples:

- If addressed: `"Fixed in <sha> — updated the return type to match the schema."`
- If deferred: `"Valid point — deferring to a follow-up since it requires a broader refactor."`
- If disagreed: `"Keeping as-is — the explicit check is needed here because <brief reason>."`

When you've made a decision that differs from what the reviewer suggested, briefly explain **why**. Don't just say "won't fix" — give the reasoning in one sentence.

#### Posting the reply

For line review comments:

```bash
gh api repos/{owner}/{repo}/pulls/comments/{comment_id}/replies \
  -f body="<short reply>"
```

Use the `comment_id` from step 1 (the top-level review comment's `id`, not a child reply's ID).

For PR issue comments or review summaries:

```bash
gh api repos/{owner}/{repo}/issues/{pr_number}/comments \
  -f body="<short reply referencing the original feedback>"
```

Mention the reviewer or bot and include enough context that it is obvious which issue comment or review summary you are addressing.

**If the `/replies` endpoint fails for a line review comment, do not immediately fall back to a top-level comment.** Diagnose first:
- Verify `owner`, `repo`, and `comment_id` — common mistakes: using a reply's ID instead of the top-level ID, wrong owner for forks, or swapped PR number and comment ID
- Re-fetch comments and confirm the correct top-level `id`, then retry
- Try alternate form: `gh api repos/{owner}/{repo}/pulls/{pr_number}/comments/{comment_id}/replies`
- **Only after all retries fail**, fall back to a top-level issue comment:
  ```bash
  gh api repos/{owner}/{repo}/issues/{pr_number}/comments \
    -f body="<short reply referencing the original review comment>"
  ```

## Important rules

- **Confirm with the user** before making changes (step 3). If a comment is unclear, ask before proceeding.
- **Source files only.** Never edit generated files — fix the source, then regenerate.
- **Minimal fixes.** No unrelated refactors or improvements.
- **Short replies.** One sentence max. Include a brief reason when disagreeing or deferring.
- **One commit per feedback item** unless issues share a root cause.
- **Deferring is valid.** Say so and move on.
