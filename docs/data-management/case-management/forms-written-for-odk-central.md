---
description: >-
  A form written for ODK Central's entities uploads to FormShare and keeps its
  answers, but creates and updates no entity. Its form page lists what to set
  up instead: a published list, a case link and actions.
---

# Forms written for ODK Central

ODK Central keeps cases as **entities**, declared in the form itself. An *entities* sheet names the entity list, and a *save\_to* column on a question says which property of the entity the answer fills. FormShare keeps cases in the repository of the form that registers them instead, and serves them to other forms through [published lists](published-lists.md).

A form written for Central still uploads to FormShare like any other, and its answers are kept. FormShare does not create or update entities, so nothing in the *entities* sheet happens by itself: no list appears, and no case changes.

## The Written for ODK Central box

On the form's page, owners, administrators and editors see a box titled **Written for ODK Central**. It says what the form does in Central, for example *This form was written for ODK Central: it creates entities in the list "trees".* It then says that FormShare keeps the answers without creating or updating entities, and lists the steps that set up the same workflow here, in the form's own names.

Each step leaves the box once it is done, and the box goes with the last one. FormShare reads the declaration from the form as it was uploaded, so the box appears on any form with an *entities* sheet, a new version of a form included.

| Step the box shows | Shown for | Leaves the box when |
| --- | --- | --- |
| Publish a list named "trees.csv" from this form, or from the repeat that holds one entity per entry | A form that creates entities | The project publishes a list with that file name |
| Publish a list named "trees.csv" from the form that registers these cases | A form that only updates entities | The project publishes a list with that file name |
| Attach "trees.csv", with the header of the list, as a file of this form, then confirm its case link | A form that updates entities | The form has its [case link](case-links.md) |
| Add an action for each value it saves to the case | A form that updates entities and saves values | The form has an [action](case-actions.md) |

The file name is the entity list's name with `.csv`: `trees.csv` for a list called `trees`. That is the file the form's follow-ups ask for when they are uploaded.

## A form that registers entities

The step to publish the list comes with details, when they apply:

* Each question with a *save\_to* is served under the name it saves to. A question `location` saved to `geometry` is served as `geometry`, so follow-ups written for Central find the column they expect.
* Central labels each entity with an expression, and a list takes its label from one column. When the expression is a single question, the box names it; otherwise it lists the questions the expression uses, to choose one.
* When the sheet has a *create\_if*, Central creates an entity only when that condition holds. The box asks you to give the list the same condition as its [filter](list-filters.md).

Publishing needs the form's repository, as for any list.

## A form that updates entities

The form follows up a case it names with its *entity\_id*. Once the list is published, attach its file to the form with the list's header, from **Sample CSV** for example, and confirm the case link in **Links to lists**.

The values the form saves to the entity become actions. Each one must set a column or a property of the cases, so add a [property](properties.md) for a value the registration did not collect. When the *entities* sheet has an *update\_if*, the box asks you to use that condition as the **When** of each action.

## How Central's words map

| ODK Central | FormShare |
| --- | --- |
| Entity list | A published list, drawn from the form that registers the cases |
| Entity | A case: a row of that form's repository |
| *save\_to* at registration | A served column, under the *save\_to* name |
| *save\_to* in an update | An action that sets a property or a column of the case |
| Entity label | The list's label column |
| *create\_if* | The list's filter |
| *entity\_id* | The selector of the case link |
| *update\_if* | The **When** of an action |

## When the project cannot publish lists

In a project created with **Use a case/longitudinal workflow**, or on a server without case management, the box lists no steps. It only says that the form was written for Central, and that FormShare keeps its answers but does not create or update entities.

## What's next

* Publish the list in "[Published lists](published-lists.md)".
* Write the actions of an update form in "[Form actions](case-actions.md)".
