---
description: Data curation in FormShare — fixing errors, editing submitted records, moving and removing submissions, and reviewing the audit trail.
---

# Data curation

Even the most carefully designed survey produces records that need attention after the fact. Enumerators mistype values, devices submit duplicates, edge cases reveal themselves only once data starts flowing in. FormShare provides a complete set of tools for **data curation** — the day-to-day work of keeping a dataset clean, accurate, and well-documented.

This section covers:

* "[Cleaning data in a repository](in-a-repository/README.md)" — how to fix values inside submissions that already entered the database, using either the [web interface](in-a-repository/web-interface.md) or [the API](in-a-repository/api.md), with every change captured in [the audit log](in-a-repository/audit-log.md).
* "[Submissions with errors](submissions-with-errors.md)" — how to handle submissions that the database rejected (duplicate primary keys, missing required fields, unknown lookup options), through the checkout / revise / push workflow.
* "[Working with submissions](working-with-submissions.md)" — how the project owner manages submissions at the form level: deleting individual records, moving records back into the error log for re-cleaning, or wiping the whole repository.

## Who does what

FormShare assigns curation work to two distinct roles:

* **Assistants** (with the "Can clean" permission) do the cleaning. They edit values, handle errored submissions, and check revised records back into the database. The audit log records every change against the assistant's identity, so individual accountability is preserved even when many assistants work on the same form.
* **Project owners and collaborators** (with appropriate access) manage submissions at the form level. They can delete records, move records into or out of the error log, and inspect the audit trail. They typically do not perform individual cell edits — those go to assistants.

{% hint style="info" %}
**Why split the roles?** Separating curation (who fixes the data) from administration (who decides what data should exist) is a defense against accidental data loss. Assistants can be added or removed without granting them the power to wipe submissions; project owners retain control over the dataset's overall shape without having to do every edit themselves.
{% endhint %}

## Curation requires a repository

All of the tools in this section operate on forms with a repository. Forms in the [testing stage](../../fundamentals/forms/#the-testing-stage) have no relational storage, no audit log, and no error log — they can only be tested, not curated. Create a repository for the form before you begin curating; see "[Forms](../../fundamentals/forms/#create-a-repository)" for the steps.

Once the repository exists, the curation tools described here become available from the form's details page and from the [Assistant Access](../../fundamentals/tasks/) portal.

## The audit trail is non-optional

Every change to repository data — through the web interface, through the API, or as a side effect of moving submissions in or out of the database — is recorded in the audit log automatically. You cannot turn this off, you cannot delete entries, and the same recording mechanism applies regardless of which tool made the change. This guarantees that any value in the dataset can be traced back to who set it, when, and from where.

See "[The audit log](in-a-repository/audit-log.md)" for how to read and search it.
