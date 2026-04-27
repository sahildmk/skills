# Checkout reliability work — PR text

## Problem / feature description

You are about to open a pull request from your branch `feature/checkout-retry` into `main`. Over the past week, payment failures spiked during peak traffic; on-call traced many to transient network blips between the checkout service and the PSP. Your branch introduces client-side retries with exponential backoff and idempotency keys on the public checkout API so duplicate charges cannot land if clients or gateways repeat requests.

Product tracking for this initiative lives in Linear under **ENG-4421**. Your engineering manager asked you to drop a review-ready GitHub pull request **body** (not the title) into the repository as a Markdown file before you open the PR, because reviewers on the payments guild were not in your pairing sessions.

## Output specification

Create exactly one file named **`pr-body.md`** in the working directory. The file must contain the full Markdown text intended to paste into the GitHub pull request description field (body only). Do not include the GitHub PR title in this file.
