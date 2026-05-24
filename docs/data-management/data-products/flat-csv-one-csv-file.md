---
description: Flat CSV is a single CSV file that denormalizes a form into one wide table — easy to open in Excel, but at the cost of duplicating parent rows for every child row.
---

# Flat CSV (One CSV file)

The **flat CSV** export is the simplest format FormShare produces: a single CSV file that contains every value from every submission. Where the [zipped CSV](README.md#the-built-in-product-types) gives you one file per table (main, repeats, lookups, multi-selects), the flat CSV gives you one file with everything fused together. It is the file you hand to a colleague who only knows how to open spreadsheets.

The trade-off is denormalization: forms with repeats end up with parent-row data duplicated across every child row, and forms with rich multi-selects end up with wide rows. For very simple forms, this is invisible; for complex forms, you may prefer the zipped CSV.

## Where you see it

Flat CSV is available in two places:

* On the form details page of a form with a [repository](../../fundamentals/repositories/), as part of the **Products and tasks** tab. There are two variants: **Flat CSV — private** and **Flat CSV — public**. See "[Private vs public products](private-vs-public-products.md)".
* On the form details page of a form in the [testing stage](../../fundamentals/forms/#the-testing-stage), as the "Download data in CSV format" option. Testing-stage CSVs are private only — there is no public/private distinction before a repository exists.

The two share the same flat structure but the testing-stage version is simpler: it does not resolve lookup labels or multi-select rendering options. The repository version exposes the full set of choices described below.

## How repeats are flattened

If your form has a repeat group — for example, "household members" inside a household questionnaire — the flat CSV uses **one row per child record**, with the parent's columns repeated.

A household with three members produces three rows in the flat CSV, each carrying:

* The same parent-level data (head of household's name, GPS, etc.).
* The discriminating identifier (the row index of the repeat).
* The child's own data (member's name, age, sex, …).

A second-level repeat (e.g. "household members" → "member's children") multiplies further: a household with three members who each have two children produces six rows for that household in the flat CSV.

For forms without any repeats, the flat CSV has exactly one row per submission.

{% hint style="info" %}
**When the duplication gets in the way**: For forms with deeply nested repeats and many submissions, the flat CSV grows large quickly and parent-level analytics get awkward (you have to remember to deduplicate). At that point, switch to the zipped CSV — it gives you a relational view with the join keys already in place.
{% endhint %}

## Lookup field rendering

For `select_one` fields, you can choose how options appear in the CSV when you generate the product:

* **Code only** — the column contains the option code (`1`, `2`, `m`, `f`, …). The default and the most compact.
* **Label only** — the column contains the human-readable label (`"Yes"`, `"No"`, `"Male"`, `"Female"`, the option name in the form's primary language).
* **Code and label** — two columns per field: `<field>` with the code and `<field>_label` with the label.

Pick **code only** when handing the file to a script or a statistical tool that expects coded data; pick **label only** when the recipient is going to read the CSV in a spreadsheet without the codebook; pick **both** when you don't know which the recipient wants and you can spare the columns.

## Multi-select rendering

For `select_multiple` fields, the flat CSV offers the same three modes:

* **Code only** — one column per field, with selected codes joined by a separator (e.g. `1 3 5`).
* **Label only** — one column per field, with selected labels joined by a separator.
* **Code and label** — two columns per field, one with codes joined, one with labels joined.

Alternatively — and this is the better option for analytical work — you can opt to **include multiselect tables**. That keeps the parent-level fields in the flat CSV but produces an extra file per multi-select with one row per (submission, selected option) pair. Use this when you need to count or filter by individual options without parsing joined strings.

## Generating the file

From the form details page, click **Export data** → pick **Flat CSV** (private or public) → set the rendering options for lookups and multi-selects → click **Generate**.

<!-- TODO screenshot: the "Export data" dialog with the Flat CSV options selected. -->

FormShare queues a background task. When it finishes, the file appears under the form's **Products and tasks** tab; download it from there. Like every product, the file is cached — generate again to pick up newer submissions or cleaning edits.

## When to use flat CSV

* **Quick inspection in Excel**: opening one CSV with everything in it is faster than opening four.
* **Sharing with non-technical recipients**: most people understand a single spreadsheet, not a Zip of relational tables.
* **One-shot analyses where deduplication is easy**: e.g. counting submissions by region — pick the parent fields, deduplicate by submission ID, done.

## When to prefer zipped CSV instead

* **The form has nested repeats**. The flat CSV's row count explodes; the zipped CSV gives you a clean relational view.
* **You're loading into R, Stata, Python, or SQL**. Most of these expect normalized tables — you'd otherwise have to re-normalize the flat CSV manually.
* **Multi-selects are central to your analysis**. The multi-select join-table in the zipped CSV is easier to filter and count than the joined-string column in the flat CSV.
* **The form has tens of thousands of submissions and several repeats**. The flat CSV file size grows faster than the zipped CSV by a wide margin.

## What's next

* "[Data products](README.md)" — the full list of product types and the export workflow.
* "[Private vs public products](private-vs-public-products.md)" — choosing the right variant for the audience.
* "[Marking fields as sensitive](../data-dictionary/marking-sensitive-fields.md)" — controlling which columns appear in the public variant.
