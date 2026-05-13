# Refresh-token bug fix — PR title and body

## Problem / feature description

You are about to open a pull request from your branch `fix/auth-refresh-expiry` into `main`. The `auth` service was rejecting valid sessions because expired refresh tokens were not being detected before the access-token exchange. Your branch adds an explicit expiry check on the refresh token and a clear error response so clients can re-authenticate cleanly. The fix is tracked in Linear as **AUTH-712**.

Your team lead asked you to draft **both the GitHub PR title and the PR body** before opening the pull request, so they can be pasted directly into the GitHub UI.

## Output specification

Create exactly two files in the working directory:

1. **`pr-title.txt`** — a single line containing only the proposed PR title. No quotes, no leading hash, no Markdown.
2. **`pr-body.md`** — the full Markdown for the PR description field (body only).
