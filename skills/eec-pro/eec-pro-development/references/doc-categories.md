# EEC Pro doc index — category map

The `eecpro-rag` index (`https://rageecpro.covaga.xyz`, MCP name `eecpro-rag`) holds the
**EPLAN EEC Pro 2026** documentation: ~1,648 pages / ~6,760 vectors, embedded with
`bge-base-en-v1.5`, searchable semantically via `eecpro_search` or `POST /search`.

`eecpro_search` parameters:

| Param | Type | Notes |
|---|---|---|
| `query` | string | Natural language, English works best |
| `topK` | int | 1–20, default 5 |
| `category` | string | Optional — one of the 36 categories below. Scoping pays off: the index is small enough that unfiltered queries can surface a same-named concept from the wrong module. |

## Reference categories (`ref*` — lookup material)

| Category | Covers | Reach for it when… |
|---|---|---|
| `refformulas` | **Formula language** (~317 pages): functions, operators, expressions | Computing a parameter value, writing a condition, string/date/number functions |
| `admin` | Installation, configuration, **VMArgs** (~304 pages) | Setup, JVM/memory tuning, licensing, system options |
| `refformui` | Form-UI reference (~84 pages) | Building custom configuration dialogs |
| `refcommands` | Commands (~79 pages) | Any "how do I make EEC *do* X" step in a pipeline |
| `refscripting` | Scripting reference (~58 pages) | Script API surface, entry points |
| `refjobserver` | Job Server reference | Configuring/queuing generation jobs |
| `refcsv` / `refexcel` / `refxml` / `refimx` | Import/interface formats | Bulk-loading configuration values from files |
| `reftextgen` | Text generation | Producing Word/text deliverables |
| `refregex` | Regex reference | Pattern matching inside formulas/config |
| `refquickrefguide` | Quick reference | Compact cheat-sheet material |

## Product areas (`eec*` / `gui` / `main` / `concept` / `eecbase`)

| Category | Covers |
|---|---|
| `concept` | Concepts (~58 pages) — the mechatronic model, product structure, terminology |
| `eecbase` | Base functionality (~119 pages) — core UI and operations |
| `main` / `gui` | Main application / GUI elements |
| `eececad` | ECAD module (~47 pages) — generation into EPLAN Electric P8 |
| `eecpropanel` | Pro Panel module — 3D mounting layouts |
| `eecplc` | PLC module (~47 pages) — PLC program generation |
| `eecgraph2d` | Graph2D — free-form graphical documentation |
| `eectext` | Text module — document deliverables |
| `eecoffice` | Office integration |
| `eecsap` | SAP integration |
| `eecscripting` | Scripting environment (pairs with `refscripting`) |
| `eecjobserver` | Job Server product side |

## Tutorials (`tut*` — worked examples)

`tutorial` (general) · `tutmechatronic` (build the mechatronic model) ·
`tutp8` (generate into P8) · `tutpropanel` · `tutstep7` (PLC via STEP 7) ·
`tutjobs` (Job Server) · `tutimp` (import) · `tutgraph2d` · `tuttext` · `tutword`

## Query strategy

1. Know the subsystem? Set `category`. Unsure? Query `concept`/`eecbase` first, then
   the specific module.
2. Formula/condition questions → `refformulas` *first*, always.
3. Prefer several narrow queries ("formula function read parameter value",
   "Job Server queue generation command") over one broad one.
4. The REST endpoint mirrors the MCP: `POST /search` with
   `{"query": "...", "topK": 5, "category": "..."}`; plus `GET /stats`, `GET /health`.
