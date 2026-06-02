# Kontrak Inti

## SHARED CONTRACT
```markdown
//------------------------------------------------------------------------------
// SHARED CONTRACT
//------------------------------------------------------------------------------
string TF_M1  = "1"
string TF_M5  = "5"
string TF_M15 = "15"
string TF_M30 = "30"
string TF_H1  = "60"
string TF_H4  = "240"
string TF_D1  = "1D"

int BUF_CAP = 300

string eng_exec_tf = timeframe.main_period
string eng_ctx_tf = f_ictContextHtf(timeframe.in_seconds())
string eng_narr_tf = f_ictNarrativeHtf(timeframe.in_seconds())

bool eng_exec_valid = eng_ctx_tf != "" and eng_narr_tf != ""

string tradingSessionTimezone = "Etc/UTC"
string asiaSessionWindow = "0000-0900"
string londonSessionWindow = "0700-1600"
string newYorkSessionWindow = "1300-2200"

string ipdaTimeTimezone = "Etc/UTC"
string ipdaAsiaKillzoneSession = "0000-0300"
string ipdaLondonKillzoneSession = "0700-1000"
string ipdaNewYorkKillzoneSession = "1200-1500"

```

## Engine 1: Data Buffer
Gunakan template namespace raw buffer, structure, dan execution untuk tiap TF: m1, m5, m15, m30, h1, h4, d1. Liquidity/PD array yang tidak dipakai langsung downstream tidak disimpan sebagai state per-TF; nilainya dihitung pada active layer dari snapshot narrative/context/execution yang sedang dipakai.

Catatan pruning implementasi: snapshot zona OB/FVG tidak wajib untuk M1/M5 karena kedua TF ini dipakai sebagai execution layer, bukan context POI. Context/narrative POI tetap berasal dari M15, M30, H1, H4, dan D1 agar state tidak melebar tanpa downstream ownership.

```markdown
//------------------------------------------------------------------------------
// ENGINE 1A: RAW CLOSED-CANDLE BUFFER TEMPLATE
//------------------------------------------------------------------------------
// Replace X with: m1, m5, m15, m30, h1, h4, d1
var array<int>   buf_X_time  = array.new_int()
var array<float> buf_X_open  = array.new_float()
var array<float> buf_X_high  = array.new_float()
var array<float> buf_X_low   = array.new_float()
var array<float> buf_X_close = array.new_float()

var int X_last_closed_time = na
var bool X_new_closed_bar = false

```

```markdown
//------------------------------------------------------------------------------
// ENGINE 1B: STRUCTURE SNAPSHOT TEMPLATE
//------------------------------------------------------------------------------
var float X_last_swing_high = na
var int   X_last_swing_high_bar = na
var float X_prev_swing_high = na
var int   X_prev_swing_high_bar = na
var float X_last_swing_low = na
var int   X_last_swing_low_bar = na
var float X_prev_swing_low = na
var int   X_prev_swing_low_bar = na

var bool  X_bull_bos = false
var bool  X_bear_bos = false
var bool  X_bull_choch = false
var bool  X_bear_choch = false
var int   X_trend_bias = 0

```

```markdown
//------------------------------------------------------------------------------
// ENGINE 1C: ACTIVE LIQUIDITY / PD ARRAY SNAPSHOT TEMPLATE
//------------------------------------------------------------------------------
// Computed only for the selected active layer unless a downstream engine owns it.
var float X_eqh = na
var float X_eql = na
var bool  X_eqh_active = false
var bool  X_eql_active = false

var float X_range_high = na
var float X_range_low = na
var float X_equilibrium = na
var float X_ote_long_top = na
var float X_ote_long_bottom = na
var float X_ote_short_top = na
var float X_ote_short_bottom = na

```

```markdown
//------------------------------------------------------------------------------
// ENGINE 1D: ZONE SNAPSHOT TEMPLATE
// Apply to context/narrative POI TFs only: m15, m30, h1, h4, d1.
// M1/M5 are intentionally execution-only and do not carry OB/FVG snapshot state.
//------------------------------------------------------------------------------
var float X_bull_ob_top = na
var float X_bull_ob_bottom = na
var float X_bull_ob_entry = na
var int   X_bull_ob_bar = na
var bool  X_bull_ob_breaker = false

var float X_bear_ob_top = na
var float X_bear_ob_bottom = na
var float X_bear_ob_entry = na
var int   X_bear_ob_bar = na
var bool  X_bear_ob_breaker = false

var float X_bull_fvg_top = na
var float X_bull_fvg_bottom = na
var int   X_bull_fvg_bar = na

var float X_bear_fvg_top = na
var float X_bear_fvg_bottom = na
var int   X_bear_fvg_bar = na

```

