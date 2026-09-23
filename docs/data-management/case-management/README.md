---
description: >-
  Register a subject once, then come back to it with follow-up forms. Case
  management lets one form's data feed the choice lists of another, and lets the
  database enforce that a follow-up refers to a case that really exists.
---

# Case management (longitudinal workflows)

A longitudinal study has two kinds of forms:

* **A form that creates cases.** One submission registers one thing: a school, with the teachers who work there. Each submission is a *case*, and so is each teacher inside the repeat.
* **Forms that follow cases up.** An interview form starts by picking a teacher from a list, then asks its questions about that teacher.

FormShare keeps every form's data in a [repository](../../fundamentals/repositories/README.md) of its own. Case management is the set of features that let one form's repository feed the lists another form selects from, live, with the database checking that a follow-up really does refer to a case that exists.

## What the feature gives you

| Feature | What it gives you |
| --- | --- |
| [Published lists](published-lists.md) | A CSV file generated from a table of a form's repository, the main table or a repeat, and served to devices as an attachment of any form that names it. It refreshes as data arrives. |
| [Links and the case link](case-links.md) | A follow-up form that selects from a published list is *linked* to it. One of its lists is the **case link**: the list whose rows the form is about. When the follow-up's repository is built, that link becomes a database relationship. |
| [Properties](properties.md) | Extra typed columns FormShare keeps beside a table that publishes a list, without changing the ODK form. A teacher's `status`, a patient's `risk_level`. They travel in the lists like any other column. |
| [Workflow diagram](workflow-diagram.md) | A drawing of the forms, the lists between them and the links the databases enforce, generated from what you built. |

Nothing changes in how you write ODK. The form author writes `select_one_from_file school_list.csv` and that is all: no special sheet, no entity declaration, nothing to learn beyond the file names FormShare shows you. The same forms work in ODK Collect, in [Enketo](../../additional-functionality/enketo.md) web forms and in FormShare Collect.

## Words used in this guide

| Word | Meaning |
| --- | --- |
| **repository** | The database FormShare builds for a form when you click *Create repository*. One table for the form (`maintable`) plus one table per repeat. |
| **case** | A row of a table that a published list serves. A school (a row of `maintable`) or a teacher (a row of the repeat table `teachers`). |
| **rowuuid** | The permanent, unique identity of a row, present in every table. Lists and links use it rather than the variable that controls duplicates. See "[How cases are identified](how-cases-are-identified.md)". |
| **parent\_rowuuid** | In a repeat table, the `rowuuid` of the row the entry belongs to. |
| **source table** | The table a published list is generated from. |
| **published list** | The CSV FormShare generates from a source table and serves to devices. |
| **consumer** | A form that has a `select_one_from_file` naming a published list's file. |
| **case link** | The one list a consumer follows up. It becomes a foreign key and a check when the consumer's repository is built. |
| **value list** | A list of the distinct values of one column (the districts found in the table of schools) rather than of the rows. Used for cascading selects. It can never be the case link. |
| **active case** | Every case has an `_active` flag, 1 by default. A list serves either the active cases or the inactive ones. |
| **property** | A typed column FormShare keeps in a side table (`<table>_properties`), one value per case, born with a default. |
| **edition** | The number of times a list has been regenerated. |

## Before you start

* **Create a project.** A project holds the forms, and more than one form is what makes a longitudinal workflow.
* **Be an owner, administrator or editor of the project.** Members can use the forms but cannot publish lists or set links.
* **Give the case-creating form a repository first.** Lists are generated from a repository, so the form that creates cases must have been uploaded, tested and had its [repository created](../../fundamentals/forms/#create-a-repository) before anything can be published from it. The **Published lists** button appears on the project page once the project has a form with a repository.
* **Know when devices refresh.** A device picks up a list when it checks for form updates: *Get Blank Form* or the automatic update in ODK Collect, loading the form in Enketo. A follow-up form always carries the last list the device fetched.

## How to read this guide

Work through the walkthrough first. It builds a complete workflow, and the reference pages make more sense once you have seen the screens in order.

| Page | What it covers |
| --- | --- |
| [How cases are identified](how-cases-are-identified.md) | `rowuuid`, and why links are built on it instead of on the code your enumerators type. Read this before designing your forms. |
| [Walkthrough: schools and teachers](walkthrough.md) | A complete workflow in ten steps, from the registration form to the interview's repository. The two workbooks and the three placeholder files are there to download. |
| [Published lists](published-lists.md) | Publishing a list, choosing its columns, value lists and cascades, and how lists reach the devices. |
| [Links and the case link](case-links.md) | Telling FormShare which list a form follows up, and what the database enforces once the repository is built. |
| [Properties](properties.md) | Adding typed columns to a case without touching the ODK form. |
| [Workflow diagram](workflow-diagram.md) | Reading the drawing of what you built. |
| [Rules and safeguards](rules-and-safeguards.md) | What FormShare refuses to do, and what happens to a follow-up whose case is missing or inactive. |
| [Quick reference](quick-reference.md) | The screens and the expressions on one page, once you know the feature. |

{% hint style="info" %}
**Not available yet.** Three pieces of the picture are still being built, and this guide does not describe them: **actions** (a rule on a follow-up form that sets a property or deactivates the case when a submission arrives), **offline workflows in FormShare Collect** (a device that registers cases and follows them up without connectivity), **per-assistant lists** and **GeoJSON lists** for map pickers. [Properties](properties.md) exist today so that the columns actions will write are already in your lists, and forms you design now will not have to change when the rest arrives.
{% endhint %}
