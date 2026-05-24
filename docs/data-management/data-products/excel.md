---
description: Excel export packages every table from a form's repository as a separate sheet inside one .xlsx workbook — the right format for analysts who live in spreadsheets.
---

# Excel

The **Excel export** produces a single `.xlsx` workbook with **one sheet per table** in the repository. It is functionally the same data as the [zipped CSV](zipped-csv.md), just in Excel's native multi-sheet format. Use it when the recipient is going to open the file in Excel (or LibreOffice, Numbers, or Google Sheets) and you'd rather not ask them to unzip and import.

## Where you see it

On the form details page, click **Export data** → pick **Excel** (private or public). The product appears in the **Products and tasks** tab when the background task finishes; download the `.xlsx` directly from there.

## What's in the workbook

The workbook contains:

* A sheet named after the main table (typically `maintable`) — one row per submission for fields outside any repeat.
* One sheet per **repeat group** — e.g. `rpt_members`, `rpt_crops` — one row per repeat instance.
* Optional sheets for **multi-select tables** and **lookup tables**, depending on the toggles you set when generating.

<!-- TODO screenshot: an exported workbook open in Excel showing several sheets with their tab names. -->

Sheet names come from the table descriptions in the [data dictionary](../data-dictionary/). Excel imposes a **31-character limit** on sheet names, so very long descriptions are truncated.

Columns inside each sheet map to the field names in the dictionary, plus FormShare bookkeeping columns (submission ID, primary key, and — for repeat sheets — the row index in the repeat).

## Options at generation

Same options as the [zipped CSV](zipped-csv.md):

* **Lookup / select rendering**: code only, label only, or both.
* **Include multi-select tables**: adds a sheet per multi-select field.
* **Include lookup tables**: adds a sheet per `select_one` / `select_multiple` lookup.

There is also an instance-wide configuration setting that applies Excel data-type formatting: dates rendered as date cells, integers as numbers, decimals with the right precision. If your administrator has enabled it, the workbook is more directly usable in formulas; if not, all cells come through as text. You can always re-format cells in Excel after download.

## Row limits

Excel has a hard limit of **1,048,576 rows per sheet**. FormShare does not pre-warn you if a table is approaching that limit — for typical forms (a few thousand submissions, a few hundred fields per submission) this is never an issue, but for very large repositories with rich repeats, you can hit it.

{% hint style="warning" %}
**Big datasets**: If your largest table has more than ~1 million rows, the Excel export will either fail or truncate silently. For datasets that big, use the [zipped CSV](zipped-csv.md) or [JSON](json.md) instead — CSV and JSON have no row limit.
{% endhint %}

## When to use Excel

* Your recipient is a spreadsheet-native user. Excel is universally readable; zipped CSV requires unzipping.
* You want to look at multiple tables side-by-side without flipping between files. The sheet tabs make this easy.
* You'll add charts, pivot tables, or formulas on top of the data — Excel is built for that.
* You want to email a single attachment instead of a Zip archive.

## When *not* to use Excel

* Your dataset is very large (close to a million rows in any one table). Use [zipped CSV](zipped-csv.md).
* You're loading the data into R, Stata, Python, or SQL. These tools speak CSV more directly than XLSX, even though they can read XLSX too.
* You need the data as a tree (nested submissions). Use [JSON](json.md).

## What's next

* "[Zipped CSV](zipped-csv.md)" — the CSV alternative with the same content.
* "[Flat CSV (One CSV file)](flat-csv-one-csv-file.md)" — the denormalized single-file variant.
* "[JSON](json.md)" — for programmatic consumers.
