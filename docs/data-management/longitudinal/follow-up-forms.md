---
description: Follow-up forms attach repeat-visit data to existing cases. They reference cases through a picker fed by the real-time CSV file.
---

# Follow-up forms

A **follow-up form** is any form in a [longitudinal project](README.md) other than the [case creator](case-creator-forms.md). Where the case creator form *registers* a new case, a follow-up form *attaches new data* to an existing case — a clinic visit, a quarterly livelihood check, a follow-up vaccination record, a re-measurement of a plot of land.

Follow-up forms are the workhorses of a longitudinal project. You can have as many of them as you need, each capturing a different kind of repeat data, and all of them share the same case list maintained by the [real-time CSV file](real-time-csv.md).

## Uploading a follow-up form

Once a project has its case creator form, any new form you upload is treated as a follow-up. From the project details page, click **Add new form**. The upload page appears with the follow-up-specific fields visible.

> Add screenshot of the follow-up upload form showing the type dropdown, case selector field, and case date field.

You need to provide:

* **Survey file (XLS / XLSX)**: The ODK form in Excel format.
* **Follow-up type**: Pick what this submission does to the case it references. Three options:
  * **Simple follow-up** — adds data to the case without changing its status. The case remains active. This is the most common type.
  * **Follow-up and deactivate** — adds data and marks the case **inactive**. Use this for end-of-participation forms, withdrawal records, household-moved forms, plots that have been abandoned, etc. The case disappears from the [picker](real-time-csv.md) after submission.
  * **Follow-up and activate** — adds data and **reactivates** a previously inactive case. Use this when a case needs to come back into the panel — a household that returns, a patient resuming treatment, etc.
* **Case selector variable**: The field in your ODK form that holds the reference to the case being visited. This is the variable an enumerator fills in by picking from the case-picker (or by scanning a barcode). See "Building the case selector field" below for how to construct it in ODK.
* **Case date variable**: The field in your ODK form that records when *this follow-up* happened (the visit date, the measurement date). Different from the case's original registration date.
* **Variable used to control duplicate submissions**: As with any FormShare form, you pick a variable that is unique across submissions of *this* form. For follow-ups this is usually a **calculated combination** of the case ID and the visit date — see "Primary key for follow-ups" below.

Click **Add form**. FormShare validates the form and uploads it in the [testing stage](../../fundamentals/forms/#the-testing-stage), like any other form.

## Building the case selector field

The case selector is the field that ties this submission to a case. In your XLSForm, the case selector variable must be either:

* A **`select_one_from_file`** that points at the case CSV — typically named `list_caseselector.csv`. ODK Collect will render this as a searchable list of cases populated from the [real-time CSV file](real-time-csv.md).
* A **`barcode`** field, if your enumerators scan a barcode from a printed sticker or a registration card to identify the case.

Either way, the value the enumerator enters must be a case ID that exists in the project — the same case ID as the case creator form recorded when the case was first registered.

{% hint style="info" %}
**Pick before you collect**. Designing the case selector as `select_one_from_file` gives enumerators a searchable, filterable list of every active case — they cannot accidentally type a non-existent ID. Designing it as `barcode` requires no list lookup but does require printed barcodes in the field. Pick the one that fits how your team operates.
{% endhint %}

## Primary key for follow-ups

A follow-up form's primary key — the variable FormShare uses to control duplicate submissions of *that form* — is usually not the same as the case ID. If it were, you could only submit one follow-up record per case ever.

The standard pattern is to use a **calculated field that combines the case ID with the visit date** (or the visit number, or some other discriminator). In ODK, add a `calculate` field whose value is something like `concat(${case_id}, '-', ${visit_date})`, then pick that calculated field as the form's primary key on the FormShare upload page.

This way:

* Each (case, visit) pair is unique — you can record many visits per case without colliding.
* If an enumerator submits the same (case, visit) twice by accident, the second submission lands in the [error log](../cleaning/submissions-with-errors.md) as a duplicate primary key, which is exactly what you want.

## What happens when a follow-up is submitted

When a follow-up submission arrives, FormShare:

1. Reads the case ID from the case selector field.
2. Looks the case up in the case list.
3. If the case **does not exist** (typo, deleted case, future case not yet registered), the submission lands in the [error log](../cleaning/submissions-with-errors.md). An assistant can then correct the case ID or disregard the submission.
4. If the case **is inactive** and the form is a simple follow-up, the submission also lands in the error log — inactive cases cannot accept new data unless the submitting form has the *activate* effect.
5. If the case exists and is reachable, the follow-up data enters the database, linked to the case by the case ID.
6. If the form is a *deactivate* type, the case is marked inactive. The next regeneration of the real-time CSV will drop it.
7. If the form is an *activate* type, the case is marked active. The next regeneration of the CSV will include it.

## Common mistakes

* **Forgetting to declare the case CSV in the XLSForm**. The `select_one_from_file` in your form must reference the same CSV name FormShare exposes (typically `list_caseselector`). If the names disagree, ODK Collect can't render the picker.
* **Picking the case ID itself as the form's primary key**. This caps follow-ups at one per case forever. Use a calculated combination of case ID and visit date instead.
* **Using a free-text case selector**. Without the `select_one_from_file` (or barcode) constraint, enumerators will inevitably mistype case IDs and submissions will land in the error log. Constrain at the device.
* **Not realizing the case must be active**. Simple follow-ups against inactive cases are rejected. If you need to add data to an inactive case, first design a follow-up form with the activate effect.

## What's next

* Read about the [real-time CSV file](real-time-csv.md) to understand how the case list ends up on the device.
* Read about [activating / de-activating a case](activate-deactivate-case.md) to understand the lifecycle.
