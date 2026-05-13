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

```

## Engine 1: Data Buffer
Gunakan template namespace ini untuk tiap TF: m1, m5, m15, m30, h1, h4, d1.

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
// ENGINE 1C: LIQUIDITY / PD ARRAY SNAPSHOT TEMPLATE
//------------------------------------------------------------------------------
var float X_erl_high = na
var float X_erl_low = na
var float X_irl_high = na
var float X_irl_low = na
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
var float X_protected_high = na
var float X_protected_low = na

```

```markdown
//------------------------------------------------------------------------------
// ENGINE 1 HELPERS
//------------------------------------------------------------------------------
f_buf_push_int(array<int> buf, int value, int cap) =>
f_buf_push_float(array<float> buf, float value, int cap) =>

f_tf_closed_ohlc(string tf) =>
    // returns: [closedTime, closedOpen, closedHigh, closedLow, closedClose]

f_tf_update_ohlc_buffer(string tf, array<int> arrT, array<float> arrO, array<float> arrH, array<float> arrL, array<float> arrC, int cap, int lastClosedTime) =>
    // returns: [newClosed, nextLastClosedTime]

f_tf_build_structure(array<float> arrH, array<float> arrL, array<float> arrC, int swingLen) =>
    // returns: [lastHigh, lastHighBar, prevHigh, prevHighBar, lastLow, lastLowBar, prevLow, prevLowBar, bullBos, bearBos, bullChoch, bearChoch, trendBias]

f_tf_build_liquidity(...) =>
    // returns: [erlHigh, erlLow, irlHigh, irlLow, eqh, eql, eqhActive, eqlActive]

f_tf_build_pd_arrays(...) =>
    // returns: [rangeHigh, rangeLow, equilibrium, oteLongTop, oteLongBottom, oteShortTop, oteShortBottom]

f_tf_build_fvg(...) =>
    // returns: [bullFvgTop, bullFvgBottom, bullFvgBar, bearFvgTop, bearFvgBottom, bearFvgBar]

f_tf_build_ob(...) =>
    // returns: [bullObTop, bullObBottom, bullObEntry, bullObBar, bullBreaker, bearObTop, bearObBottom, bearObEntry, bearObBar, bearBreaker]

f_tf_build_execution(...) =>
    // returns: [bullSweep, bearSweep, bullDisp, bearDisp, bullMss, bearMss, protectedHigh, protectedLow]

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

bool  exe_bull_sweep = false
bool  exe_bear_sweep = false
bool  exe_bull_mss = false
bool  exe_bear_mss = false
bool  exe_bull_displacement = false
bool  exe_bear_displacement = false
float exe_protected_high = na
float exe_protected_low = na

```	
	
## Engine 2: Draw Engine


### ENGINE 2: DRAW STATE

string draw_mode = effectiveZoneDisplayMode  // Full / Focused / Minimal
bool draw_full = draw_mode == "Full"
bool draw_focused = draw_mode == "Focused"
bool draw_minimal = draw_mode == "Minimal"

bool draw_show_sessions = showSessionVisuals and draw_full and timeframe.isintraday
bool draw_show_structure = showStructureLabels and draw_full and not compactMode
bool draw_show_sweeps = showSweeps and draw_full and not compactMode
bool draw_show_htf = showHtf
bool draw_show_protected = showProtectedLevels
bool draw_show_ote = strictShowOte and not draw_minimal
bool draw_show_right_labels = displayLiquidityRightLabels
// Active visual contract only
bool draw_long_selected = false
bool draw_short_selected = false
float draw_long_zone_top = na
float draw_long_zone_bottom = na
float draw_short_zone_top = na
float draw_short_zone_bottom = na
string draw_long_zone_type = ""
string draw_short_zone_type = ""
f_draw_zone_visible(string mode, bool isSelected, bool isBull, string preferredDirection) =>
f_draw_level_visible(string mode, bool isPrimary, bool isBull, string preferredDirection) =>
f_draw_zone_box(box bx, label lb, float top, float bottom, string zoneType, bool isBull, bool selected, bool terminal) =>
f_draw_session_box(...) =>
f_draw_structure_markers(...) =>
f_draw_right_edge_label(...) =>
f_draw_cleanup_hidden_objects() =>


## Engine 3: Setup Engine


### ENGINE 3A: CONTEXT POI CONTRACT

bool  poi_long_valid = false
bool  poi_short_valid = false
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

### ENGINE 3B: EXECUTION CONFIRM CONTRACT

bool exe_long_confirm = false
bool exe_short_confirm = false
bool exe_long_pd_pass = false
bool exe_short_pd_pass = false
bool exe_long_ote_pass = false
bool exe_short_ote_pass = false
bool exe_long_kz_pass = false
bool exe_short_kz_pass = false
bool exe_long_dol_pass = false
bool exe_short_dol_pass = false

### ENGINE 3C: TRADE PLAN / LIFECYCLE

string stp_long_state = "Waiting"
string stp_short_state = "Waiting"

float stp_long_entry = na
float stp_long_stop = na
float stp_long_target = na
float stp_short_entry = na
float stp_short_stop = na
float stp_short_target = na

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
f_select_context_poi(bool bullSide) =>
    // choose from ctx_* only, never from execution-owned arrays

f_validate_narrative_side(bool bullSide) =>
    // direction + macro liquidity + macro S/D

f_validate_execution_side(bool bullSide) =>
    // sweep + MSS + displacement + retest + session

f_build_trade_plan(bool bullSide, string poiType, float poiTop, float poiBottom, float poiEntry) =>
    // returns: [entry, stop, target, rr]

f_update_trade_lifecycle(...) =>
    // returns: [state, touched, completed, invalidated, ambiguous, age]
	
## Aturan Engine 3

// Long setup is valid only if:
poi_long_valid :=
    f_validate_narrative_side(true) and
    f_select_context_poi(true)

exe_long_confirm :=
    exe_bull_sweep and
    exe_bull_mss and
    exe_bull_displacement and
    exe_long_pd_pass and
    exe_long_ote_pass and
    exe_long_dol_pass and
    exe_long_kz_pass

// Same for short


## Engine 4: Dashboard Engine


### ENGINE 4: DASHBOARD SNAPSHOT

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
string dash_compact_zone = ""
string dash_compact_metric = ""
string dash_compact_life = ""
string dash_compact_mode = ""
f_dash_bias_text(int execBias, int ctxBias, int narrBias, string narrStrength) =>
f_dash_setup_text(string state, string poiType, bool retired) =>
f_dash_zone_text(float top, float bottom, float entry, bool retired) =>
f_dash_risk_text(float stop, float target, bool retired) =>
f_dash_rr_text(float rr, float confidence, bool retired) =>
f_dash_draw_text(float drawLevel, bool retired) =>
f_dash_life_text(int age, bool touched, bool retired) =>
f_dash_next_text(string modeSummary, string mtfSummary, string oteSummary) =>
f_dash_compact_text(...) =>


## Urutan Eksekusi

1. Resolve TF mapping
2. Update raw closed-candle buffers for all fixed TFs
3. Build per-TF snapshots
4. Select narrative/context/execution active snapshots
5. Build context POI
6. Validate execution confirmations
7. Build trade plan + lifecycle
8. Render chart from snapshots only
9. Render dashboard from snapshots only
