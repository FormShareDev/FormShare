---
description: Data products are the downloadable artifacts FormShare generates from a form's repository — CSV, Excel, JSON, KML, and media archives, available as private files for the team or public files for partners.
---

# Data products

A **data product** is a downloadable file (or archive of files) that FormShare generates from a form's repository on demand. Products are the bridge between the live data sitting in FormShare and the world outside — analysts opening it in Excel or R, partners receiving public extracts, external systems consuming it through an API client, or your future self running a re-analysis next year.

## Where to find them

Products live under the form details page, on the **Products and tasks** tab. Each product type has its own card; opening a card shows the recent generations of that product, with download buttons, publish/unpublish controls, and a delete button on each one.

<!-- TODO screenshot: the form details page with the "Products and tasks" tab open and several product cards visible. -->

Products are only available for forms with a [repository](../../fundamentals/repositories/). Forms in the [testing stage](../../fundamentals/forms/#the-testing-stage) have a much more limited set of downloads (the testing-stage flat CSV and the media zip), no public/private distinction, and no product history.

## The built-in product types

FormShare ships with the following product types out of the box. Plugins can add more.

* **[Flat CSV](flat-csv-one-csv-file.md)** (private / public) — a single CSV file that denormalizes the form into one table. The easiest format to open in Excel for ad-hoc inspection, with the trade-off that repeats are duplicated.
* **[Zipped CSV](zipped-csv.md)** (private / public) — a Zip archive containing one CSV per table in the repository (main, repeats, lookups, multi-selects). The right format for analysts who want a normalized, lossless export.
* **[Excel](excel.md)** (private / public) — an `.xlsx` workbook with one sheet per table. Same content as the zipped CSV, but in a single file readable by Excel-native users.
* **[JSON](json.md)** (private / public) — a Zip archive of JSON records. The right format for downstream systems that consume nested data structures.
* **[KML](kml.md)** — a Keyhole Markup Language file for forms that include GPS data. Opens in Google Earth and most mapping software. KML is published as a single non-publishable variant (there is no "public KML" toggle, since GPS coordinates are often sensitive and you control their inclusion through the [data dictionary](../data-dictionary/marking-sensitive-fields.md)).
* **[Media](media.md)** — a Zip archive of every media file (photos, audio, video, signatures) that has been submitted with the form. Organized by submission ID. Like KML, this is a single variant.

The first four — Flat CSV, Zipped CSV, Excel, JSON — exist in both [private and public variants](private-vs-public-products.md). The public variant respects fields marked as [sensitive](../data-dictionary/marking-sensitive-fields.md); the private variant always includes the full data.

## Lifecycle of a product

The lifecycle of any product is the same:

1. **Request the product**. From the form details page, click **Export data**, pick the product type, and configure the options. (Different product types have different options — Flat CSV has rendering choices for lookups and multi-selects; JSON has none.) Click **Generate**.
2. **Background generation**. FormShare queues a Celery task to build the file. The Products and tasks tab shows the task as in-progress with a live progress log. Generation can take from seconds (a small form, a hundred submissions) to several minutes (large forms, complex repeats, ten of thousands of submissions).
3. **Ready to download**. When the task completes, the product card shows the newly generated file with its size and creation timestamp. Click **Download** to retrieve it.
4. **Publish, if applicable**. For products with a public variant, you can [publish](private-vs-public-products.md) the generated file to expose a public download URL.
5. **Delete or regenerate**. Old generations can be deleted manually; regenerating a product creates a fresh file from the current state of the repository.

{% hint style="info" %}
**Products are cached files, not live queries.** A product reflects the data as it was at the moment it was generated. If you fix a value through the [cleaning interface](../cleaning/in-a-repository/web-interface.md), already-generated products do not refresh. Generate the product again to pick up the change.
{% endhint %}

## Who can do what

* **Project owner and editors** can generate, download, publish, unpublish, and delete any product.
* **Read-only collaborators** can download private products that have already been generated, but cannot generate or publish.
* **Partners** can only download **public** products they have been [linked to](../../fundamentals/partners.md). They never see private products and they never see the generation interface.
* **Assistants** do not have access to products; their portal is focused on data collection and cleaning, not export.

## Practical advice

* **Generate a private product before any destructive operation**. Wiping all submissions with [Delete all submissions](../cleaning/working-with-submissions.md#delete-all-submissions) is irreversible. A private Excel export taken just before the wipe is your only safety net.
* **Pick the simplest product that fits the consumer**. Excel and Flat CSV are easy for non-technical users; zipped CSV and JSON are better for data scientists; KML is for cartographers. Don't ship a four-table zip CSV to a colleague who is going to open it in a spreadsheet.
* **Refresh products before sharing**. A product that was generated a month ago does not include the last month's edits and submissions. Regenerate before sending it on.
* **Mind the public/private distinction**. Public products are designed to be shareable; private ones contain everything you have. Generate the right variant for the consumer.

## What's next

* "[Private vs public products](private-vs-public-products.md)" explains the two variants and how publishing works.
* "[Flat CSV (One CSV file)](flat-csv-one-csv-file.md)" covers the single-file CSV format and its rendering options.
