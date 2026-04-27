# Session handling hotfix — PR text

## Problem / feature description

You will open a pull request for a hotfix branch `hotfix/session-fingerprint` that tightens session binding after a third-party pen test flagged **session fixation risk** on the login flow. The patch rotates session identifiers post-authentication and invalidates old cookies server-side. Security filed **SEC-215**; infra wants a careful rollout because edge caches briefly held stale Set-Cookie behavior last release.

Your director wants the GitHub PR **body** written for security and platform reviewers. Save it as **`pr-body.md`** in the workspace (body text only, not the PR title).

## Output specification

Create exactly one file named **`pr-body.md`** containing the full Markdown for the pull request description field.
