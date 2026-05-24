---
description: How FormShare handles submissions that the repository rejected, and the checkout / revise / push workflow assistants use to drain the error log.
---

# Submissions with errors

When a submission arrives at FormShare but cannot enter the repository — for example, because its primary key duplicates an existing submission, because a required field is missing, or because an option doesn't match the lookup table — it does not disappear. Instead, FormShare puts it into the **error log** so that a human can inspect it, fix it, and push it back to the database.

The error log is what makes FormShare safe for production: no submission is ever silently dropped, and every problematic record has a chance to be recovered.

## How submissions land in the error log

A submission enters the error log when any of these checks fail at the moment of submission:

* **Duplicate primary key** — another submission with the same primary-key value already exists in the database.
* **Missing required field** — a value the form marks as required is absent.
* **Unknown lookup option** — the value of a `select_one` field does not match any code in the corresponding lookup table.
* **Type mismatch** — a numeric field contains non-numeric data, a date field contains an invalid date, etc.
* **Foreign-key violation** — a value referencing another table (e.g. in a case workflow) points at a record that does not exist.

The same checks apply to live submissions from ODK Collect and to records arriving through [imports](../../fundamentals/submissions/import-external-data.md): if the data doesn't fit, the record sits in the error log instead of being dropped.

A form's details page shows the count of errored submissions in a "With error" badge. Clicking it takes you to the error list.

## The error workflow

Errored submissions are owned by assistants, not by project owners. Each errored record goes through up to four states:

* **With error** — the initial state. The submission failed validation and is waiting for a human.
* **Checked out** — an assistant has claimed the submission and is working on it. While checked out, no other assistant can edit the same record.
* **Checked in** — the assistant has uploaded a revised version. It now awaits a final push attempt against the database.
* **Fixed** — the revised submission passed validation and entered the database. At this point it disappears from the error log and reappears in the main submission grid.

A fifth state is possible at any point:

* **Disregarded** — the assistant has explicitly marked the submission as something to keep in the audit trail but not in the dataset. Disregarded submissions are preserved in full (you can always look back at them) but do not count toward the dataset's submission total.

<!-- TODO screenshot: the error list showing the status badges (with error, checked out, checked in, fixed, disregarded). -->

## What an assistant does

The error list, accessible from the assistant's view of the form, shows every record currently in the error log. The list can be filtered by state.

### Push directly

For some errors, the simplest path is to re-attempt the push without any changes. This is useful when the error was caused by transient state — for example, a foreign key that was missing at submission time but has since been added. From a "With error" record, click **Push**. If validation now succeeds, the submission enters the database and is marked **Fixed**.

### Checkout, revise, push

For records that need editing:

1. **Checkout** the submission. Its status changes to **Checked out**. From this point until check-in, only the assistant who checked it out can act on it.
2. **Download** the raw JSON of the submission, or **compare** it side-by-side with another version (useful when fixing a duplicate primary key — you can see exactly which fields differ between the two records).
3. Edit the JSON locally to correct the values.
4. **Checkin** by uploading the revised JSON. The status changes to **Checked in**.
5. **Push** to the database. FormShare runs the same validation rules as for a live submission; if the rules pass, the record enters the database and is marked **Fixed**. If they fail, the record stays at **Checked in** with the new error explained, ready for another round of revisions.

At any point during the revise step, the assistant can **Cancel** the revision and return the submission to **With error**, discarding the partial work.

### Disregard

When a submission is clearly invalid and should not be added to the dataset — a test record, a duplicate that should not be merged, a sample that did not pass field protocol — the assistant clicks **Disregard**. A notes field is mandatory: the assistant must explain why the record is being set aside. The submission moves to **Disregarded** and stops counting in the dataset, but the full JSON and the notes are kept indefinitely.

Disregard is also reversible — an assistant can **Cancel disregard** to return the record to **With error** if a decision needs to be revisited.

## What the project owner sees

The project owner does not work the error log directly — that's the assistant's job. But the owner can:

* Watch the "With error" count on the form to know when the queue is growing.
* Move existing submissions *into* the error log to send them back for cleaning. See "[Working with submissions](working-with-submissions.md)" for how this is done and when it's useful.
* Review the [audit log](in-a-repository/audit-log.md) to see when each errored submission was pushed or disregarded and by whom.

## History is preserved

Every checkout, check-in, push, disregard, and cancel writes an entry to a per-submission history table. You can always see exactly what happened to a record from the moment it landed in the error log to the moment it was resolved — who touched it, when, and (for disregards) why.

{% hint style="info" %}
**Where to focus**: A healthy form has a near-empty error log. If yours grows steadily, that often points to a form-design issue (a required field that enumerators struggle to fill, a lookup table missing common codes) rather than to careless data collection. Investigate the *pattern* of errors before you start cleaning one by one.
{% endhint %}
