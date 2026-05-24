---
description: The in-browser spreadsheet-style editor for cleaning repository data, used by assistants from the Assistant Access portal.
---

# The web interface

The web cleaning interface is an in-browser table editor that lets an assistant pick a table from the repository, choose which fields to display, and edit values directly in a grid. It is the easiest way to make ad-hoc corrections.

## Access

The web interface is only available to **assistants** with the **Can clean** permission on the form. Collaborators and project owners do not have access to it — they manage submissions through "[Working with submissions](../working-with-submissions.md)" instead.

To open the interface:

1. The assistant signs in to the **Assistant Access** portal of FormShare. Each FormShare instance has its own assistant sign-in URL; ask the project owner if you do not know it.
2. From the assistant's dashboard, the assistant picks the form they want to clean.
3. The assistant clicks **Clean data**. The cleaning interface opens.

<!-- TODO screenshot: the Assistant Access portal showing the "Clean data" link on a form card. -->

## Pick a table and fields

A repository can have many tables: the main table, one table per repeat, lookup tables, and one multi-select table for each select-multiple variable (see "[How does FormShare stores my data?](../../../fundamentals/repositories/how-does-formshare-stores-my-data.md)"). Editing each of these is a separate context.

At the top of the cleaning interface:

* **Table dropdown**: Pick which table you want to edit (e.g. `maintable`, `rpt_members`, `lkp_province`).
* **Fields multi-select**: Pick which fields to display. The grid only shows the fields you select, plus the primary-key columns and the submission ID.

<!-- TODO screenshot: the table selector and field selector. -->

Click **Load** to refresh the grid with your selection. You can change the table or the visible fields at any time.

## Edit values in the grid

The data grid loads 10 rows at a time. You can:

* **Sort** by any column header.
* **Search / filter** with the search bar — supports the usual operators (equals, contains, starts with, greater than, etc.).
* **Page** forward and backward with the controls at the bottom.
* **Edit a cell** by double-clicking it. The cell becomes editable; type the new value and click outside (or press Tab) to save.

<!-- TODO screenshot: the grid with a cell in edit mode. -->

The change is sent to the server as soon as you leave the cell. If the value violates a database constraint (wrong type, missing reference, primary key duplication), the change is rejected and the cell reverts to its previous value; an error message explains why.

### Lookup fields

When a field is backed by a lookup table (a `select_one` in the ODK form), the cell becomes a **drop-down** instead of a plain text input. The drop-down only contains the options defined in the lookup table, so you cannot accidentally introduce values the rest of the system doesn't know about.

To change the lookup options themselves (add a new option, fix a label), edit the corresponding `lkp_*` table from the table dropdown.

### Multi-select fields

Multi-selects (`select_multiple` in the ODK form) cannot be edited inline because each row may contain several selected options. Click the field — a modal dialog opens with two lists:

* **Available options**: All options defined in the lookup, minus what is already selected.
* **Selected options**: What is currently checked for this submission.

<!-- TODO screenshot: the multi-select editor modal. -->

Move options between the lists with the arrow buttons. Click **Save** to apply the changes. FormShare replaces all junction-table rows for that submission and records the change in the audit log.

## What the audit log captures

Every cell save and every multi-select change writes one entry per modified column to the audit log: the assistant who made the change, the timestamp, the table, the column, the previous value, and the new value. The submission ID (`rowuuid`) identifies which row was affected.

The audit log applies in the same way to changes made through the [API](api.md) — both surfaces feed into the same log, viewable from "[The audit log](audit-log.md)".

## Limits

* The web interface does **not** delete submissions. To remove a submission, the project owner uses "[Working with submissions](../working-with-submissions.md)".
* You cannot edit the primary key or the submission ID. To re-key a submission, delete and re-collect.
* The grid loads rows in pages — there is no "bulk select and edit" affordance. For changes that span many rows, use [API data cleaning](api.md) with a script.
