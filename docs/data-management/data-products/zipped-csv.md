---
description: Zipped CSV is the relational export — one CSV per table in the repository (main, repeats, lookups, multi-selects), packaged together in a Zip archive.
---

# Zipped CSV

A **zipped CSV** export is what you reach for when you want to load FormShare data into R, Stata, Python, SQL, or any tool that prefers normalized tables. It is a Zip archive containing **one CSV per table** in the form's repository — exactly the layout described in "[How does FormShare stores my data?](../../fundamentals/repositories/how-does-formshare-stores-my-data.md)", without the flattening that the [flat CSV](flat-csv-one-csv-file.md) applies.

## Where you see it

On the form details page of a form with a repository, click **Export data** → pick **Zipped CSV** (private or public). Like every product, this generates a [private or public variant](private-vs-public-products.md) depending on what you choose, and lands in the **Products and tasks** tab when the background task finishes.

## What's in the zip

At minimum, the archive contains one CSV per **data table** in the form:

* `maintable.csv` — the main submission table (one row per submission for the fields outside any repeat).
* One CSV per **repeat group**, named after the repeat — `rpt_members.csv`, `rpt_crops.csv`, and so on. Each row is one repeat instance, with a foreign key back to the parent row.

Optionally — controlled by toggles when you generate the product — the archive can also contain:

* **One CSV per multi-select field** (filename pattern `<parent-table>_msel_<field>.csv`). Each row is one (submission, selected option) pair. Without this, multi-selects are condensed into a single column in the parent CSV.
* **One CSV per lookup table** (filename pattern `lkp_<list>.csv`). These are the reference tables that hold option codes and labels for `select_one` and `select_multiple` choices.

<!-- TODO screenshot: the "Export data" dialog for Zipped CSV with the multiselect-tables and lookup-tables toggles visible. -->

Each CSV's columns are the field names from the repository — the same names you see in the [data dictionary](../data-dictionary/) — plus standard FormShare bookkeeping columns: the **submission ID** (`rowuuid`), the **primary key**, and (for repeat tables) the row index inside the repeat.

## Options at generation

When you click **Generate**, you pick:

* **Lookup / select rendering**: how `select_one` fields appear in their parent CSV.
  * **Code only** — the column contains the option code (`1`, `m`, …). Most compact, the right pick for analytical work.
  * **Label only** — the column contains the human-readable label.
  * **Code and label** — two columns per field, suffixed `_label` for the label.
* **Include multi-select tables** — adds a CSV per multi-select. Recommended when you want to filter, count, or join on individual options without parsing joined strings.
* **Include lookup tables** — adds a CSV per `select_one` / `select_multiple` lookup. Useful when you want the reference data alongside the submission data, e.g. to build dashboards that need labels and codes both.

## When to use zipped CSV

* You're loading data into a statistical tool that expects normalized tables (R `data.frame`, pandas DataFrames, Stata, SQL imports). Each CSV maps to one table; join keys are already in place.
* The form has repeats and you want to analyse parent and child levels separately. Flat CSV duplicates parent data per child row, which complicates parent-level analytics; zipped CSV keeps the levels distinct.
* The form has many multi-selects and you want to count individual options. Multi-select tables let you `GROUP BY option` without splitting strings.
* You expect the file to be consumed by a script. Zipped CSV is easier to parse programmatically than Excel or flat CSV with their formatting quirks.

## When *not* to use zipped CSV

* You're handing the file to a non-technical recipient who only knows how to double-click an Excel icon. Give them the [Excel](excel.md) or [flat CSV](flat-csv-one-csv-file.md) instead.
* You only need a one-off look at the data. Flat CSV is faster to open and read.
* You need the data as a tree of nested objects (one document per submission with sub-arrays for repeats). Use [JSON](json.md) instead.

## Practical tips

* **Filenames in the zip reflect the table descriptions in the data dictionary.** If you renamed a repeat's description, the CSV filename uses the new description.
* **Encoding is UTF-8 with a header row.** All text fields are quoted; numeric and date fields are not.
* **Foreign keys connect tables.** A row in `rpt_members.csv` carries the parent's primary key plus its own row index. A row in `maintable_msel_languages.csv` carries the main table's primary key plus the selected option's code.
* **The zip is generated once and cached.** If you generate, download, then submit more data, re-generate to get the new rows. Same as any other product.

## What's next

* "[Excel](excel.md)" — a workbook variant for spreadsheet-native consumers.
* "[JSON](json.md)" — a tree-structured variant for programmatic consumers.
* "[Flat CSV (One CSV file)](flat-csv-one-csv-file.md)" — the denormalized single-file alternative.