```markdown
//------------------------------------------------------------------------------
// ENGINE 1E: EXECUTION SNAPSHOT TEMPLATE
//------------------------------------------------------------------------------
var bool X_bull_sweep = false
var bool X_bear_sweep = false
var bool X_bull_displacement = false
var bool X_bear_displacement = false
var bool X_bull_mss = false
var bool X_bear_mss = false
var float X_mss_ref_high = na
var float X_mss_ref_low = na

```

```markdown
//------------------------------------------------------------------------------
// ENGINE 1 HELPERS
//------------------------------------------------------------------------------
int ENG1_CHART_INDEX_CAP = 5000
int ENG1_TIME_MAP_CACHE_CAP = 64
// Chart time/bar mapping arrays trim as a paired cache; do not trim one side alone.
// Source-time to chart-bar cache also trims as paired source/bar entries.

f_buf_push_int(array<int> buf, int value, int cap) =>
f_buf_push_float(array<float> buf, float value, int cap) =>

f_tf_closed_ohlc(string tf) =>
    // returns: [closedTime, closedOpen, closedHigh, closedLow, closedClose]

f_prev_period_high_low(bool enabled, string tf) =>
    // returns: [periodHigh, periodLow]
    // Used only for last-bar calendar liquidity visuals; disabled paths must return na.
    // PDH/PDL may reuse the active D1 Engine 1 buffer before falling back to request.security.

f_tf_update_ohlc_buffer(bool enabled, string tf, array<int> arrT, array<float> arrO, array<float> arrH, array<float> arrL, array<float> arrC, int cap, int lastClosedTime) =>
    // returns: [newClosed, nextLastClosedTime]

f_tf_build_structure(array<float> arrH, array<float> arrL, array<float> arrC, int swingLen) =>
    // returns: [lastHigh, lastHighBar, prevHigh, prevHighBar, lastLow, lastLowBar, prevLow, prevLowBar, bullBos, bearBos, bullChoch, bearChoch, structureBias]

f_tf_build_liquidity(...) =>
    // returns: [erlHigh, erlLow, irlHigh, irlLow, eqh, eql, eqhActive, eqlActive]

f_tf_build_pd_arrays(...) =>
    // returns: [rangeHigh, rangeLow, equilibrium, oteLongTop, oteLongBottom, oteShortTop, oteShortBottom]

f_tf_build_fvg(...) =>
    // returns: [bullFvgTop, bullFvgBottom, bullFvgBar, bearFvgTop, bearFvgBottom, bearFvgBar]

f_tf_build_ob(...) =>
    // returns: [bullObTop, bullObBottom, bullObEntry, bullObBar, bullBreaker, bearObTop, bearObBottom, bearObEntry, bearObBar, bearBreaker]

f_tf_build_execution(...) =>
    // returns: [bullSweep, bearSweep, bullDisp, bearDisp, bullMss, bearMss, mssRefHigh, mssRefLow]

```
	
## Engine 1: Active Layer Alias

```markdown
//------------------------------------------------------------------------------
// ENGINE 1F: ACTIVE LAYER SNAPSHOT
//------------------------------------------------------------------------------
int   narr_bias = 0
string narr_strength = "Flat"
float narr_liq_high = na
float narr_liq_low = na
float narr_bull_sd_top = na
float narr_bull_sd_bottom = na
float narr_bear_sd_top = na
float narr_bear_sd_bottom = na

int   ctx_trend_bias = 0
bool  ctx_bull_breakout = false
bool  ctx_bear_breakout = false
bool  ctx_bull_reversal = false
bool  ctx_bear_reversal = false
float ctx_bull_ob_top = na
float ctx_bull_ob_bottom = na
float ctx_bull_ob_entry = na
float ctx_bull_fvg_top = na
float ctx_bull_fvg_bottom = na
float ctx_bear_ob_top = na
float ctx_bear_ob_bottom = na
float ctx_bear_ob_entry = na
float ctx_bear_fvg_top = na
float ctx_bear_fvg_bottom = na
float ctx_liq_high = na
float ctx_liq_low = na

int   exe_trend_bias = 0
bool  exe_bull_sweep = false
bool  exe_bear_sweep = false
bool  exe_bull_mss = false
bool  exe_bear_mss = false
bool  exe_bull_displacement = false
bool  exe_bear_displacement = false
float exe_mss_ref_high = na
float exe_mss_ref_low = na

// Downstream reads these aliases so visuals, setup validation, and dashboard
// use the same active Engine 1 bias snapshot.
int display_exec_bias = exe_trend_bias
int display_ctx_bias = ctx_trend_bias
int display_narr_bias = narr_bias

```	
	
