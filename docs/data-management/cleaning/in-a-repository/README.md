---
description: How to edit values inside submissions that have already entered the repository.
---

# Cleaning data in a repository

Submissions that have entered the repository can be edited. The most common reasons:

* An enumerator typed a wrong value and you want to correct it.
* A code or unit needs to be normalized across many records.
* Data quality checks (range checks, outlier detection, internal consistency) found a record to fix.
* A respondent contacted you with a correction after the fact.

FormShare offers two ways to make these edits:

* "[The web interface](web-interface.md)" — an in-browser spreadsheet-style editor used by assistants directly inside FormShare.
* "[API data cleaning](api.md)" — a programmatic interface for tools like R, Stata, Python, or any other language that can make HTTP requests. Use this when the corrections are large in volume or driven by a script.

Both interfaces produce the same end result and use the same audit infrastructure underneath. Pick whichever fits the situation: ad-hoc fixes go through the web, scripted bulk updates go through the API.

## What can and cannot be edited

You can edit:

* Regular field values in any data table — text fields, numbers, dates, single-select codes, GPS coordinates, etc.
* Multi-select fields — by adjusting which options are selected for a given row. The web interface presents this as a two-list picker; the API requires deleting/re-inserting selections at a lower level.
* Lookup values for fields backed by lookup tables — through a drop-down constrained to the lookup's allowed options.

You cannot edit:

* The **primary key** of a submission (the variable you chose at form upload to control duplicates). To change a primary key, you have to delete the submission and re-collect it.
* The **submission ID** (`rowuuid`) — this is FormShare's internal identifier and is immutable.
* The structure of the form (variable names, types, repeats). Those are set when the repository is created and changed only by [merging a new form version](../../../fundamentals/repositories/merging-subversions-of-a-form.md).

## Who can clean

Only [assistants](../../../fundamentals/tasks/) flagged with the **Can clean** permission on a form can edit data through either interface. The flag is set per assistant per form, so you can have data collectors who cannot edit and data cleaning specialists who cannot collect — or one assistant who does both.

Project owners and collaborators cannot edit individual values directly. This is intentional: it forces cleaning work to be done by an identifiable assistant and tracked in the audit log.

## The audit log is always on

Every cell change, every multi-select toggle, every value an assistant overwrites is captured in the form's audit log along with the assistant who made the change, the timestamp, the table and column affected, the previous value, and the new value. The log is read-only and applies equally to web and API edits — see "[The audit log](audit-log.md)".

{% hint style="info" %}
**When in doubt, edit through the web interface.** The API is powerful but unforgiving — a typo in a script can quickly affect thousands of rows. For most ad-hoc work, the web interface is faster, safer, and the audit log will look exactly the same.
{% endhint %}
