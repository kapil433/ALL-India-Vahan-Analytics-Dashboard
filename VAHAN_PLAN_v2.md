# ALL INDIA VAHAN ANALYTICS PLATFORM — REVISED MASTER PLAN v2
### 4-Wheeler Passenger Vehicle Industry | 2013–2026

---

## 1. DATA MODEL (REVISED)

### Financial Year Mapping Rule
```
Calendar Year File  →  Month        →  Financial Year
CY 2013 file           Jan, Feb, Mar  →  FY 2012-13  ← belongs to PREVIOUS FY
CY 2013 file           Apr–Dec        →  FY 2013-14  ← belongs to CURRENT FY
CY 2014 file           Jan, Feb, Mar  →  FY 2013-14  ← completes 2013-14
CY 2014 file           Apr–Dec        →  FY 2014-15
...and so on
```

**Key insight**: FY 2013-14 is assembled from:
- Apr–Dec from the CY 2013 file (9 months)
- Jan–Mar from the CY 2014 file (3 months)

### Master Long-Format Schema
Every row represents one **Maker × Fuel × Month** data point with BOTH time labels:

| Column | Type | Example |
|---|---|---|
| `cal_year` | int | 2013 |
| `month` | int (1–12) | 4 |
| `month_name` | str | "APR" |
| `fy` | str | "2013-14" |
| `fy_sort` | int | 201314 (for sorting) |
| `maker` | str (canonical) | "Maruti Suzuki" |
| `fuel` | str (canonical) | "Petrol" |
| `registrations` | int | 48,468 |

### Financial Years Available
`2012-13` through `2026-27`
(Note: 2012-13 only has Jan–Mar from CY2013 file — partial; 2026-27 only has Apr–Dec from CY2026 — partial)

---

## 2. KPI FRAMEWORK

### Filter Hierarchy (top of every page)
```
[Geography ▼]  [Year Type ▼]  [Year/Period ▼]  [Month ▼]  [OEM ▼]  [Fuel ▼]
 All India       Calendar Yr    2024             All         All       All
 State: XX       Financial Yr   2024-25          Jan
```

**Geography**: All India | [State name when state file uploaded]
**Year Type**: Calendar Year | Financial Year
**Year/Period**: Dropdown populated based on Year Type selection
**Month**: All | Jan | Feb | Mar … Dec (disabled when Year Type = Financial Year — shows full FY)
**OEM**: All | [Individual maker]
**Fuel**: All | CNG | Diesel | EV | Petrol | Strong Hybrid

---

### KPI Cards (Row 1 — always visible)

| KPI | Definition | Comparison |
|---|---|---|
| **Total Volume** | Sum of registrations for selected filters | vs Prior Period (MoM or YoY) |
| **YoY Growth %** | (Current period – Prior year same period) / Prior year | Arrow indicator ↑↓ |
| **MoM Growth %** | (Current month – Prior month) / Prior month | Only shown in month view |
| **EV Share %** | EV regs / Total regs × 100 | vs Prior period EV share |
| **CNG Share %** | CNG regs / Total regs × 100 | vs Prior period |
| **Top OEM Share %** | #1 OEM regs / Total regs × 100 | Show OEM name + share |
| **Green Vehicle Share %** | (EV + Strong Hybrid + CNG) / Total × 100 | vs Prior period |

---

## 3. DASHBOARD PAGES & CHARTS

### PAGE 1 — OVERVIEW
```
[KPI Cards Row: 7 cards]
[Volume Trend Line]              [Fuel Mix Donut — selected period]
  CY: 2013–2026 annual totals     5 segments with % labels
  FY: 2012-13 – 2025-26

[YoY Growth Bar Chart]           [Top 5 OEM Bar (horizontal)]
  Each year = bar, colored        Selected period ranking
  Green=growth, Red=decline
```

### PAGE 2 — FUEL TRENDS
```
[KPI Cards: EV Share, CNG Share, Diesel Share, Green Vehicle Share]

[Stacked Area Chart: All 5 fuels 2013–2026]
  Toggle: Absolute volume | % share

[Monthly Seasonality Heatmap]    [Fuel YoY Growth Table]
  12 months × years grid          Each fuel's % change per year
  Color intensity = volume

[EV Emergence Line Chart]        [CNG Trend Line Chart]
  EV volume 2013–2026             CNG vs Petrol share over time
```

### PAGE 3 — OEM ANALYSIS
```
[KPI Cards: Top OEM share, HHI concentration, Maruti dominance %]

[Animated Bar Race]              [Market Share Pie — selected year]
  Top 10 OEMs by year             Toggle: Volume | Share %
  Play/pause animation

[OEM × Fuel Matrix Heatmap]      [Individual OEM Deep Dive]
  Rows = OEMs, Cols = Fuels        Select OEM → see fuel mix
  Cell = volume or share           YoY trend + monthly breakdown

[OEM Market Share Line]
  Selected OEMs over years (multi-select)
  Toggle: Absolute | % share
```

### PAGE 4 — EV & GREEN VEHICLES
```
[KPI Cards: EV total, EV CAGR, Strong Hybrid total, Green % total]

[EV Volume + Share Dual Axis]    [Top EV Makers Horizontal Bar]
  Volume bars + Share % line       Tata, Mahindra, MG, Hyundai...

[EV + Hybrid + CNG Combined]     [EV Monthly Seasonality]
  "Green vehicle" total trend      Monthly EV regs heatmap
```

