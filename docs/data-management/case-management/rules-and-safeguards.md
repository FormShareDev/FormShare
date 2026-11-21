---
description: >-
  What FormShare refuses to do once lists, case links and actions are in use,
  how active and inactive cases change, and where a follow-up goes when its
  case is missing.
---

# Rules and safeguards

Once a form's repository has been built against a list, the shape of that list is part of a database. FormShare protects it, which means some things you may want to do are refused. This page collects those rules in one place so you meet them here rather than in the field.

## What FormShare refuses

| What you try | What happens |
| --- | --- |
| Delete a list that a built form uses | Refused. The **Delete** button is not offered while a form with a repository attaches the list. |
| Delete a list that only testing forms use | Allowed, after a warning. Those forms stop receiving fresh data and keep the file they already had. |
| Change a list's key or its rows to serve while a built form uses it | Refused. The form's database was shaped by them. |
| Delete a form whose cases another form uses | Refused, naming those forms: *These forms use the cases this form creates: …. Delete them first.* |
| Delete a form that feeds published lists | Refused, with the message *This form feeds the published lists …. Delete the lists first.* |
| Delete a case that has follow-ups | Refused by the database, because the follow-ups refer to it. Remove them first. |
| Change the case link after the repository is built | Not possible, and the page says so. A new version keeps the link of the version it replaces. |
| Merge a new version that picks its cases from another list | Refused at the [merge check](../../fundamentals/repositories/merging-subversions-of-a-form.md), naming the list the version must keep. |
| A follow-up arrives for a case that is inactive or missing | The submission is refused by the database check and goes to the form's [error log](../cleaning/submissions-with-errors.md). |
| Put the case selector inside a repeat | The list is still served to the form, but no link is detected and nothing is enforced. Keep the question that picks the case outside every repeat. |
| Delete a property that a list serves, or that a list's filter reads | Refused, naming what uses it. Remove it from the list, or change the filter, first. |
| Save an action FormShare cannot turn into code, or one that takes the form's actions past 64 KB together | Refused with the reason, and nothing is saved. |
| An action fails on a submission | The submission stays stored. None of the form's actions is applied to it, and the run is marked *failed* on the [Form actions](case-actions.md) page, with **Retry**. |
| Delete a submission whose actions changed a case, or move it to the error log | What the actions wrote goes back to what it was, unless it has changed since. The run is marked *taken back*. |
| A second submission with the same primary key arrives, such as an edited copy of one already sent | It goes to the error log as a duplicate, and its actions do not run. |
| Serve each assistant their own cases in a crowdsourcing project | Not offered. A project cannot become crowdsourcing while one of its lists serves each assistant their own cases. |
| Use an encrypted column as a list's label or key, in a filter, or in an action | Refused. An encrypted column can still be served in a list, sealed. |
| Import a file of lists with a single problem | Nothing is imported, and the page lists every reason. |

## Active and inactive cases

Every case has an `_active` flag, set to 1 when the row is stored. A list serves one side of that flag, chosen under **Rows to serve** when you [publish it](published-lists.md):

* A list of active cases is the usual one. Its check requires the case to exist and be active.
* A list of inactive cases serves the cases that were retired, for a form whose job is to bring them back. Its check requires the case to be inactive.

The flag changes in three ways:

* An [action](case-actions.md) of a follow-up form deactivates or reactivates its case, or the case's parent, when the submission arrives. An interview that answers *No* to "still working" deactivates the teacher.
* Deleting that submission, or moving it to the error log, takes the change back, unless the flag has changed again since.
* Someone changes it by hand in the [data editor](../cleaning/in-a-repository/web-interface.md) of the source form, where the column is editable.

A case that changes side leaves one list and joins the other the next time each is generated. A device keeps the copy it downloaded until its next update check.

## When a submission is refused

A follow-up whose case is unknown or inactive does not reach the repository. It is refused with the message *Case ID … is inactive or does not exist* and lands in the form's error log, under **Manage errors** on the form page. Nothing is lost: the submission sits there with its data, and you decide what to do with it.

The usual causes are worth knowing before you go looking:

* The registration that created the case was deleted.
* The case was deactivated, by a person or by an action of another follow-up, between the moment the device fetched the list and the moment the submission arrived.
* The device is carrying an old copy of the list and the case never existed on this server.
* The follow-up reached the server before the registration of its case, which was collected offline and not sent yet.

"[Submissions with errors](../cleaning/submissions-with-errors.md)" covers what an assistant can do from there: fix the value, push it through, or disregard the submission.

## A case a list stops serving is not refused

A list's [filter](list-filters.md) and the choice of [who receives which cases](who-receives-which-cases.md) decide what a list offers. They do not limit what a follow-up may pick: a case a filter drops, or one given to another assistant, stays selectable on a device until its next update, and a follow-up on it is stored. Only an inactive or missing case is refused.

## What's next

* "[Published lists](published-lists.md)" explains when a device gets a fresh copy of a list.
* "[Links and the case link](case-links.md)" explains what the repository build writes into the database.
* "[Form actions](case-actions.md)" explains how a follow-up changes its case.