## Engine 2: Draw Engine
```markdown
//------------------------------------------------------------------------------
// ENGINE 2: DRAW STATE
//------------------------------------------------------------------------------
string draw_mode = effectiveZoneDisplayMode  // Full / Focused / Minimal
bool draw_full = draw_mode == "Full"
bool draw_focused = draw_mode == "Focused"
bool draw_minimal = draw_mode == "Minimal"

int ENG2_SESSION_HISTORY_CAP = free_safe_mode ? 1 : 3
bool sessionVisualsEnabled = showSessionVisuals and draw_full and timeframe.isintraday
int session_archive_styled_count = na  // dirty counter; restyle archived sessions only when archive set changes
// Active session sync moves geometry only; object style/text is set on create/archive.
// Session lane owns create/update/archive directly; no extra session wrapper layer.
// Archived session restyle is inline in the guarded archive refresh loop.
// Disabled session lanes should clear only when active objects exist.
// Session archive trim is guarded so empty histories/counters do not churn.
// Terminal history boxes move geometry only after creation, style is static for
// the snapshot, and sync only when visible or existing handles must be cleared.
// Selected setup OB/FVG boxes, entry lines, and labels sync only when visible or
// existing handles must be cleared.
// Bounded/risk/thin liquidity lines and right-edge labels update only on the
// last bar; hidden visuals skip sync when no handles must be cleared.
// Calendar/right-label overlap checks short-circuit when the source visual is
// hidden, so hidden labels do not spend work on collision rules.
// Segment lines keep extend static after creation.
// Narrative HTF S/D boxes and labels move geometry only after creation,
// style/text is static per side, and sync only when visible or existing handles
// must be cleared.
// Passive context OB/FVG boxes move geometry only after creation, never own labels,
// and sync only when visible or existing handles must be cleared.
// Dealing-range/OTE sync is inline in the last-bar visual block; fills track
// their bound lines and sync only when visible or existing handles must clear.
bool draw_show_structure = showMarketStructure and draw_full and not compactMode
string structure_render_fingerprint = ""
bool structure_source_changed = exec_structure_new_closed or ctx_structure_new_closed
// Structure history rebuild is allowed on first render/reset or active exec/context closed-bar changes that alter the visual snapshot.
// Unchanged snapshots may move the latest structure event line endpoint only; they must not rebuild history.
// Full audit is budgeted: structure scan/history caps stay visual-only and must not feed Engine 1/3 rules.
// Label collision tolerance is computed once per label resolve, then reused across bump attempts.
bool displaySweepMarkers = showLiquidity and showSweeps and draw_full and not compactMode
// Tight Full-budget mode suppresses raw/extra audit event labels; primary setup lifecycle labels remain.
bool draw_show_htf = showHtf
bool showOteVisual = strictShowOte and (draw_full or draw_focused) and not draw_minimal
bool draw_show_right_labels = showLiquidityRightLabels

```

```markdown
// Active visual contract only
bool draw_long_selected = false
bool draw_short_selected = false
float draw_long_zone_top = na
float draw_long_zone_bottom = na
float draw_short_zone_top = na
float draw_short_zone_bottom = na
string draw_long_zone_type = ""
string draw_short_zone_type = ""

```

```markdown
f_draw_zone_visible(string mode, bool isSelected, bool isBull, string preferredDirection) =>
f_draw_level_visible(string mode, bool isPrimary, bool isBull, string preferredDirection) =>
f_signal_event_cap() =>
    // Visual-only cap for signal event label history.
f_trim_signal_event_history(int cap) =>
    // Signal event trim is guarded so unchanged histories do not churn.
// Event tag and raw cooldown resets use direct guards; they must not change event creation rules.
bool any_event_create_ready = raw_event_create_ready or long_setup_event_state_ready or short_setup_event_state_ready
    // Event label payload/cooldown/emit work runs only when at least one event path can create.
    // Raw sweep/displacement and setup tapped/continuation/breaker payloads are sub-gated separately.
    // Event emit helpers are called only after each concrete label event is true.
// Raw sweep/displacement cooldown bars and visual-only structure render budgets are computed inline.
// Structure/session trim guards are inline so empty histories do not churn.
f_draw_zone_box(box bx, label lb, float top, float bottom, string zoneType, bool isBull, bool selected, bool terminal) =>
f_draw_session_box(...) =>
f_draw_structure_markers(...) =>
f_draw_right_edge_label(...) =>
f_draw_cleanup_hidden_objects() =>

```

