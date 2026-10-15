# Colin Austin

Quantitative research and trading systems. I build the measurement apparatus
first and let it tell me when an idea doesn't work.

Most of what's here is infrastructure for answering one question honestly:
*does this edge survive contact with costs, out-of-sample data, and the number
of hypotheses I actually tested?* Usually the answer is no, and the repositories
below document that rather than hiding it.

---

### [futures-backtest](https://github.com/Elikem07/futures-backtest) — start here

A backtesting and statistical-audit harness for intraday micro-futures
(MES/MNQ/MGC), across 543 sessions.

The headline is a pair of **negative results**. Two candidate strategies were
built, swept, and audited to no edge:

- **Vol Divergence Short** looked like PF 1.68 on n=78. About 60 configurations
  were tested, putting the multiple-comparisons bar at t≈3.3–3.4; the best
  result anywhere in the audit reached t=2.54. It does not survive.
- **Yellow Box** (Market Profile value-area breakout/retest) came in at PF 0.84
  over n=892, negative in every symbol and both directions.

Two findings from that work are more interesting than the verdict. A 48-config
walk-forward sweep returned **0/48 profitable on train and 48/48 on test** — a
unanimous sign flip, which is a regime artifact rather than a discovery, caused
by the train/test boundary landing at a point where quarterly P&L changed sign.
And making an unreachable target *reachable* made profit factor **worse** (0.81
vs 0.93), because cutting winners short cost more than the low hit rate did.

Walk-forward splits, bootstrap confidence intervals, Bonferroni correction, and
a synthetic-data control that the engine is required to find no edge in.

### [mes-research](https://github.com/Elikem07/mes-research)

A measurement framework that deliberately stops short of designing a strategy.
It classifies every level touch into five reaction outcomes and estimates
`P(outcome | regime)` across 16 regime variables — because if the conditional
distribution matches the unconditional one, there is nothing to build on, and
that is worth learning before committing to an execution stack.

Point-in-time features (every bar carries an `available_at` timestamp so a
feature cannot be used before it existed), Benjamini–Hochberg FDR correction,
Wilson confidence intervals, and three cost tiers — a result that only survives
the optimistic tier is not a result.

### [options-auto-trader](https://github.com/Elikem07/options-auto-trader)

End-to-end intraday options **execution system**: pattern scanner, confluence
scoring, an IBKR order loop with limit-fill handling and timeouts, layered risk
limits (daily loss halt, per-trade premium cap, concurrency and churn bounds,
time stops, forced end-of-day flat), plus a backtester, parameter sweep and
bootstrap Monte Carlo.

Included as systems engineering — order management, state handling, failure
modes — rather than as an alpha claim.

### [trendscout](https://github.com/Elikem07/trendscout)

A production web platform, unrelated to trading: FastAPI + Next.js monorepo
with Postgres/pgvector, Celery workers, Clerk auth with verified webhooks, and
CI on GitHub Actions. 63 tests passing, ruff clean, mypy strict clean, no
Alembic drift.

Architecture worth noting: archive-before-transform ingestion (raw responses
committed durably before any transformation), Redis-backed quota budgets with
atomic refund, two-phase clustering, and PELT change-point detection for trend
labels. Revenue is reported only as ranges, never point estimates.

---

### Notes

Strategy parameters in the trading repositories are loaded from gitignored
TOML; only `*.example.toml` files with neutral placeholders are committed. Each
repo imports and runs from a fresh clone but produces no signal until
configured, and says so loudly at import.

These repositories were published together, so their commit history starts at
the point of publication rather than at the start of the work.
