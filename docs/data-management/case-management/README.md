---
description: >-
  Register a subject once, then come back to it with follow-up forms. Case
  management lets one form's data feed the choice lists of another, lets the
  database enforce that a follow-up refers to a case that really exists, and
  lets each follow-up change the case it is about.
---

# Case management (longitudinal workflows)

A longitudinal study has two kinds of forms:

* A form that creates cases. One submission registers one thing: a school, with the teachers who work there. Each submission is a *case*, and so is each teacher inside the repeat.
* Forms that follow cases up. An interview form starts by picking a teacher from a list, then asks its questions about that teacher.

FormShare keeps every form's data in a [repository](../../fundamentals/repositories/README.md) of its own. Case management is the set of features that let one form's repository feed the lists another form selects from, live, with the database checking that a follow-up really does refer to a case that exists, and with actions that change a case when one of its follow-ups arrives.

## What the feature gives you

| Feature | What it gives you |
| --- | --- |
| [Published lists](published-lists.md) | A CSV file generated from a table of a form's repository, the main table or a repeat, and served to devices as an attachment of any form that names it. It refreshes as data arrives. |
| [GeoJSON lists](geojson-lists.md) | The same cases as points, lines or shapes, for a follow-up that picks its case on a map. |
| [Links and the case link](case-links.md) | A follow-up form that selects from a published list is *linked* to it. One of its lists is the **case link**: the list whose rows the form is about. When the follow-up's repository is built, that link becomes a database relationship. |
| [Properties](properties.md) | Extra typed columns FormShare keeps beside a table that publishes a list, without changing the ODK form. A teacher's `status`, a patient's `risk_level`. They travel in the lists like any other column. |
| [Form actions](case-actions.md) | Rules, or code, that change the case each time a follow-up is submitted: set a property or a column, deactivate or reactivate the case. |
| [List filters](list-filters.md) | Conditions that narrow the cases a list serves, the same for every device. |
| [Who receives which cases](who-receives-which-cases.md) | Every case to every assistant, or each assistant only their own: the cases they registered, the cases assigned to them, or the cases of the filters given to them. |
| [Exporting and importing lists](exporting-and-importing-lists.md) | A project's lists, with their columns, filters and properties, written to a JSON or YAML file and read into another project. |
| [Offline in FormShare Collect](offline-in-formshare-collect.md) | Cases registered and followed up with no connection, with the actions run on the device. |
| [Workflow diagram](workflow-diagram.md) | A drawing of the forms, the lists between them and the links the databases enforce, generated from what you built. |

Nothing changes in how you write ODK. The form author writes `select_one_from_file school_list.csv` and that is all: no special sheet, no entity declaration, nothing to learn beyond the file names FormShare shows you. The same forms work in ODK Collect, in [Enketo](../../additional-functionality/enketo.md) web forms and in FormShare Collect. A form written for ODK Central's entities uploads too: "[Forms written for ODK Central](forms-written-for-odk-central.md)" says what to set up in its place.

## Words used in this guide

| Word | Meaning |
| --- | --- |
| **repository** | The database FormShare builds for a form when you click *Create repository*. One table for the form (`maintable`) plus one table per repeat. |
| **case** | A row of a table that a published list serves. A school (a row of `maintable`) or a teacher (a row of the repeat table `teachers`). |
| **rowuuid** | The permanent, unique identity of a row, present in every table. Lists and links use it rather than the variable that controls duplicates. See "[How cases are identified](how-cases-are-identified.md)". |
| **parent\_rowuuid** | In a repeat table, the `rowuuid` of the row the entry belongs to. |
| **source table** | The table a published list is generated from. |
| **published list** | The CSV or GeoJSON file FormShare generates from a source table and serves to devices. |
| **consumer** | A form that has a `select_one_from_file` naming a published list's file. |
| **case link** | The one list a consumer follows up. It becomes a foreign key and a check when the consumer's repository is built. |
| **value list** | A list of the distinct values of one column (the districts found in the table of schools) rather than of the rows. Used for cascading selects. It can never be the case link. |
| **active case** | Every case has an `_active` flag, 1 by default. A list serves either the active cases or the inactive ones, and an action can change the flag. |
| **property** | A typed column FormShare keeps in a side table (`<table>_properties`), one value per case, born with a default and changed by actions. |
| **action** | A rule, or a piece of code, on a form, that changes the case each time the form is submitted. |
| **run** | One pass of a form's actions over one submission, recorded on the form's actions page. |
| **filter** | Conditions that narrow the cases a list serves. A list has its own filter, and can give others to assistants and groups. |
| **edition** | The number of times a list has been regenerated. |

