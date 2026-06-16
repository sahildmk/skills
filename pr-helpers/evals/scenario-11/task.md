# GitHub Review Reply Endpoint Contract

## Problem / feature description

Your team just found that GitHub's shorter review-comment reply endpoint can return `404` for a valid top-level PR review comment:

```bash
gh api repos/acme/api/pulls/comments/3419335254/replies
```

The same reply succeeds when the PR number is included:

```bash
gh api repos/acme/api/pulls/4794/comments/3419335254/replies
```

Build a small artifact that documents and demonstrates the correct endpoint selection for line review comment replies. The goal is to prevent future agents from posting top-level issue comments or using the non-PR-scoped endpoint as the primary call.

## Output specification

Produce exactly three files:

1. **`reply_endpoint_contract.sh`** — a bash script that defines a function for replying to a line review comment. The script must:
   - accept `owner`, `repo`, `pr_number`, `comment_id`, and `body`
   - call the PR-scoped endpoint first: `repos/{owner}/{repo}/pulls/{pr_number}/comments/{comment_id}/replies`
   - only try `repos/{owner}/{repo}/pulls/comments/{comment_id}/replies` as a legacy retry after the PR-scoped endpoint fails
   - only use `repos/{owner}/{repo}/issues/{pr_number}/comments` as the final fallback after both inline reply attempts fail

2. **`expected_calls.txt`** — the expected ordered API calls for `owner=acme`, `repo=api`, `pr_number=4794`, `comment_id=3419335254`.

3. **`endpoint_notes.md`** — a concise explanation of why the PR-scoped endpoint is primary, what IDs to verify on failure, and when a top-level issue comment fallback is acceptable.
