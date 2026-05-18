---
description: The data dictionary documents every table and field in a form's repository — types, descriptions, ontology codes, and which fields are sensitive.
---

# The data dictionary

A form's **data dictionary** is the catalogue of every table and every field in its [repository](../../fundamentals/repositories/). For each field FormShare records its name, its data type, how it was declared in the original XLSForm, whether it is a primary key, whether it is part of a lookup, and any descriptive metadata you want to add. The dictionary is what turns a database schema into something you can hand to an analyst — they can read the dictionary and understand what each column means without having the ODK form open in front of them.

The dictionary is also where you flag fields as **sensitive** so that they are masked or excluded from [public data products](../data-products/private-vs-public-products.md). See "[Marking fields as sensitive](marking-sensitive-fields.md)" for that workflow.

## When the dictionary becomes available

The dictionary is created automatically when you [create a repository](../../fundamentals/forms/#create-a-repository) for a form. There is no separate step. The dictionary picks up every variable in the XLSForm, every table that FormShare generated (main table, repeat tables, lookup tables, multi-select tables — see "[How does FormShare stores my data?](../../fundamentals/repositories/how-does-formshare-stores-my-data.md)"), and every relationship between them.

Forms in the [testing stage](../../fundamentals/forms/#the-testing-stage) have no dictionary — they have no repository yet. Create the repository before you start curating field-level metadata.

## How to open it

From the form details page, click **Data dictionary**. The dictionary opens on the **Tables** view.

> Add screenshot of the data dictionary tables view, filtered by type with a search box.

The tables view lists:

* **Data tables** — the main table, plus one table per ODK repeat.
* **Multi-select tables** — one per `select_multiple` variable.
* **Lookup tables** — one per `select_one` variable, holding the option codes and labels.

Use the type filter and the search box to narrow the list. Click any table to drill into its fields.

## Viewing and editing fields

Inside a table, the **Fields** view shows one card per column.

> Add screenshot of the data dictionary fields view showing the sensitive / not sensitive indicator on each card.

Each card displays:

* **Field name** — the column name in the database (and the variable name in the XLSForm).
* **Type** — text, integer, decimal, date, etc.
* **XML code** — the corresponding XForm reference, useful when you need to write XPath queries.
* **Size / decimal size** — for fields where it applies.
* **Sensitive indicator** — a coloured circle: red if the field is marked sensitive, green if not. See "[Marking fields as sensitive](marking-sensitive-fields.md)".
* **Role flags** — whether the field is the primary key, encrypted, unique, or part of a lookup relationship.

Click any field to open its **metadata** page. There you can edit:

* **Description** — a free-text description of what the field means and how it was collected. Use this to write the kind of note you would otherwise put in a codebook.
* **Ontology code** — an external code (an ICD code, a CAS number, a controlled-vocabulary identifier) that links the field to a recognised standard. Optional but useful when you publish data to external repositories.

All the other properties on the metadata page are **read-only** — they reflect what FormShare derived from the XLSForm and the repository schema. To change those, you would need to update the form and [merge a new version](../../fundamentals/repositories/merging-subversions-of-a-form.md).

## Who can access the dictionary

The data dictionary is restricted to **project owners, administrators, and editors**. Read-only collaborators ("members"), assistants, and partners do not see the dictionary at all — for them, attempting to open the URL returns Not Found.

This is intentional: the dictionary is where decisions about *what data is* and *what data is shareable* are recorded. Keeping it in the hands of editors and above keeps that decision-making auditable.

## When to fill in the dictionary

You don't have to fill in descriptions or ontology codes for a form to work — exports and products work fine without them. But the dictionary becomes valuable in proportion to how widely your data is shared:

* **Internal short-lived projects**: Often the form labels are enough. Skip the dictionary except to mark sensitive fields.
* **Multi-team or long-running projects**: Fill in descriptions for every non-obvious field. A year from now, when someone new joins the project, they will read the dictionary, not your head.
* **Publishing or sharing data externally**: Ontology codes and well-written descriptions are essentially required. They are what turn your dataset into something a stranger can use without contacting you.

## What's next

* "[Marking fields as sensitive](marking-sensitive-fields.md)" walks through the sensitivity workflow and the three protection types (exclude, recode, unlink).
* "[Private vs public products](../data-products/private-vs-public-products.md)" explains how sensitivity affects exports.
