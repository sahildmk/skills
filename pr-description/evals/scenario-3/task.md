# Monorepo tooling migration — PR text

## Problem / feature description

You are preparing a pull request that migrates shared build tooling across **thirty-eight services** in the company monorepo: ESLint flat config, shared `tsconfig` bases, and CI matrix updates. Touch footprint is intentionally broad; many teams will see noisy diffs mostly from config churn rather than product logic. The platform initiative is tracked as **PLAT-550**.

Your goal is to give reviewers a clear PR **body** (not the title) so guild leads understand blast radius and how to review—saved as **`pr-body.md`**.

## Output specification

Write **`pr-body.md`** containing only the Markdown for the GitHub pull request description field.
