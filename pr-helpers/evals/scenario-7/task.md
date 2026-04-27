# Usage metering — PR text

## Problem / feature description

You are opening a pull request for branch `feature/usage-metering-hooks` that wires the billing service to new usage events emitted by the data plane. The integration **depends on** a client bump that merged elsewhere: pull request **#1847** in the separate `billing-sdk` repository (same org). Your squad also has an internal design note at `go/billing-metering` that reviewers should read for field semantics.

Ticket **BILL-901** tracks the epic. Save the GitHub PR **body** as **`pr-body.md`** (description field only, not the title).

## Output specification

Create exactly one file **`pr-body.md`** with the full Markdown for the pull request description.
