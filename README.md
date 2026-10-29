# skills

Agent skills for working with GitHub pull requests, packaged as [Tessl](https://tessl.io) tiles.

## Repository layout

| Path | What it is |
|---|---|
| [`pr-helpers/`](pr-helpers/) | The `sahildmk/pr-helpers` tile: skills for writing and shipping pull requests. See its [README](pr-helpers/README.md). |
| [`pr-helpers/tile.json`](pr-helpers/tile.json) | Tile manifest: name, version and the skills it exports. |
| [`pr-helpers/skills/`](pr-helpers/skills/) | One directory per skill, each with a `SKILL.md`. |
| [`pr-helpers/evals/`](pr-helpers/evals/) | Eval scenarios (`task.md`, `criteria.json`) that check an agent follows the skills' instructions. |
| [`tessl.json`](tessl.json) | Tessl project manifest for this repository. |

## Skills

- [`pr-description`](pr-helpers/skills/pr-description/SKILL.md): drafts and refines pull request descriptions with a consistent structure (context, motivation, summary of changes, links) and conventional commit-style titles.
- [`pr-comment-resolver`](pr-helpers/skills/pr-comment-resolver/SKILL.md): works through unresolved PR feedback, fixes the source, runs lint and format, commits, pushes and replies on the right GitHub thread.

## Using the skills

Install the tile into a Tessl project:

```sh
tessl install sahildmk/pr-helpers
```

Then ask your agent to write a PR description or address PR comments, and it will pick the matching skill.

## Adding a skill

1. Create `pr-helpers/skills/<skill-name>/SKILL.md` with `name` and `description` frontmatter.
2. Register it under `skills` in `pr-helpers/tile.json` and bump the `version`.
3. Add eval scenarios under `pr-helpers/evals/` and describe the skill in `pr-helpers/README.md`.
