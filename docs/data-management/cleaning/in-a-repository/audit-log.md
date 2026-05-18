---
description: The form-level audit log records every change ever made to repository data, with the assistant, timestamp, and before/after values.
---

# The audit log

Every change to data in a repository — whether through the [web interface](web-interface.md), [the API](api.md), or as a side effect of moving submissions in or out of the database — is recorded automatically in the form's **audit log**. The audit log is the single source of truth for "who changed what, when, and to what value", and is the foundation of FormShare's data-quality story.

## What gets logged

For each modified value, the audit log stores:

* **Date and time** of the change, expressed in the time zone of the viewer.
* **Assistant** who made the change. Even when the change comes through the API, it is attributed to the assistant whose API key was used.
* **Table and column** affected — for example, `maintable.household_income` or `rpt_members.age`.
* **Previous value**, before the change.
* **New value**, after the change.
* **Submission ID** (`rowuuid`) — the row that was modified. This lets you trace all the changes that affected one submission across all of its tables (main table, repeats, multi-select tables).
* **Action** — a short label describing the operation (typically *update*, but moves between database and error log are also logged).

Both web-interface edits and API edits write to the same log, in the same format. There is no way to tell, after the fact, which surface a change came through — and that is intentional: the audit log is about *what changed*, not *how the request reached the server*.

## Where to find it

From the form details page, click **Audit log**. The audit-log page opens with a grid of all changes ever made to the form's data.

> Add screenshot of the audit-log grid showing several recent changes with all columns visible.

## Reading the audit log

The audit log is presented as a sortable, searchable grid:

* **Sort** by any column — most often by date (newest first), or by assistant, or by table.
* **Search / filter** by any column with the search controls. Supports `contains`, `equals`, `starts with`, `greater than`, and so on.
* **Page** through results with the controls at the bottom.

Common things to look for:

* All changes by a specific assistant: filter on the **assistant** column.
* All changes to a specific submission: filter on the **submission ID** column. This is the audit equivalent of "show me the history of household 12345".
* All changes to a specific variable: filter on the **column** column. Useful when you want to find every time a particular field was edited.
* All recent activity: sort by date, descending.

## Who can view the audit log

Project owners and collaborators (any access level up to and including read-only) can view the full audit log. Assistants cannot — they see only their own activity through the [Assistant Access](../../../fundamentals/tasks/) portal.

## Limits and caveats

* The audit log is **read-only** through the FormShare UI. You cannot edit, delete, or hide entries.
* The audit log records changes to *data*, not to form structure. Adding a new variable through a [merge of a new form version](../../../fundamentals/repositories/merging-subversions-of-a-form.md) is recorded elsewhere (in the merge history of the repository) and does not appear here.
* The audit log records changes to *individual values*. Bulk operations — for example, [deleting all submissions](../working-with-submissions.md#delete-all-submissions) — generate one entry per affected row, which can be a lot of entries.
* The audit log lives inside the form's MySQL repository. If you take a copy of the repository for offline analysis (custom export, direct MySQL backup), the audit log is included.

{% hint style="info" %}
**Reverting a change**: There is no one-click revert button. To undo an edit, look up the previous value in the audit log and re-apply it through the web interface or the API. The undo itself will create a new audit entry, preserving the full history of the column.
{% endhint %}
