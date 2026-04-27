# PR Comment Filtering Tool

## Problem/Feature Description

A DevOps team at a mid-size company uses GitHub PRs extensively. Their engineering workflow involves an automated agent that periodically processes unresolved review comments. However, the agent currently re-processes every comment on every run, including ones it already addressed — leading to duplicate replies and redundant commits. The team needs a robust filtering module that determines which comments actually need attention on any given run.

You have a JSON payload of PR review comments (provided below) from the GitHub API. Build a Python module called `comment_filter.py` that takes a list of PR review comment objects and returns only the comments that still need to be addressed. The module should also include a shell script `fetch_comments.sh` that demonstrates how to retrieve and filter comments from the GitHub API using `gh` CLI commands.

## Output Specification

Produce the following files:

1. **`comment_filter.py`** — A Python module with a function `filter_unaddressed_comments(comments: list, my_login: str, last_agent_commit_timestamp: str | None) -> list` that filters the input list and returns only unaddressed comments.

2. **`fetch_comments.sh`** — A bash script that shows the full sequence of `gh` CLI commands for: (a) getting the authenticated user's login, (b) finding the last agent commit timestamp, (c) fetching review comments with the appropriate API endpoint and `since` parameter, and (d) calling the filter. The script should be well-commented explaining each step.

3. **`test_filter.py`** — Unit tests for the filtering function covering edge cases: comments already replied to, old comments from prior runs, first run with no prior commits, and comments that still need attention.

## Input Files

The following files are provided as inputs. Extract them before beginning.

=============== FILE: inputs/sample_comments.json ===============
[
  {
    "id": 1001,
    "body": "This function should validate the input before processing",
    "user": {"login": "reviewer-alice"},
    "in_reply_to_id": null,
    "created_at": "2024-11-01T10:00:00Z",
    "updated_at": "2024-11-01T10:00:00Z"
  },
  {
    "id": 1002,
    "body": "Fixed in abc123 — added input validation.",
    "user": {"login": "agent-bot"},
    "in_reply_to_id": 1001,
    "created_at": "2024-11-01T11:00:00Z",
    "updated_at": "2024-11-01T11:00:00Z"
  },
  {
    "id": 1003,
    "body": "The error message here is misleading, it says 'not found' but the actual issue is a permission error",
    "user": {"login": "reviewer-bob"},
    "in_reply_to_id": null,
    "created_at": "2024-11-01T12:00:00Z",
    "updated_at": "2024-11-01T12:00:00Z"
  },
  {
    "id": 1004,
    "body": "Consider using a constant for this magic number",
    "user": {"login": "reviewer-alice"},
    "in_reply_to_id": null,
    "created_at": "2024-11-02T09:00:00Z",
    "updated_at": "2024-11-02T09:00:00Z"
  },
  {
    "id": 1005,
    "body": "This reply is from another user, not the agent",
    "user": {"login": "reviewer-carol"},
    "in_reply_to_id": 1003,
    "created_at": "2024-11-02T10:00:00Z",
    "updated_at": "2024-11-02T10:00:00Z"
  },
  {
    "id": 1006,
    "body": "The return type annotation is wrong here",
    "user": {"login": "reviewer-bob"},
    "in_reply_to_id": null,
    "created_at": "2024-10-25T08:00:00Z",
    "updated_at": "2024-10-25T08:00:00Z"
  }
]
