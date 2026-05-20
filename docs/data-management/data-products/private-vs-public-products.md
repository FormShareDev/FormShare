---
description: Most FormShare exports come in two variants — private (full data, team-only) and public (sensitive fields handled, shareable via download link). Publishing is a separate action from generation.
---

# Private vs public products

The major FormShare export types — Flat CSV, Zipped CSV, Excel, and JSON — each come in **two variants**: a **private** product for the project team, and a **public** product that can be published and shared externally. Picking the right variant for a given download is a privacy decision, not a technical one, and FormShare gives you both the tools and the workflow to keep them straight.

This page covers what's different between the two variants and how publishing works. For the privacy mechanic itself — choosing which fields are sensitive — see "[Marking fields as sensitive](../data-dictionary/marking-sensitive-fields.md)".

## What's different in a public product

A public product is generated from the same underlying data as a private product, but FormShare applies the form's [sensitive-field configuration](../data-dictionary/marking-sensitive-fields.md) when producing the output. Concretely:

* Fields marked **sensitive with the exclude protection** are removed from the public product. The column does not appear in the CSV; the key does not appear in the JSON.
* Fields marked **sensitive with the recode protection** appear in the public product with replacement values. Distinct real values are mapped to sequential codes (`1`, `2`, `3`, …), so analysts can still group, count, and join — but they cannot trace a row back to the real subject.
* Fields marked **sensitive with the unlink protection** appear with the foreign-key reference broken — the column is null in the public product.
* Encrypted fields are always masked in the public product.

Everything else is identical between private and public: the same submissions, the same repeats, the same multi-select rendering, the same row counts. The two products differ only in how the sensitive columns are handled.

## What's different in a private product

A private product includes the form's data **exactly as it sits in the repository**, with no masking, no exclusion, and no recoding. Sensitive flags do not apply.

This is intentional: the project team needs the full data to clean, analyse, audit, and back up. The sensitive-field configuration is a safeguard against accidental sharing — it doesn't restrict what the people responsible for the data can see.

## Who can download which

* **Private products** are only downloadable by users with access to the project: the project owner, collaborators of all types, and read-only collaborators. They are never exposed through a public URL.
* **Public products** are downloadable by anyone with the public link **once published** (see below). Before publishing, they behave like private products — only the team can see them.
* **Partners** linked to the project receive public products only. They cannot generate exports, cannot see private products, and cannot trigger a publish.

## Publishing a public product

Generating a public product does not, by itself, make it shareable on the open web. Publishing is a separate, deliberate action.

1. Generate the public variant: from the form details page, click **Export data**, pick the public flavour of the format you want (e.g. **Excel — public**), set the options, and click **Generate**.
2. Wait for the background task to finish. The product card refreshes when the file is ready.
3. Click **Publish** on the new generation. FormShare assigns it a public URL and records who published it and when. The button switches to **Unpublish**.

Once published, the public URL can be shared with partners, donors, or external collaborators. Anyone with the URL can download the file — there is no authentication on a public link. Treat the URL as the credential.

> Add screenshot of a product card with both a "Publish" button on one generation and an "Unpublish" button on another, showing the published label and timestamp.

To revoke public access, click **Unpublish** on the generation. The public URL stops working immediately, but the file remains in your team's view as a regular private download.

{% hint style="warning" %}
**Public means "open on the internet."** A published product has no password, no expiry, and no audit beyond the publish/unpublish history. Anyone who knows the URL — and anyone who guesses it — can download it. Only publish files that you would be comfortable putting on a public website.
{% endhint %}

## Generating both variants

The variants are independent. You can generate only a private product, only a public product, or both. There is no "convert private to public" operation — if you need a public version, generate it. FormShare keeps the two product types side by side on the form details page so it's clear which one is which.

A common workflow:

1. **Always** generate a fresh private product before sharing anything externally. Keep it for the team's own records and to use during analysis.
2. Generate the matching public product only when you need to share. Inspect it (open the file, check that the sensitive columns are masked) before publishing.
3. Publish, share the URL, and unpublish when the sharing window is over.

## When publishing fails

If publishing a product gives you an error, the usual cause is that no sensitive fields are configured on the form. The public variant is generated regardless, but reviewing the [sensitive-field configuration](../data-dictionary/marking-sensitive-fields.md) is worth doing before exposing a public link — you want to make sure the export actually masks what you expect it to mask.

## What's next

* "[Marking fields as sensitive](../data-dictionary/marking-sensitive-fields.md)" — the underlying privacy mechanic.
* "[Partners](../../fundamentals/partners.md)" — how external consumers get access to published products.
* "[Flat CSV (One CSV file)](flat-csv-one-csv-file.md)" — the simplest of the four formats.