### PAGE 5 — UPLOAD & VALIDATE (State Data Portal)
```
┌─────────────────────────────────────────────────────────────┐
│  UPLOAD STATE DATA                                          │
│  Drop your .xlsx file here or click to browse              │
│  Format: same as All India files                            │
├─────────────────────────────────────────────────────────────┤
│  VALIDATION PANEL (appears after upload, BEFORE commit)     │
│  ✓ File format detected: xlsx                               │
│  ✓ Rows found: 171                                          │
│  ✓ Calendar Year detected from filename: 2025               │
│  ✓ Fuels found: CNG, Diesel, EV, Petrol, StrongHybrid      │
│  ✓ Makers found: 38 raw names                               │
│  ⚠ 3 makers unrecognized → will map to "Others"            │
│    - SOME LOCAL BRAND LTD → Others                         │
│    - REGIONAL MOTORS → Others                               │
│  ✓ Numeric parsing: 171/171 rows clean                      │
│  ✓ FY mapping: Apr-Dec → 2025-26, Jan-Mar → 2024-25        │
│                                                             │
│  PREVIEW TABLE: First 10 rows of cleaned data              │
│  [Maker] [Fuel] [JAN] [FEB]...[DEC] [Total]               │
│                                                             │
│  [✗ Cancel]                    [✓ Commit & Analyze →]      │
└─────────────────────────────────────────────────────────────┘
│  POST-COMMIT: State vs All India Comparison                 │
│  All charts show: State data | All India benchmark overlay  │
└─────────────────────────────────────────────────────────────┘
```

---

## 4. FILE FORMAT SPECIFICATION (Upload Contract)

Filename must follow: `{STATE_NAME}_{CY_YY}.xlsx` or `ALL_INDIA_{CY_YY}.xlsx`
- Examples: `Maharashtra_CY_25.xlsx`, `Delhi_CY_24.xlsx`, `ALL_INDIA_CY_25.xlsx`

The platform auto-detects:
- **State** from filename (before `_CY_`)
- **Calendar Year** from `CY_YY` pattern
- **FY** derived via mapping rule

Column contract (flexible headers, auto-detected):
```
Col 1: Serial (ignored)
Col 2: Maker name
Col 3: Fuel type
Col 4–15: JAN through DEC (any case, must be month names)
Col 16: Total (re-computed by platform anyway)
```

Validation checklist before commit:
1. ✓ File is .xlsx or .csv
2. ✓ Minimum 3 columns + month columns detected
3. ✓ Fuel column has recognized values (fuzzy match allowed)
4. ✓ At least one maker maps to canonical list
5. ✓ All numeric cells parse to valid integers
6. ✓ Year detected from filename or user-entered
7. ⚠ Warn if >20% makers fall into "Others"
8. ✓ Show row count, date range, total volume preview

---

## 5. TECH STACK

```
GitHub Repo: vahan-analytics/
├── index.html          ← Entry, router, nav shell
├── data/
│   └── vahan_master.json    ← 2MB, pre-built, loaded on start
├── pages/
│   ├── overview.js
│   ├── fuel.js
│   ├── oem.js
│   ├── ev.js
│   └── upload.js
├── js/
│   ├── dataEngine.js   ← Filter, aggregate, compute KPIs
│   ├── charts.js       ← All Chart.js / D3 definitions
│   ├── cleaner.js      ← Same pipeline as Python (runs in browser via SheetJS)
│   ├── makerMap.js     ← Canonical maker mapping dictionary
│   └── fuelMap.js      ← Fuel category mapping
└── css/
    └── style.css
```

**Libraries (CDN only — no build step, pure GitHub Pages):**
- `Chart.js 4` — line, bar, stacked area, donut
- `D3.js` — heatmap, animated bar race
- `SheetJS (xlsx.js)` — read uploaded .xlsx client-side
- `Fuse.js` — fuzzy maker name matching in upload validator

**Hosting**: GitHub Pages (same as calculator — push to `gh-pages` branch, live URL)

---

## 6. BUILD SEQUENCE

| Step | Task | Status |
|---|---|---|
| ✅ 1 | Audit raw files + understand structure | Done |
| ✅ 2 | Python cleaning script (FY mapping, maker map, fuel map) | Done |
| ✅ 3 | Generate `vahan_master.json` with both CY + FY dimensions | Done |
| 🔲 4 | Revised Master Plan (this document) | Done |
| 🔲 5 | Build `index.html` shell + navigation + filter bar | Next |
| 🔲 6 | Overview page: KPI cards + trend line + donut |  |
| 🔲 7 | Fuel Trends page: stacked area + heatmap |  |
| 🔲 8 | OEM Analysis page: bar race + matrix heatmap |  |
| 🔲 9 | EV & Green page |  |
| 🔲 10 | Upload & Validate portal |  |
| 🔲 11 | State vs All India comparison overlay |  |
| 🔲 12 | GitHub Pages deploy |  |

---

## 7. DESIGN DIRECTION
**Name**: ALL INDIA VAHAN ANALYTICS PLATFORM — 4-Wheeler PV Industry
**Aesthetic**: Dark industrial dashboard — deep charcoal background, electric blue accents, crisp data typography
**Font**: IBM Plex Mono (numbers/data) + IBM Plex Sans (labels/headings)
**Color palette**:
- Background: `#0f1117`
- Surface cards: `#1a1d27`
- Electric blue accent: `#3b82f6`
- Fuel colors: Petrol `#6366f1`, Diesel `#64748b`, CNG `#22c55e`, EV `#06b6d4`, Strong Hybrid `#f59e0b`
- OEM colors: Maruti `#e63946`, Hyundai `#3b82f6`, Tata `#06b6d4`, Mahindra `#f59e0b`, others distinct

---

*Data pipeline: complete. Ready to build the dashboard.*
