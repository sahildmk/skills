# GitHub PR Reply Automation Script

## Problem/Feature Description

A platform team is building an internal tool that automates replying to GitHub PR review comments after an agent has resolved them. The team has found that their current bot frequently misuses the GitHub API: it posts replies as top-level issue comments instead of inline on the review thread, it uses the wrong comment IDs (child reply IDs instead of the parent), and when an API call fails it immediately falls back to a different endpoint without diagnosing the issue. The team wants a robust, well-structured script that handles replying correctly.

Build a bash script and a companion configuration guide that the team can use to reply to PR review comments correctly, with proper error handling and fallback logic.

## Output Specification

Produce the following files:

1. **`reply_to_comments.sh`** — A bash script that takes a PR number as an argument and demonstrates the workflow for replying to review comments. The script should:
   - Accept resolved comment data (simulated as a JSON file of decisions)
   - Post replies to the correct API endpoint for each comment
   - Handle API failures with proper diagnosis and retry logic
   - Include clear comments explaining the approach at each step

2. **`commit_helper.sh`** — A bash script that demonstrates how to create properly formatted commits for resolved PR comments. It should handle both single-comment and multi-comment scenarios.

3. **`example_replies.json`** — A JSON file showing example reply messages for different resolution types (addressed, deferred, disagreed), demonstrating the expected tone and format.

4. **`error_handling.md`** — A document describing the error handling strategy for the reply endpoint, including what to check when failures occur and the escalation path.

## Input Files

The following files are provided as inputs. Extract them before beginning.

=============== FILE: inputs/resolved_comments.json ===============
[
  {
    "comment_id": 3001,
    "pr_number": 42,
    "owner": "acme-corp",
    "repo": "backend-api",
    "decision": "address",
    "fix_summary": "Updated the return type to match the schema",
    "commit_sha": "abc1234"
  },
  {
    "comment_id": 3002,
    "pr_number": 42,
    "owner": "acme-corp",
    "repo": "backend-api",
    "decision": "defer",
    "fix_summary": "Requires a broader refactor of the auth module",
    "commit_sha": null
  },
  {
    "comment_id": 3003,
    "pr_number": 42,
    "owner": "acme-corp",
    "repo": "backend-api",
    "decision": "disagree",
    "fix_summary": "The explicit null check is intentional because the upstream API can return null despite the type annotation",
    "commit_sha": null
  }
]
