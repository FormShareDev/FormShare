---
description: The case creator form is the first form in a longitudinal project — it registers new subjects (households, farmers, patients) into the case list that follow-up forms will reuse.
---

# Case creator forms

The **case creator form** is the entry point into a [longitudinal project](README.md). It is the form that enumerators use to register a new subject — a household, a farmer, a patient, an animal — for the first time. Every other form in the project will attach data to subjects this form has created.

A longitudinal project has exactly **one** case creator form. The first form you upload into a [longitudinal-enabled project](README.md#turning-the-feature-on) automatically becomes the case creator; subsequent forms are [follow-ups](follow-up-forms.md).

## What makes a form a case creator

In a regular project, when you upload a form, FormShare asks you to pick the variable that will be used to control duplicate submissions — the form's primary key.

In a longitudinal project, the case creator form's upload page asks for **more**: it asks for the variables that define the long-lived identity of the case. These are different in purpose from a regular primary key and FormShare records them separately so that follow-up forms can use them.

## Uploading the case creator

From the project details page of a longitudinal project, click **Add new form** as you would for any other form. The "Upload new form" screen appears with the case-specific fields visible.

<!-- TODO screenshot: the case-creator upload form showing the case ID, case label, and case date fields. -->

You need to provide:

* **Survey file (XLS / XLSX)**: The ODK form in Excel format, as always.
* **Case ID variable**: The field in your ODK form that uniquely identifies each case. Pick a variable whose values will be **unique across every case you expect to register** — for example `farmer_id`, `household_code`, `patient_uuid`. This becomes the long-lived identifier that every follow-up form will reference. It **cannot be changed afterward**.
* **Case label variable**: A human-readable field that ODK Collect will show enumerators in their case-picker — for example `farmer_name`, `household_head`, `patient_name`. The case label exists to make the case list usable in the field: the case ID is what FormShare stores, but the case label is what the enumerator sees.
* **Case date variable**: A date or datetime field in your ODK form that records when the case was first registered. This is the timestamp that anchors the case's history. The field's type in the ODK form must be `date` or `datetime`.
* **Expected number of cases** (optional): If you have a target — for instance, 400 households for a baseline survey — type it here. FormShare shows progress toward this target on the form details page.

Click **Add form**. FormShare runs the same checks it would for any form (variable names compatible with MySQL, no reserved words, etc. — see "[Common errors in a form](../../fundamentals/forms/common-errors-in-a-form.md)"). When the form passes, FormShare uploads it in the [testing stage](../../fundamentals/forms/#the-testing-stage), exactly like any other form, so you can [test it on a device](../../guides/creating-your-first-task/test-your-form.md) before committing to a repository.

{% hint style="info" %}
**Case ID vs. submission primary key**: In a regular project, the primary key controls duplicates *within one form*. In a longitudinal project, the case ID controls cases *across all forms*. The case creator form's case ID is the value that follow-up forms will reference; it does double duty as the form's primary key and as the project's case identifier.
{% endhint %}

## Creating the repository

After testing, [create the repository](../../fundamentals/forms/#create-a-repository) for the case creator form the same way you would for any other form. At that point FormShare also creates the **case lookup table** — the bookkeeping that holds the live list of cases that ODK Collect will see.

From the form's details page, a new **Case lookup table** option appears. This is where you configure which fields will be visible to enumerators when they pick a case in a follow-up form. See "[The real-time CSV file](real-time-csv.md)" for what this configuration controls and how to set it.

## How cases are added

Cases are added the same way regular submissions are added: through ODK Collect, through Enketo, or through an [external import](../../fundamentals/submissions/import-external-data.md). Every accepted submission to the case creator form registers exactly one new case.

The newly registered case appears in the [real-time CSV file](real-time-csv.md) almost immediately — the next time ODK Collect downloads the manifest of any follow-up form, the new case is in the picker.

## Common mistakes

* **Choosing a non-unique case ID**. Once you set the case ID variable, you cannot change it. Make sure the variable is genuinely unique across every case you might ever register. A national ID is a good choice when available; an auto-generated UUID also works. A first name is not unique enough.
* **Choosing a calculated field that depends on other variables**. The case ID should be a stable value — typed by the enumerator, scanned from a barcode, or generated as a UUID. If you calculate it from other fields, changes to those fields will not propagate, and FormShare relies on the case ID being immutable.
* **Forgetting the case date**. A case without a date variable cannot anchor time-series analysis. Even if you don't think you need it now, capture *when the case was registered* — your analysts will thank you in a year.
* **Using a select for the case label**. The case label is shown to enumerators to help them recognize the case. A free-text variable (the farmer's name, the household head's name) usually works best.

## What's next

* Configure which fields appear to the enumerator in [The real-time CSV file](real-time-csv.md).
* Add [follow-up forms](follow-up-forms.md) that attach data to the cases this form creates.
