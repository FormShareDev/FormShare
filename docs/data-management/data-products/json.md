---
description: JSON export delivers the form's data as a Zip of JSON files — one per table, each an array of objects — for downstream systems that consume structured data programmatically.
---

# JSON

The **JSON export** produces a Zip archive containing **one JSON file per table** in the repository. Each JSON file is an array of objects, one object per row in the table. Use this format when the consumer is a program, an API, a downstream pipeline, or anything else that prefers structured records over delimited text.

## Where you see it

On the form details page, click **Export data** → pick **JSON** (private or public). The product appears in the **Products and tasks** tab when the background task finishes. Download the `.zip` from there.

## What's in the zip

* `maintable.json` — an array of submission objects, one per submission. Each object has a key per field in the main table.
* One JSON file per **repeat group** — e.g. `rpt_members.json` — an array of repeat-instance objects, each carrying a foreign key back to the parent submission.
* Optionally, one JSON file per **multi-select table** and one per **lookup table**, depending on the toggles set at generation.

A typical `maintable.json` looks like this:

```json
[
  {
    "rowuuid": "9c84c1d0-a8e3-4e69-9c2a-e2f53bca0f1a",
    "household_id": "H-001",
    "head_name": "Maria",
    "household_size": 5,
    "gps": "-1.234 36.789 0 0"
  },
  {
    "rowuuid": "fc3d8a51-…",
    "household_id": "H-002",
    "...": "..."
  }
]
```

Repeat files have the same shape — an array of objects, each linked back to its parent through the parent's primary key.

## Options at generation

Same as the [zipped CSV](zipped-csv.md) and [Excel](excel.md) exports:

* **Lookup / select rendering**: code only, label only, or both. With "code and label" enabled, FormShare emits separate keys for the code and the label of each option.
* **Include multi-select tables**: adds one JSON file per multi-select field. Each entry is one (submission, selected option) pair.
* **Include lookup tables**: adds one JSON file per `select_one` / `select_multiple` lookup, holding option codes and labels.

## The shape of the JSON

FormShare's JSON export is **table-oriented**, not document-oriented. Each table is a flat array of objects — there is no nested "submission with embedded repeats" structure. If you want one document per submission with sub-arrays of repeats, build that yourself by joining the table files on the primary key.

This decision exists because:

* Flat per-table JSON streams scale much better than deeply nested documents.
* Most downstream systems (data warehouses, BI tools, ETL pipelines) prefer one stream per logical entity.
* Producing nested documents requires a join order that depends on the form's structure; the consumer is in a better position than the exporter to decide how to nest.

## When to use JSON

* You're feeding data into a programmatic pipeline (ETL job, ML model, API client).
* You're loading into a document store (Elasticsearch, MongoDB, BigQuery) that prefers JSON.
* You need structured types (booleans, numbers, nulls) preserved — JSON keeps them, CSV does not.
* The downstream consumer doesn't already speak CSV gracefully (and most do, but JSON is sometimes easier).

## When *not* to use JSON

* The recipient is going to open the file in a spreadsheet. Use [Excel](excel.md) or [flat CSV](flat-csv-one-csv-file.md).
* You only need a quick look. JSON in raw form is harder to read than CSV.
* You're working in R or Stata. Both speak CSV more idiomatically than JSON.

## Practical tips

* **UTF-8 encoding** throughout. JSON spec mandates UTF-8 for stored files.
* **Empty values are `null`**, not empty strings. Distinguish "not collected" from "explicitly empty" if your form does.
* **Multi-select fields without the multi-select tables option** are emitted as space-separated strings of codes (matching the CSV behavior). Turn on the multi-select tables toggle for proper structured representation.
* **The zip is regenerated on demand.** Cached after generation; download the same file repeatedly. To pick up new submissions, regenerate.

## What's next

* "[Zipped CSV](zipped-csv.md)" — same data in CSV format.
* "[Excel](excel.md)" — same data as a workbook.
* "[KML for geographic data](kml.md)" — for forms with GPS submissions.
