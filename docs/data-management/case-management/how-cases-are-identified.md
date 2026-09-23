---
description: >-
  Every row of every repository carries a rowuuid. Published lists and case
  links are built on it rather than on the variable that controls duplicates,
  and this page explains what that means for your forms and your exports.
---

# How cases are identified

Everything in case management links through one column: `rowuuid`. Read this page before you design the forms, because it decides what a follow-up form stores and how you join the data afterwards.

## Every row has a rowuuid

Every row of every table in a [repository](../../fundamentals/repositories/README.md) has a `rowuuid`. It is a 36-character identifier such as `5c1d9a7e-3b2f-4c8a-9e1d-2f3a4b5c6d7e`, generated when the row is stored, unique in the world, and never changed. A school registration has one. Each teacher in its repeat has one of its own. So does every interview. It is FormShare's name for the row, independent of anything the enumerator typed.

## It is not the variable that controls duplicates

When you upload a form, FormShare asks which variable controls duplicate submissions. In the [walkthrough](walkthrough.md) the answer is `school_emis`. That variable is the form's **primary key**: it is how FormShare refuses a second registration of the same school, and it is a code people recognise. Both columns live side by side in the table:

| school\_emis | school\_name | rowuuid |
| --- | --- | --- |
| U0012345 | Kira Primary School | 5c1d9a7e-3b2f-4c8a-9e1d-2f3a4b5c6d7e |
| U0098765 | Nakasero Primary School | 9e8d7c6b-5a4f-4e3d-8c2b-1a0f9e8d7c6b |

A published list identifies each case by its `rowuuid`, which is what the list's `name` column carries. The follow-up form stores that value in its selector (`school_id`, `teacher_id`), and the database relationship is built on it. There are four reasons for that:

* **Every table has it, including repeats.** A teacher row has no primary key of its own, because a repeat only numbers its rows within one submission, but it has a `rowuuid`, so it can be a case.
* **It never changes.** If an EMIS code was mistyped and someone corrects it later in the [data editor](../cleaning/in-a-repository/web-interface.md), every interview still points at the right school. A link built on the code would break, or point somewhere else.
* **It is unique by construction**, across tables and across projects, so the database can enforce the relationship. An interview cannot refer to a teacher that does not exist, and a teacher row cannot be deleted while interviews refer to it.
* **Nobody types it.** The form never shows it. The enumerator sees the label.

The primary key keeps doing what it is for: refusing duplicates and giving people a code they recognise. Serve it as a column of the list (`school_emis` served as `emis` in the walkthrough) when a form or an export needs to show it.

{% hint style="info" %}
**`rowuuid` is not ODK's `instanceID`.** The instance ID identifies the submission file a device sends, which is what makes a resend harmless. The `rowuuid` identifies the row in the database.
{% endhint %}

## parent\_rowuuid and root\_rowuuid

Beside `rowuuid`, every row of a data table carries two more identities:

| Column | In the main table | In a repeat table |
| --- | --- | --- |
| `rowuuid` | the row's own identity | the row's own identity |
| `parent_rowuuid` | empty, because a main row has no parent | the identity of the row it belongs to: the main row for a repeat, the enclosing repeat's row for a nested repeat |
| `root_rowuuid` | the row's own identity | the identity of the main row of the submission, whatever the depth |

So the teachers of Kira Primary School all have `parent_rowuuid = 5c1d9a7e-…`. Serving `parent_rowuuid` in the teacher list, under the name `school_id`, is what lets the interview show only the teachers of the school just picked: the school list's `name` and the teacher list's `school_id` hold the same identity.

## Following the chain

After the walkthrough, a row of the interview holds:

| Column | Value | Points at |
| --- | --- | --- |
| `rowuuid` | its own identity | nothing; it is the interview's own name |
| `teacher_id` | `9e8d…` | `teachers.rowuuid` in the registration's repository: the case link, enforced |
| `school_id` | `5c1d…` | `maintable.rowuuid` in the registration's repository: a reference, enforced too |
| `district` | `wakiso` | a value from the value list, not an identity |

To join interviews with the registration outside FormShare, join on those columns: `interview.teacher_id = teachers.rowuuid`, then `teachers.parent_rowuuid = maintable.rowuuid` to reach the school.

## Where you meet it

You will see `rowuuid` in four places:

* In the [data editor](../cleaning/in-a-repository/web-interface.md), as a read-only column.
* In the [exports](../data-products/README.md) of a repository.
* In every published list, as the `name` column.
* In the selector columns of your follow-up forms, holding the case each submission is about.

## What's next

* Build a workflow end to end in the "[Walkthrough: schools and teachers](walkthrough.md)".
* See how identities are served to devices in "[Published lists](published-lists.md)".
