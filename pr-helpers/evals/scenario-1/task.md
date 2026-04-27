# PR Review Comment Assessment Report

## Problem/Feature Description

A senior engineering lead wants to streamline how their team processes pull request feedback. Currently, team members either apply every reviewer suggestion without thinking (sometimes introducing regressions) or ignore comments that seem difficult. The lead wants a structured assessment process: for each review comment on a PR, someone should evaluate whether the feedback is valid, decide whether to address it now, defer it, or push back with reasoning, and then present a summary plan before any code changes are made.

You are given a set of PR review comments and the corresponding source code files. Your task is to write an assessment report and a plan document that categorizes each comment and proposes specific actions, as if you were preparing to resolve these comments on a real PR. You must also identify which files to edit (being careful about source vs. generated files).

## Output Specification

Produce the following files:

1. **`assessment.json`** — A structured JSON document with an array of assessments, one per review comment. Each entry should have: `comment_id`, `reviewer`, `summary` (brief summary of the concern), `decision` (one of: "address", "defer", "disagree"), `rationale` (one sentence explaining why), and `target_file` (the source file to edit, if addressing — must be a source file, not a generated file).

2. **`plan.md`** — A markdown document that presents the plan as it would be shown to a user for confirmation. It should list each comment with the proposed action and invite the user to confirm or adjust before proceeding.

3. **`decisions_log.md`** — A brief document explaining the reasoning for any "defer" or "disagree" decisions in more detail.

## Input Files

The following files are provided as inputs. Extract them before beginning.

=============== FILE: inputs/review_comments.json ===============
[
  {
    "id": 2001,
    "body": "This endpoint returns raw database IDs in the response. We should be returning UUIDs instead to avoid leaking internal identifiers.",
    "user": {"login": "security-reviewer"},
    "path": "src/api/handlers/users.ts",
    "line": 45,
    "in_reply_to_id": null
  },
  {
    "id": 2002,
    "body": "The error handling here swallows the original error. Please preserve the error chain so we can debug production issues.",
    "user": {"login": "tech-lead"},
    "path": "src/api/handlers/users.ts",
    "line": 78,
    "in_reply_to_id": null
  },
  {
    "id": 2003,
    "body": "This type definition is wrong — the `email` field should be optional since we support social login without email.",
    "user": {"login": "tech-lead"},
    "path": "generated/types.d.ts",
    "line": 12,
    "in_reply_to_id": null
  },
  {
    "id": 2004,
    "body": "Nit: can we rename this variable from `d` to something more descriptive?",
    "user": {"login": "junior-dev"},
    "path": "src/api/handlers/users.ts",
    "line": 33,
    "in_reply_to_id": null
  },
  {
    "id": 2005,
    "body": "We should migrate this entire module to the new repository pattern we discussed in the architecture meeting last week. That would make this code much cleaner.",
    "user": {"login": "architect"},
    "path": "src/api/handlers/users.ts",
    "line": 1,
    "in_reply_to_id": null
  },
  {
    "id": 2006,
    "body": "The OpenAPI spec doesn't match the handler response shape.",
    "user": {"login": "qa-engineer"},
    "path": "generated/openapi.json",
    "line": 156,
    "in_reply_to_id": null
  }
]

=============== FILE: inputs/src/api/handlers/users.ts ===============
import { db } from '../../db';
import { UserSchema } from '../../schemas/user';

interface UserResponse {
  id: number;
  name: string;
  email: string;
}

export async function getUser(req: Request): Promise<UserResponse> {
  const userId = req.params.id;

  // TODO: add validation
  const d = db.query('SELECT * FROM users WHERE id = ?', [userId]);

  if (!d) {
    throw new Error('not found');
  }

  try {
    const result = await processUser(d);
    return {
      id: d.id,
      name: d.name,
      email: d.email
    };
  } catch (e) {
    console.log('error');
    return { id: 0, name: '', email: '' };
  }
}

=============== FILE: inputs/src/schemas/user.ts ===============
export const UserSchema = {
  type: 'object',
  properties: {
    id: { type: 'string', format: 'uuid' },
    name: { type: 'string' },
    email: { type: 'string', nullable: true }
  },
  required: ['id', 'name']
};