## Engine 3: Setup Engine
```markdown
//------------------------------------------------------------------------------
// ENGINE 3A: CONTEXT POI CONTRACT
//------------------------------------------------------------------------------
bool  poi_long_candidate_valid = false
bool  poi_short_candidate_valid = false
bool  poi_long_identity_ready = false
bool  poi_short_identity_ready = false
bool  strict_long_pd_array_owned = true
bool  strict_short_pd_array_owned = true
bool  poi_long_contract_valid = false
bool  poi_short_contract_valid = false
string poi_long_wait_reason = "No POI"
string poi_short_wait_reason = "No POI"
string poi_long_retire_reason = ""
string poi_short_retire_reason = ""
string poi_long_consumed_reason = ""
string poi_short_consumed_reason = ""
string poi_long_type = ""     // "OB" / "FVG" / "Breaker"
string poi_short_type = ""
float poi_long_top = na
float poi_long_bottom = na
float poi_long_entry = na
int   poi_long_bar = na
float poi_short_top = na
float poi_short_bottom = na
float poi_short_entry = na
int   poi_short_bar = na

```

```markdown
//------------------------------------------------------------------------------
// ENGINE 3B: EXECUTION CONFIRM CONTRACT
//------------------------------------------------------------------------------
bool exe_long_confirm = false
bool exe_short_confirm = false
bool exe_long_pd_pass = false
bool exe_short_pd_pass = false
bool exe_long_ote_pass = false
bool exe_short_ote_pass = false
bool exe_long_dol_pass = false
bool exe_short_dol_pass = false
bool strict_long_killzone_pass = true
bool strict_short_killzone_pass = true

```

```markdown
//------------------------------------------------------------------------------
// ENGINE 3C: TRADE PLAN / LIFECYCLE
//------------------------------------------------------------------------------
string stp_long_state = "Waiting"
string stp_short_state = "Waiting"

float stp_long_entry = na
float stp_long_stop = na
float stp_long_target = na
float stp_short_entry = na
float stp_short_stop = na
float stp_short_target = na
float strict_long_sweep_extreme = na
float strict_short_sweep_extreme = na

float stp_long_rr = na
float stp_short_rr = na
bool  stp_long_touched = false
bool  stp_short_touched = false
bool  stp_long_completed = false
bool  stp_short_completed = false
bool  stp_long_invalidated = false
bool  stp_short_invalidated = false
bool  stp_long_ambiguous = false
bool  stp_short_ambiguous = false
int   stp_long_age = na
int   stp_short_age = na

```

```markdown
f_select_context_poi(bool bullSide) =>
    // choose from ctx_* only, never from execution-owned arrays

f_validate_narrative_side(bool bullSide) =>
    // direction + macro liquidity + macro S/D

f_validate_execution_side(bool bullSide) =>
    // sweep + MSS + displacement + retest + session

f_build_trade_plan(bool bullSide, string poiType, float poiTop, float poiBottom, float poiEntry) =>
    // returns: [entry, stop, target, rr]
    // Standard stop = POI boundary + ATR buffer.
    // Strict Confirm stop = outside the latest sweep/raid extreme when available,
    // while keeping the wider of POI-boundary stop and sweep-extreme stop.

f_update_trade_lifecycle(...) =>
    // returns: [state, touched, completed, invalidated, ambiguous, age]

```

