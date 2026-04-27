# PR Comment Resolver CLI Tool

## Problem/Feature Description

An engineering team wants to build an internal CLI tool that automates the end-to-end workflow of resolving GitHub pull request review comments. Today, developers manually read each comment, make fixes, commit, push, and reply — a tedious process that takes 30+ minutes per review cycle. The team wants a Python-based CLI tool that orchestrates this entire workflow, from fetching comments to posting replies.

The tool won't be run directly in this context, but the team needs the complete source code, configuration, and documentation so they can integrate it into their developer workflow. The tool should handle the full lifecycle of processing PR review comments from start to finish.

## Output Specification

Produce the following files:

1. **`resolver.py`** — The main CLI tool that orchestrates the full PR comment resolution workflow. It should define clear functions/stages for each phase of the process.

2. **`config.py`** — Configuration and constants used by the resolver (API endpoints, templates, etc.).

3. **`README.md`** — Usage documentation including: how to run the tool, what each stage does, and what the expected behavior is at each step.

4. **`workflow_diagram.md`** — A text-based diagram or ordered list showing the exact sequence of operations the tool performs, from start to finish. Include decision points (e.g., what happens when there are no comments, or when a comment should be deferred).

## Input Files

The following files are provided as inputs. Extract them before beginning.

=============== FILE: inputs/sample_usage.txt ===============
The team expects to use the tool like this:

  python resolver.py --pr 689
  python resolver.py --pr https://github.com/acme/api/pull/689

It should work with the `gh` CLI for GitHub API access.
The team values transparency — developers should understand what the tool
plans to do before it starts changing code.
===============
