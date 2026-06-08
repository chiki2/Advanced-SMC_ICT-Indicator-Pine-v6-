# Release Handoff - 2026-06-08

Status: verified release candidate.

## Scope

- Source script: `advanced_smc_ict_indicator.pine`
- Release freeze: `RELEASE_FREEZE.md`
- Freeze hash: `ba0773b0705b458758cefedee1a5d8fd2981075460d923140cfde6d7930cc435`
- Branch: `codex/regime-layer`
- Validation: TradingView Standard + Full screenshots on M1, M5, M15, M30, and H1.

## What Was Verified

- Clean compile in TradingView.
- No token-limit warning.
- No 20-second loading warning.
- Full mode remains audit-readable after visual budget cleanup.
- Right-edge labels remain useful on the validated timeframes.
- Engine 3 signal logic was not changed during the final release hygiene pass.

## Release Procedure

1. Open `advanced_smc_ict_indicator.pine` in TradingView.
2. Confirm the displayed source matches the freeze hash recorded in `RELEASE_FREEZE.md`.
3. Apply Standard + Full on M1, M5, M15, M30, and H1.
4. Confirm no compile warning, token-limit warning, or 20-second loading warning.
5. Use the release tag as the rollback point.

## Rollback

- Roll back to the verified freeze commit/tag if later patches affect compile, load time, or visual readability.
- Do not patch Engine 3 while diagnosing release visual issues unless the signal logic itself is the confirmed source of the problem.

## Next Maintenance Lane

- Keep future production patches small.
- Prefer visual/dashboard changes outside Engine 3.
- Re-run the same five-timeframe Standard + Full validation after every Pine source change.

