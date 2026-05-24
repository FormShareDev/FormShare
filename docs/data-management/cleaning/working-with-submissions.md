---
description: Manage submissions at the form level — delete individual records, move records into the error log for re-cleaning, or wipe the repository entirely.
---

# Working with submissions

The **Manage submissions** screen is the project owner's view of every submission in a form's repository. From here you can delete individual records, send a record back to the [error log](submissions-with-errors.md) for re-cleaning by an assistant, or — when starting fresh is necessary — delete every submission at once.

This screen is meant for occasional administrative work, not for routine data cleaning. Value-level edits go through the [web interface](in-a-repository/web-interface.md) or [the API](in-a-repository/api.md); the error log is drained by assistants from their own portal. Manage submissions exists for the few operations that only the project owner should be able to perform.

## Access

From the form details page, click **Manage submissions**. The screen shows a grid of every submission currently in the database.

<!-- TODO screenshot: the Manage Submissions screen with the grid, the per-row buttons, and the "Danger zone" section visible. -->

The screen is available to the **project owner** and to **collaborators** with editor or administrator access. Read-only collaborators, assistants, and partners do not see it.

{% hint style="warning" %}
**The form must be inactive.** All delete operations on this screen require the form to be set to **inactive** so that no new submissions land mid-operation. If the form is currently accepting submissions, the buttons are disabled and FormShare prompts you to deactivate the form first. After you have finished cleaning, you can reactivate the form to resume data collection. To change a form's active state, see "[Forms — Edit the metadata of a form](../../fundamentals/forms/#edit-the-metadata-of-a-form)".
{% endhint %}

## Delete a single submission

Click the trash icon next to a submission. FormShare asks for confirmation, and on confirmation removes the submission from the repository.

Deleting is **permanent**: the rows are removed from the data table, from any repeat tables, and from any multi-select tables that referenced the submission. Media files attached to the submission remain on disk but are no longer referenced from the database. An entry is written to the [audit log](in-a-repository/audit-log.md) recording the deletion, so you always know which submissions were removed and when.

Use single delete when:

* A submission is obviously bogus (a test record from your own phone, a malformed entry submitted by mistake) and you do not need to preserve it for re-cleaning.
* A duplicate has already been resolved in the surviving record, and the duplicate has no value as a reference.

For records that might still need work, prefer **Move to logs** instead — it preserves the record and lets an assistant decide what to do.

## Move a submission to the error log

Next to each submission, there is a **Move to logs** button. Clicking it opens a small dialog:

* Pick the **assistant** to whom the moved submission should be assigned. That assistant then sees it in their error list with the **With error** status, ready for cleaning.

On confirmation, FormShare removes the submission from the repository and creates a corresponding entry in the [error log](submissions-with-errors.md). The full JSON of the submission is preserved — nothing is lost.

<!-- TODO screenshot: the "Move to logs" dialog with the assistant picker. -->

Use **Move to logs** when:

* A data-quality check (range check, internal consistency, comparison against another dataset) flagged a record after it had already entered the database.
* A respondent contacted you with a substantial correction that an assistant should apply.
* You want to redo a record's collection without losing its history.

Once moved, the record is back in the [checkout / revise / push workflow](submissions-with-errors.md#the-error-workflow). The assistant can fix it and push it back, or disregard it.

## Delete all submissions

The **Danger zone** at the bottom of the Manage Submissions screen contains the **Delete all submissions** button. Click it to remove **every submission** from the form's repository in one operation.

This is intended for situations like:

* The pilot phase is over and you want to start "real" data collection on a clean repository while keeping the form unchanged.
* Test data is mixed with production data, and the simplest path forward is to wipe the slate and re-collect.

FormShare requires extra confirmation: typically a typed acknowledgement and/or an email-based confirmation step, depending on how your FormShare instance is configured. The operation runs asynchronously — you'll see a progress task under the form's Tasks tab.

{% hint style="danger" %}
**Delete all is irreversible.** Every data row, every repeat row, every multi-select row, and every error-log entry for this form is removed. The audit log entries describing the deletions remain, but the underlying data is gone. There is no undo. If you might ever need this data again, [download a private product](../data-products/private-vs-public-products.md) first.
{% endhint %}

## What gets audited

* **Single delete** writes one audit entry per affected row across every table.
* **Move to logs** writes audit entries for the deletion plus a separate trail in the error log.
* **Delete all** writes a large number of audit entries — one per affected row — and they will still be there after the dataset is wiped.

The audit log itself is never deleted; it is the permanent record of what existed in the dataset, even after the data itself is gone.

## What this screen does *not* do

* It does not edit individual values. For that, an assistant uses the [web interface](in-a-repository/web-interface.md) or [the API](in-a-repository/api.md).
* It does not handle errored submissions. Those are managed by assistants via "[Submissions with errors](submissions-with-errors.md)".
* It does not produce exports. For exports, see "[Data products](../data-products/)".
