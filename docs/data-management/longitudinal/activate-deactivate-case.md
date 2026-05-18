---
description: Active vs. inactive cases — what the status means, how it changes, and what happens to data when a case becomes inactive.
---

# Activating / de-activating a case

In a [longitudinal project](README.md), every case has a status: **active** or **inactive**. The status decides whether the case appears in the [real-time CSV file](real-time-csv.md) that ODK Collect downloads, and therefore whether enumerators in the field can record more follow-up data against it.

Cases are not free to be edited directly — their status changes are driven by [follow-up forms](follow-up-forms.md), so every status change has an associated submission, an associated enumerator, and an associated date. The trail of why a case was deactivated is always traceable.

## What "inactive" means

An **inactive** case:

* Is **excluded** from the [real-time CSV file](real-time-csv.md) on the next regeneration. ODK Collect's case picker will not show it.
* Cannot receive new **simple follow-up** submissions. A simple follow-up that references an inactive case lands in the [error log](../cleaning/submissions-with-errors.md) instead of the database.
* Still exists in the database. All of its previously submitted data is intact and continues to appear in exports.
* Is included in the audit trail like any other case.

In other words, "inactive" means *the case is closed for new data collection but its history is preserved forever*. Inactive is not delete.

An **active** case is everything inactive is not: it appears in the picker, accepts simple follow-ups, and is counted in the project's live targets.

## How cases get deactivated

A case is deactivated when a submission to a **follow-up form with the deactivate effect** ([form type "Follow-up and deactivate"](follow-up-forms.md#uploading-a-follow-up-form)) lands successfully in the database.

The typical pattern is to design one follow-up form whose role is to close cases — a "withdrawal form", an "end of enrollment form", a "household moved" form. The form captures whatever closure information you need (reason, date, who reported it) and, by virtue of being marked *deactivate*, also flips the case to inactive.

Some common reasons to deactivate:

* A household has moved out of the study area.
* A patient withdrew consent or completed the protocol.
* A farmer's plot has been sold or abandoned.
* The case is found to be a duplicate or otherwise invalid (though usually a [disregard](../cleaning/submissions-with-errors.md) is the cleaner option here).

## How cases get reactivated

A case is reactivated when a submission to a **follow-up form with the activate effect** ([form type "Follow-up and activate"](follow-up-forms.md#uploading-a-follow-up-form)) lands successfully.

This is rarer than deactivation but it does come up:

* A household that moved away has returned and resumed participation.
* A patient resuming care after a break.
* An accidental deactivation that needs to be undone.

Reactivation also preserves all the history — the data collected before deactivation plus the data collected after will all sit together, linked by the case ID.

## Manual status changes

There is no direct "make this case inactive" button in the FormShare UI. All status changes go through a submission to a follow-up form with the appropriate effect. This is intentional: it forces every status change to be:

* **Attributable** to a specific enumerator or import.
* **Dated** with a specific timestamp.
* **Explained**, because the deactivate / activate form can capture whatever metadata you require.

If you find yourself wanting a "manual" status change without a corresponding submission, ask first whether you really mean "delete this case" instead — for that, see "[Working with submissions](../cleaning/working-with-submissions.md)".

## What survives a deactivation

Even after a case is deactivated:

* All previously submitted follow-up data remains in the repository.
* Exports continue to include the case (every download type — CSV, JSON, KML — includes inactive cases by default; flat exports often add an `active` flag column).
* The audit log entries for the case are preserved.
* The disregard / restore mechanics for individual submissions are unchanged.

The only thing that changes is the case's presence in the live picker, and its ability to accept simple follow-ups.

## What's next

* "[Follow-up forms](follow-up-forms.md)" covers how to mark a follow-up form as deactivating or activating.
* "[The real-time CSV file](real-time-csv.md)" explains how status changes propagate to the field.
* "[Working with submissions](../cleaning/working-with-submissions.md)" covers the harder kind of removal: deleting or disregarding the submission that registered the case in the first place.