## Before you start

* Use an ordinary project. A project created with **Use a case/longitudinal workflow** follows the [earlier case model](../longitudinal/README.md) and has none of these pages.
* Be an owner, administrator or editor of the project. Members cannot open the pages of case management.
* Give the case-creating form a repository first. Lists are generated from a repository, so the form that creates cases must have been uploaded, tested and had its [repository created](../../fundamentals/forms/#create-a-repository) before anything can be published from it. The **Published lists** button appears on the project page once the project has a form with a repository.
* Know when devices refresh. A device picks up a list when it checks for form updates: *Get Blank Form* or the automatic update in ODK Collect, loading the form in Enketo. A follow-up form always carries the last list the device fetched.

{% hint style="info" %}
**For server administrators.** The pages of case management appear only when a plugin installed on the server declares the `workflows` feature.
{% endhint %}

## How to read this guide

Work through the walkthroughs first. The first builds a complete workflow, the other two add actions and per-assistant lists to it, and the reference pages make more sense once you have seen the screens in order.

| Page | What it covers |
| --- | --- |
| [How cases are identified](how-cases-are-identified.md) | `rowuuid`, and why links are built on it instead of on the code your enumerators type. Read this before designing your forms. |
| [Walkthrough: schools and teachers](walkthrough.md) | A complete workflow, built step by step, with the example workbooks to download. |
| [Walkthrough: update cases with actions](walkthrough-actions.md) | The interview marks its teacher interviewed, records their experience, and deactivates a teacher who has left. |
| [Walkthrough: filter lists and share cases out](walkthrough-sharing-cases.md) | A list that leaves some rows out, and each field officer receiving the schools of their own district. |
| [Published lists](published-lists.md) | The Published lists page, publishing a list, choosing its columns, value lists and cascades, and how lists reach the devices. |
| [GeoJSON lists](geojson-lists.md) | Lists of cases to pick on a map, and the cases left out for having no location. |
| [List filters](list-filters.md) | Narrowing the cases a list serves, and why texts are compared exactly as written. |
| [Who receives which cases](who-receives-which-cases.md) | Serving each assistant their own cases, the filters given to assistants and groups, and what a device keeps. |
| [Exporting and importing lists](exporting-and-importing-lists.md) | Moving lists between projects, what the file carries and what an import requires. |
| [Links and the case link](case-links.md) | Telling FormShare which list a form follows up, and what the database enforces once the repository is built. |
| [Properties](properties.md) | Adding typed columns to a case without touching the ODK form. |
| [Form actions](case-actions.md) | Rules and code that change a case when a follow-up is submitted, their test, their runs, and what is taken back when a submission is deleted. |
| [Offline in FormShare Collect](offline-in-formshare-collect.md) | What FormShare Collect does with cases without a connection. |
| [Forms written for ODK Central](forms-written-for-odk-central.md) | What happens to a form with an *entities* sheet, and what to set up instead. |
| [Workflow diagram](workflow-diagram.md) | Reading the drawing of what you built. |
| [Rules and safeguards](rules-and-safeguards.md) | What FormShare refuses to do, how the active flag changes, and what happens to a follow-up whose case is missing or inactive. |
| [Quick reference](quick-reference.md) | The screens and the expressions on one page, once you know the feature. |
