---
description: >-
  What FormShare refuses to do once lists and case links are in use, how active
  and inactive cases behave, and where a follow-up goes when its case is missing.
---

# Rules and safeguards

Once a form's repository has been built against a list, the shape of that list is part of a database. FormShare protects it, which means some things you may want to do are refused. This page collects those rules in one place so you meet them here rather than in the field.

## What FormShare refuses

| What you try | What happens |
| --- | --- |
| Delete a list that a built form uses | Refused. The **Delete** button is not offered while a form with a repository attaches the list. |
| Delete a list that only testing forms use | Allowed, with a warning. Those forms stop receiving fresh data and keep the file they already had. |
| Delete a form that feeds published lists | Refused, with the message *This form feeds the published lists …. Delete the lists first.* |
| Delete a case that has follow-ups | Refused by the database, because the follow-ups refer to it. Remove them first. |
| Change a list's key while a built form uses it | Refused. The form's database was shaped by the key. |
| Change the case link after the repository is built | Not possible, and the page says so. A new version keeps the link of the version it replaces. |
| Merge a new version that picks its cases from another list | Refused at the [merge check](../../fundamentals/repositories/merging-subversions-of-a-form.md), naming the list the version must keep. |
| A follow-up arrives for a case that is inactive or missing | The submission is refused by the database check and goes to the form's [error log](../cleaning/submissions-with-errors.md). |
| Put the case selector inside a repeat | The list is still served to the form, but no link is detected and nothing is enforced. Keep the question that picks the case outside every repeat. |

## Active and inactive cases

Every case has an `_active` flag, set to 1 when the row is stored. A list serves one side of that flag, chosen under **Rows to serve** when you [publish it](published-lists.md):

* A list of **active cases** is the usual one. Its check requires the case to exist and be active.
* A list of **inactive cases** serves the cases that were retired, for a form whose job is to bring them back. Its check requires the case to be inactive.

Today the flag changes through the [data editor](../cleaning/in-a-repository/web-interface.md) of the source form, where the column is editable. The *actions* feature that will let a follow-up submission change it is still being built.

## When a submission is refused

A follow-up whose case is unknown or inactive does not reach the repository. It is refused with the message *Case ID … is inactive or does not exist* and lands in the form's error log, under **Manage errors** on the form page. Nothing is lost: the submission sits there with its data, and you decide what to do with it.

The usual causes are worth knowing before you go looking:

* The registration that created the case was deleted.
* The case was deactivated between the moment the device fetched the list and the moment the submission arrived.
* The device is carrying an old copy of the list and the case never existed on this server.

"[Submissions with errors](../cleaning/submissions-with-errors.md)" covers what an assistant can do from there: fix the value, push it through, or disregard the submission.

## What's next

* "[Published lists](published-lists.md)" explains when a device gets a fresh copy of a list.
* "[Links and the case link](case-links.md)" explains what the repository build writes into the database.
