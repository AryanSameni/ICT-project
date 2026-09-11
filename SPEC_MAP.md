# SPEC_MAP — ICT PRO 16-Phase Master Spec

**Source:** `ICT_PRO_COMPLETE_16_PHASE_MASTER_SPEC 4.md` (2,516 lines). This is the compact navigation map; it deliberately does not repeat implementation detail.

## Phase index

| Phase | Spec lines | Definition (1–2 lines) | Pine linkage (current map) |
|---|---:|---|---|
| 1 Core Architecture | 252–333 | Shared enums, types, storage, IDs, safe collections and compile-budget contracts. | 1–1,482: declaration, enums/types, utilities, global stores. |
| 2 Swing Engine | 334–424 | Three-degree, non-repainting swing detection, relations, hierarchy and finalization. | 1,535–3,977: inputs, pivot detection, relation/hierarchy/finalization. |
| 3 Liquidity Engine | 425–566 | Swing/reference/equal liquidity plus touch, sweep, acceptance and lifecycle. | 3,901–7,013: liquidity, EQH/EQL and sweep-event processing. |
| 4 Market Structure | 567–696 | Context, protected levels, BOS/CHoCH and pre-MSS candidate contract. | 7,014–10,690: contexts, events, protection, MSS candidates. |
| 5 Displacement + MSS | 697–794 | Scores displacement and confirms valid CHoCH→displacement MSS sequences. | 10,691–11,067: displacement detection and candidate confirmation. |
| 6 FVG Engine | 795–874 | Detects, validates, tracks fill/mitigation and links fair-value gaps. | 11,068–11,451; renderer 12,461–12,532. |
| 7 Order Block + Breaker | 875–948 | Creates, tests, mitigates and converts lineage-preserving OB/breaker zones. | 11,452–11,839; renderer 12,533–12,608. |
| 8 Dealing Range + OTE | 949–1,032 | Directional range, premium/discount, equilibrium and 62/70.5/79 OTE. | 11,840–12,103; renderer 12,609–12,787. |
| 8B Session + Killzone | 1,033–1,113 | Session OHLC/state, session liquidity and timezone/DST-aware killzones. | 12,104–13,049; renderer 12,788–13,006. |
| 9 Real MTF Context | 1,114–1,221 | MTF contexts must carry structure, liquidity, PD, session and confirmation state. | 13,050–13,306; context builder 13,181–13,299. |
| 9B DOL Engine | 1,222–1,305 | Ranks valid liquidity targets into explainable primary/secondary/tertiary DOL. | 13,307–13,703; renderer 14,039–14,101. |
| 10 Visual Manager | 1,306–1,588 | Centralizes drawing ownership, lifecycle rendering, scope, extension, labels and pools. | 12,414–13,006 and 13,705–14,206. |
| 11 Dashboard | 1,589–1,653 | Minimal/advanced/diagnostic views read analytical state without creating analysis. | 14,207–14,466. |
| 12 Object Manager + Performance | 1,654–1,798 | Separates logical-history, processing and drawing budgets; preserves equivalence. | 14,467–14,557; bounded scans occur throughout engines. |
| 13 Trading Assistant | 1,799–1,892 | Gates and scores A/A+/Sniper setups, explaining POI, invalidation and target. | 14,901–15,137. |
| 14 Backtest Strategy | 1,893–1,987 | Deterministic, non-leaking executions, journal and performance metrics. | **No implementation found**: script is `indicator()` at 2; no `TradeObject`/`strategy.*`. |
| 15 Alert Engine | 1,988–2,068 | Event-standard alerts with deduplication, filters, cooldown, cap and message contract. | 14,558–14,900. |
| 16 QA + Release Validation | 2,069–2,179 | Release gates for compile, tests, replay/MTF, visuals, budgets, backtest and alerts. | Debug/self-test hooks 860–1,482; no in-repo release evidence yet. |

**Supporting spec:** lines 2,183–2,516 define shared Pine standards, troubleshooting and phase templates; consult only when the active change touches that subject.

## Feature checklist by phase

- [ ] P1 — Enum/type contract; `ObjectMeta`; IDs; normalized TF/ticks; bounded array/map/queue cleanup; tests and compile reserve.
- [ ] P2 — Wick/close/combined pivots; External/Internal/Micro; HH/HL/LH/LL; parent linkage; analysis independent of visibility.
- [ ] P3 — BSL/SSL, EQH/EQL and reference levels; tolerance modes; touch→sweep→acceptance; quality; bounded clusters and filters.
- [ ] P4 — Per-TF/degree context; protected swings; BOS/CHoCH basis; lineage; pending/superseded/invalid MSS candidates with reasons.
- [ ] P5 — Body/ATR/close-location displacement quality; time-window link; standard/strict MSS; one-way confirmed state.
- [ ] P6 — Bull/bear three-candle FVG; min-size mode; CE/fill%; partial/mitigated/invalid lifecycle; displacement/structure lineage.
- [ ] P7 — Opposing-candle/cluster OB; structural+displacement proof; test/mitigation/age; breaker root lineage and overlap policy.
- [ ] P8 — Structural/manual/current/HTF range sources; directional PD/EQ/OTE; stale/replaced policy; session OHLC/liquidity and DST.
- [ ] P9 — Real per-TF payload: biases, BOS/CHoCH/MSS, protection, liquidity, sweep, FVG/OB, PD/session, confirmation; request budget.
- [ ] P9B — Valid-liquidity candidates; explainable weighted DOL score; hysteresis; reached/invalidation; target ranking/rendering.
- [ ] P10 — One visual owner; `VisualRef`; logical-ID dedup; scopes/extensions/endpoints/actions; label collision/budgets; recreate after reload/TF change.
- [ ] P11 — Minimal/Advanced/Diagnostic dashboard; engine-state parity; settings genuinely apply; no new analytical logic.
- [ ] P12 — Independent history/processing/visual limits; map/queue/cursor/pool optimization; equivalence and token-impact review.
- [ ] P13 — `SetupObject`; hard gates plus weighted score; A/A+/Sniper; POI/entry/stop/target/reason; user retains final decision.
- [ ] P14 — `TradeObject`; confirmed-only entry/exit; same-bar rule; commission/slippage; journal and metrics by context/sample.
- [ ] P15 — `AlertObject`; event ID + type dedup key; transition-only, cooldown/session cap, confirmed filters and complete message fields.
- [ ] P16 — Static/unit/integration/replay/realtime/visual/performance/backtest/alert matrix; release log, token reserve and regression gates.

## Dependency graph

- P1 → all phases (shared contract and storage).
- P2 → P3, P4, P8, P10, P11, P13, P14.
- P3 → P4, P5 (strict MSS), P9B, P13, P15.
- P4 → P5 → P6/P7/P13/P14/P15.
- P2 + P4 → P8; P8B → P3/P9/P13/P15.
- P2–P8 + P8B → P9 → P9B → P11/P13/P14/P15.
- P2–P9B → P10; P10 → P11 and visual portions of P15.
- P1–P11 → P12; P2–P13 → P14; P2–P13 → P15.
- P1–P15 → P16 (validation gate, not a feature dependency).

## Low-token operating rule

1. Read this map and `PINE_MAP.md` first.
2. Read only the cited source range for the requested feature; then update both maps if ranges, contracts or gaps change.
3. Preserve logical-object history when changing display/performance code; never use a visual limit as an analytical-data limit.
