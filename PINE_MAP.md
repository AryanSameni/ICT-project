# PINE_MAP — ICT V8

**Source:** `ICT V8.pine` (15,137 lines; Pine v6). Ranges are navigation ranges, not a substitute for source review.

## Code structure and phase links

| Lines | Section / key functions | Purpose | Spec phase |
|---:|---|---|---|
| 1–9 | `indicator()` | Script declaration and chart resource caps. | P1; conflicts with P14 strategy requirement. |
| 38–267 | Enums | Direction, swing/liquidity/structure/FVG/OB/PD/session/MTF/DOL/visual states. | P1 |
| 274–852 | Object types | `ObjectMeta`, analytical objects, `VisualRef`, `ZigZagSegmentRef`. | P1; object-specific P2–P10 |
| 859–1,043 | Global inputs | Core/debug, visual transparency, killzones. | P1/P10 |
| 1,047–1,319 | Core utilities | TF normalization, tokens, logical IDs, meta/swing creation, safe lookup, transparency. | P1 |
| 1,327–1,428 | Global storage | Arrays/maps/queues for swing through MSS objects. | P1/P2–P9 |
| 1,535–3,977 | Swing engine | Inputs; raw high/low pivot detection; relation/hierarchy; finalization and dev renderers. | P2 |
| 3,901–7,013 | Liquidity engine | Liquidity classification/storage, EQ clusters, sweep lifecycle and quality. | P3 |
| 7,014–10,690 | Structure engine | Contexts, break classification, protected swings, BOS/CHoCH and MSS candidates. | P4 |
| 10,691–11,067 | Displacement/MSS | `f_isDisplacementCandle`, scores, object creation and candidate confirmation. | P5 |
| 11,068–11,451 | FVG | `f_detectFVG`, ID/state/fill/age handling. | P6 |
| 11,452–11,839 | OB/breaker | Opposing-candle lookup, OB ID/state, mitigation and breaker promotion. | P7 |
| 11,840–13,049 | Range/session | PD/OTE, rolling dealing range; session parsing/upsert/close. | P8/P8B |
| 12,414–13,006 | First visual layer | FVG/OB/range/session pools and render functions. | P10 |
| 13,050–13,703 | MTF + DOL | `request.security`, `f_buildMTFContexts`, alignment, DOL scoring/creation. | P9/P9B |
| 13,705–14,206 | Visual layer | Render structure, displacement, zigzag, swings, DOL and MTF. | P10 |
| 14,207–14,466 | Dashboard | Table sections for bias, structure, liquidity, PD, MTF, objects and DOL. | P11 |
| 14,467–14,557 | Object manager | Inputs and `f_trimObjectArray`. | P12 |
| 14,558–14,900 | Alerts | Event-driven `alert()` calls for sweep, structure, FVG/OB, DOL, killzone, MTF. | P15 |
| 14,901–15,137 | Assistant | Confluence component scores, setup classification and chart label. | P13 |

## Primary data and state

- `ObjectMeta` (274–292): logical identity, origin/confirmation/update timing and analytical quality/state metadata.
- `swingObjects` + `swingObjectById` (1,327–1,329): ordered swing database plus direct lookup; feeds liquidity and structure.
- Liquidity stores/queues (1,331–1,351): objects, active index, EQ clusters, sweep events and transition queue.
- Structure stores/queues (1,353–1,383; 1,417–1,428): contexts/events, protected-swing state, sequence/lifecycle/governance and MSS candidate indexes.
- Zone/context stores (1,385–1,410): displacement, FVG/OB maps, dealing ranges, sessions, MTF contexts and DOL map.
- Visual pools (12,414–12,420; 13,705–13,717): boxes/lines/labels for rendering only; `dashTable` (14,283) is dashboard state.

## Evidence-backed gaps / mismatches

| Priority | Location | Finding and why it matters |
|---|---:|---|
| P0 | 14,544–14,550 | Object-manager “visual” limits call `f_trimObjectArray` on **logical** swing/FVG/OB/liquidity arrays. This can delete analysis/history when a display budget changes, contradicting P2/P10/P12 independence and risking stale maps. |
| P0 | 2; whole file | P14 is absent: the declaration is `indicator()`, and there is no `TradeObject`, `strategy()` or `strategy.*` call. Backtest/journal/metrics cannot meet the spec. |
| P1 | 13,181–13,260 | MTF fetches candle direction/close/high/low only; `f_buildMTFContexts` constructs contexts with most required ID fields as empty strings (13,247–13,258). This is a proxy, not the P9 real context payload. |
| P1 | 14,558–14,900 | P15 has alert switches and calls, but no `AlertObject`, event-ID/type dedup key, cooldown or per-session cap. `alert.freq_once_per_bar` alone is not the specified transition-level dedup contract. |
| P1 | 14,901–15,137 | P13 produces a score/label only; no `SetupObject`, hard-gate state, POI/stop/target/invalidation or setup lifecycle is stored. |
| P1 | 11,840–12,070 | P8 range is derived from rolling highest/lowest prices; its `highSwingId`/`lowSwingId` fields (758–759) need verification because the source selection shown is not structural-swing based. |
| P2 | 1,880; 2,652; 4,277; 5,380; 9,371 | Engine-local dev drawing functions coexist with P10’s centralized visual-manager goal. Treat this as a migration/ownership audit: confirm they are debug-only and cannot emit production drawings. |
| P2 | 860–1,482; repository | Some debug/self-test hooks exist, but no saved compile token baseline, replay matrix, release log or regression suite proves P16 gates. TradingView-side validation is still required. |

## Proposed change order

1. **Protect analytical state:** split P12 logical-history limits from visual limits; stop trimming analytical arrays at 14,547–14,550 and synchronize every map/index on any true history eviction.
2. **Implement real MTF payload:** replace P9 proxy values with confirmed per-TF structure/liquidity/PD/session lineage, then validate reload/non-repaint behavior.
3. **Add stateful setup and alert contracts:** introduce `SetupObject` and `AlertObject`; implement hard gates, IDs, transitions, dedup, cooldown and session cap.
4. **Choose the P14 architecture:** retain this indicator as analysis-only and build a separate strategy script, or convert intentionally; then implement deterministic trade snapshots and metrics.
5. **Audit visual ownership and P8 source lineage:** centralize production draw calls; make dealing-range source explicit and fill swing IDs from valid structural swings.
6. **Establish P16 evidence:** compile/token baseline, test matrix, replay/reload checks and documented known limitations.

## Focused source-reading guide

- Swing/structure change: spec P2/P4 ranges and code 1,535–10,690.
- Liquidity/MSS/zone change: spec P3/P5–P7 and code 3,901–11,839.
- Range/session/MTF/DOL change: spec P8/P9 and code 11,840–13,703.
- Visual/dashboard/performance change: spec P10–P12 and code 12,414–14,557.
- Assistant/backtest/alerts/QA: spec P13–P16 and code 14,558–15,137 (plus P14 gap).
