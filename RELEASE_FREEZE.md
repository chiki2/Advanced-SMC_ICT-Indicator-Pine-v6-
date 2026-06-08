# Release Freeze - 2026-06-08

Status: release-candidate freeze for `advanced_smc_ict_indicator.pine`.

## Source Snapshot

- Script: `advanced_smc_ict_indicator.pine`
- SHA-256: `ba0773b0705b458758cefedee1a5d8fd2981075460d923140cfde6d7930cc435`
- Lines: `4292`
- Characters: `282026`

## TradingView Load Budget

- `request.security`: `2`
- `box.new`: `4`
- `line.new`: `6`
- `label.new`: `8`
- `table.new`: `2`
- `input.*`: `16`

## Freeze Scope

- Engine 3 signal logic is frozen for this release hygiene pass.
- The release hygiene work after this snapshot is limited to non-signal hygiene:
  source freeze evidence, Full-mode readability checks, and right-edge label
  deduplication review.
- The current visual budget/readability guardrails in the script are:
  - smaller structure labels in Full audit,
  - lower Full-mode structure/event caps,
  - lower Full-mode backfill scan caps,
  - wider right-edge label deduplication tolerance.
- No new inputs, no new `request.security`, and no new drawing constructors were added.

## Visual Audit Checklist

Validate in TradingView using Standard + Full:

- M1: `TF 1>15/30`
- M5: `TF 5>15/60`
- M15: `TF 15>30/60`
- M30: `TF 30>60/240`
- H1: `TF 60>240/1D`

Expected result:

- no token-limit warning,
- no 20-second loading warning,
- right-edge labels remain readable,
- Full mode remains audit-oriented but less cluttered,
- POI, Trigger, Order, and Next stay consistent with Engine 3 lifecycle.

## Verification Status

- Local source freeze: verified.
- Local diff hygiene: script has no active working-tree diff after freeze.
- Local load-budget metrics: verified from source counts above.
- TradingView compile/render after this freeze: pending external validation.
