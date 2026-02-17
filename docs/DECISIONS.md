# ⚖️ Architecture Decision Record (ADR)

## ADR-001: Data Access Pattern
- **Date:** 2026-02-17
- **Decision:** Use strictly GAQL (v1) with Dot-Notation.
- **Rationale:** More performant and future-proof than legacy `Selector` methods.

## ADR-002: Calculation Logic Location
- **Date:** 2026-02-17
- **Decision:** Perform all math (ROAS, CPA) in JavaScript, not GAQL.
- **Rationale:** GAQL does not support calculated fields or aggregations like SUM/AVG.

## ADR-003: Spreadsheet Batching
- **Date:** 2026-02-17
- **Decision:** Mandatory use of `setValues()`.
- **Rationale:** `appendRow()` is too slow for scripts with >50 rows.