## Aturan Engine 3
```markdown
// Long setup is valid only if:
poi_long_candidate_valid :=
    f_select_context_poi(true)

poi_long_contract_valid :=
    f_validate_narrative_side(true) and
    poi_long_candidate_valid and
    poi_long_identity_ready and
    strict_long_pd_array_owned

poi_short_candidate_valid :=
    f_select_context_poi(false)

poi_short_contract_valid :=
    f_validate_narrative_side(false) and
    poi_short_candidate_valid and
    poi_short_identity_ready and
    strict_short_pd_array_owned

exe_long_confirm :=
    exe_bull_sweep and
    exe_bull_mss and
    exe_bull_displacement and
    exe_long_pd_pass and
    exe_long_ote_pass and
    exe_long_dol_pass and
    strict_long_killzone_pass

// Same execution confirmation shape for short

// Strict-only PD Array ownership:
// - Standard mode may still select the best active context OB/FVG.
// - Strict Confirm may select only confirmed PD+OTE candidates owned by the
//   latest sweep/raid, MSS, and displacement sequence.
// - FVG ownership is anchored to the expansion leg; OB/Breaker ownership is
//   allowed from the sweep-to-expansion leg so the last opposing candle remains valid.

```

## Engine 4: Dashboard Engine
```markdown
//------------------------------------------------------------------------------
// ENGINE 4: DASHBOARD SNAPSHOT
//------------------------------------------------------------------------------
string dash_bias_market = ""
string dash_setup_long = ""
string dash_setup_short = ""
string dash_zone_long = ""
string dash_zone_short = ""
string dash_risk_long = ""
string dash_risk_short = ""
string dash_rr_long = ""
string dash_rr_short = ""
string dash_draw_long = ""
string dash_draw_short = ""
string dash_life_long = ""
string dash_life_short = ""
string dash_session_market = ""
string dash_next_market = ""
string dash_compact_setup = ""
string dash_compact_exec = ""
string dash_compact_bias = ""
string dash_compact_poi = ""
string dash_compact_zone = ""
string dash_compact_metric = ""
string dash_compact_life = ""
string dash_compact_mode = ""


```

```markdown
f_dash_bias_text(int execBias, int ctxBias, int narrBias, string narrStrength) =>
f_dash_bias_compact_text(...) =>
    // Compact dashboard shortens bias state and MTF chain only; it must not
    // alter narrative/context/execution bias ownership.
f_dash_setup_text(string state, string poiType, bool retired) =>
f_dash_zone_text(float top, float bottom, float entry, bool retired) =>
f_dash_risk_text(float stop, float target, bool retired) =>
f_dash_rr_text(float rr, string signalGrade, bool retired) =>
f_dash_draw_text(float drawLevel, bool retired) =>
f_dash_life_text(int age, bool touched, bool retired) =>
f_dash_next_text(string modeSummary, string mtfSummary, string oteSummary) =>
f_dash_poi_text(..., bool compactText) =>
    // Full dashboard keeps descriptive POI/retire reasons; compact dashboard
    // shortens the same state only for mobile readability.
f_dash_compact_text(...) =>

```


## Urutan Eksekusi

1. Resolve TF mapping
2. Update raw closed-candle buffers for active execution/context/narrative TFs
3. Update session range high/low only when Strict IPDA pools or Full session visuals need them
4. Evaluate Strict-only IPDA pool raids only when Strict Confirm is active
5. Update previous-period calendar liquidity only on the last bar when HTF liquidity visuals are enabled
6. Build per-TF snapshots
7. Select narrative/context/execution active snapshots
8. Build context POI
9. Validate execution confirmations
10. Build trade plan + lifecycle
11. Render chart from snapshots only
12. Render dashboard from snapshots only

Killzone ownership: Strict Confirm uses killzone as a hard gate; normal mode
may still use the same active window as a scoring bonus only.
Dynamic score helper inputs are evaluated only after the side hard gate is true;
the score helper assumes a valid side, while invalid sides keep score 0.
Target/penalty score evidence is named per side before entering the dynamic
score helper; this keeps score ownership auditable without changing weights.
Single-line score penalties stay inline; target-liquidity matching remains
helper-owned, uses the shared exact-level tolerance, and receives only the
active side's relevant liquidity levels.
Lifecycle base pass owns Waiting-to-Confirming display progress only; execution
confirmation, MSS freshness, and displacement freshness are read directly from
the side snapshots and armed latch.
Zone quality response pass is score-only for quality tiering; it must not be
read as execution confirmation.
Quality tier inputs are named score components before entering the tier helper;
the weights stay response + overlap bonus + displacement + ATR expansion.
Dashboard trigger flags are display-only OK/Need states; entry confirmation
still comes from the side confirm contract.
Dashboard triggerReady owns only the Trigger-row highlight color.
Dashboard row backgrounds are named display snapshots only; they must not feed
signal validation or lifecycle transitions.
Timing row background is part of the same display snapshot set.
Dashboard table reset and blank spacer rows are helper-owned display cleanup.
