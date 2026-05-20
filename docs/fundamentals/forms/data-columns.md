---
description: Data columns are extra columns you add to your ODK XLSForm beyond the standard ones. FormShare imports them into the data dictionary or the lookup tables when you create the repository.
---

# Data columns

ODK XLSForms have a fixed set of standard columns — `name`, `type`, `label`, `hint`, `required`, `calculation`, and so on. FormShare lets you add **extra columns** to your XLSForm to carry information that isn't part of the standard schema: codebook descriptions, ontological codes, internal tags, anything you want to preserve alongside the variable. When the repository is created, FormShare imports those extra columns automatically — survey-sheet columns become field metadata in the [data dictionary](../../data-management/data-dictionary/), and choices-sheet columns become extra columns in the lookup tables.

This is how you ship a richly documented dataset without writing a separate codebook by hand.

## Where you see them

After you upload a form, the form details page lists which extra columns FormShare detected in your XLSForm. Click **Data columns** on the form details page to open the data columns modal.

> Add screenshot of the form details page showing the "Data columns" button.

The modal has three lists:

* **Data columns that can be imported into the dictionary** — extra columns found in the **survey** sheet. Each becomes a field-level metadata column in the data dictionary after the repository is created.
* **Data columns that can be imported into lookup tables** — extra columns found in the **choices** sheet. Each becomes an extra column in the lookup table corresponding to the choice list it appeared in.
* **Warning: data columns that cannot be imported because their names are invalid** — columns whose names break FormShare's naming rules (see [Restrictions](#restrictions) below). These columns are detected but skipped.

> Add screenshot of the Data columns modal with all three lists populated.

The modal is informational — it shows you what FormShare found. The import itself happens automatically when you [create the repository](README.md#create-a-repository); you do not need to take a separate action to trigger it.

## Special columns FormShare recognises

A handful of column names are reserved as **semantic columns** — FormShare reads them and applies their values directly, instead of treating them as generic metadata. They are not listed as "extra" because their meaning is built in:

* **`formshare_sensitive`** (survey sheet) — if the value for a row is truthy, the variable is marked [sensitive](../../data-management/data-dictionary/marking-sensitive-fields.md) at repository creation time. Saves you the step of opening the data dictionary and clicking each field.
* **`formshare_encrypted`** (survey sheet) — marks the variable as encrypted. Encrypted fields are implicitly sensitive and cannot be unmarked from the UI.
* **`formshare_unique`** (survey sheet) — marks the variable as carrying unique values across submissions (a secondary uniqueness constraint beyond the primary key).
* **`formshare_ontological_term`** (survey **and** choices sheets) — an ontology code (an ICD code, a CAS number, a controlled-vocabulary identifier) that links the variable or the option to a recognised external standard. Populates the **Ontology code** field in the data dictionary.

These four are processed first; only the remaining extra columns are eligible for the generic dictionary / lookup import.

## Columns that are ignored

FormShare ignores any column whose name is `note`, `notes`, `nota`, `notas`, `fa`, or `fa::testing` (case-insensitive). These are conventional names for in-form comments and translation notes that you do not want polluting the dictionary. If you genuinely need to carry a column called "notes" into the dictionary, rename it (for example, to `internal_notes`).

## Restrictions

For a column to be importable, its name must:

* Contain **only letters, digits, and underscores** (`_`). No spaces, dots, dashes, slashes, or other punctuation.
* Not be a reserved MySQL keyword. The list of restricted words is maintained as a [public reference spreadsheet](https://docs.google.com/spreadsheets/d/1PsDsbFZZXnlHRdmntWzX_DDSPCm7K-6knV3qICCxAk0/edit?usp=sharing).

Columns that violate these rules appear in the "invalid" list of the Data columns modal. The corresponding values stay in your XLSForm but never make it into the FormShare database.

{% hint style="info" %}
**Underscores, not spaces**: A column called `Country code` in your XLSForm will not be imported because of the space. Rename it to `country_code` in the spreadsheet and re-upload the form. If the form already has a repository, you'll need to [merge a new version](../repositories/merging-subversions-of-a-form.md).
{% endhint %}

## What happens to imported columns

### Survey-sheet columns

Each accepted survey-sheet column becomes a **field-level metadata column** in the data dictionary. After repository creation, open the [data dictionary](../../data-management/data-dictionary/), drill into the relevant table, and click any field — the extra column's value for that field appears as part of the field's metadata.

This is useful for:

* **Codebooks** — add a `description` column to your survey sheet, and every field gets its codebook entry imported automatically.
* **Ontology codes** — `formshare_ontological_term` is the special case, but you can add other coding columns (ISO codes, internal classifiers) and have them ride along.
* **Internal tags** — flag fields by topic, by team, by data-collection wave, etc.

### Choices-sheet columns

Each accepted choices-sheet column becomes an extra column in the **lookup table** created for the corresponding `select_one` or `select_multiple`. After repository creation, the lookup table (e.g. `lkp_province`) has the standard `<list>_cod` and `<list>_des` columns plus one extra column per imported choices column, populated with the values from your XLSForm.

This is useful for:

* **Multilingual labels beyond the form's languages** — labels in additional languages your enumerators don't see but your analysts will use.
* **External mappings** — link each option to a code in another classification system (e.g. ISO 3166-1 codes for countries, ICD-10 codes for diagnoses).
* **Sort order, weights, parent-child hierarchies** — anything you want to carry from the XLSForm into the lookup table.

## What about new versions?

When you [merge a new version](../repositories/merging-subversions-of-a-form.md) of a form, FormShare applies the same data-column rules to the new XLSForm. New extra columns are imported; columns that disappeared from the new XLSForm remain in the dictionary (FormShare never removes data).

The semantic columns (`formshare_sensitive`, `formshare_encrypted`, `formshare_ontological_term`) are preserved across merges as part of the merge process — they do not need to be re-applied on every version.

## Tips

* **Declare your extra columns in the XLSForm itself**, not after the fact. The dictionary's field-description editor is useful for tweaks, but for anything you want to ship across teams or environments, the XLSForm is the source of truth.
* **Use a consistent naming convention** across forms in a project. `desc_en`, `desc_es`, `desc_fr` is easier to query later than ad-hoc names like `description`, `descripcion`, `Beschreibung`.
* **Keep the spreadsheet tidy** — extra columns the team is not actually using should be removed. Once they're in the dictionary, they don't pull their weight; they just clutter the metadata view.
