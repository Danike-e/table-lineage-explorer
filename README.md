# Table Lineage Explorer

A single-file, offline HTML tool for exploring table dependencies and the APIs they feed.

Open `lineage.html` in a browser. No build step and no dependencies.

## Features

- Search tables; filter to **sources** (nothing upstream), **leaves** (nothing downstream), **isolated** tables and **APIs**
- Node diagram (force-directed, flowing left → right) or column layout, with pan, zoom and draggable nodes
- Per-table details: direct and transitive upstream/downstream, root sources, leaf tables and APIs fed, each copyable
- Light and dark themes; the last loaded data is remembered locally in the browser

## Loading data

Drag files onto the page, use **Load files…**, or **Paste…**. The format is auto-detected:

| Format | Example |
| --- | --- |
| CSV / TSV | `table,depends_on` (also `source,target`, `from,to`, `parent,child`); `;` separates multiple deps |
| Arrows | `raw.orders -> stg_orders -> fct_orders` |
| JSON | `{"table": ["dep1", "dep2"]}`, `[{"name": "x", "depends_on": [...]}]`, or a dbt `manifest.json` |
| SQL | `CREATE TABLE/VIEW … AS SELECT`, `INSERT INTO`, `MERGE`; dbt `ref()` / `source()` |

### APIs

Mark a node as an API with an `api:` prefix (`api:orders-service,marts.fct_orders`), an `api,table` CSV header,
or a `type` column / JSON field (`api`, `dashboard`, …). dbt exposures of type `application` are shown as APIs.

```csv
table,depends_on
staging.stg_orders,raw.orders
marts.fct_orders,staging.stg_orders
api:orders-service,marts.fct_orders
```

SQL parsing is regex-based: comma joins (`FROM a, b`) and dynamic SQL are not detected.
