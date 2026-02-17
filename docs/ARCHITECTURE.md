# 🏗️ System Architecture: Google Ads Scripting Engine

## 🔄 Data Flow Pipeline


1. **Authentication:** Uses native `AdsApp` authorization.
2. **Fetch Layer:** GAQL queries fetch raw nested objects (defined in `SPEC.md`).
3. **Processing Layer:** - Unit conversion (Micros to Currency).
   - Division-by-zero protection.
   - JS-side aggregation (as GAQL has no GROUP BY).
4. **Output Layer:** Batch write to Google Sheets via `setValues()`.

## 🗺️ Logical Components
- **Main Entry:** `main()` function orchestration.
- **Processors:** Pure functions for metric calculations.
- **Adapters:** Formatting logic for Google Sheets.

## 🛠️ Performance Guardrails
- **Timeouts:** Scripts must complete within 30 mins (27 min soft cap).
- **Memory:** Use iterator `.hasNext()` to avoid loading massive arrays into memory.
