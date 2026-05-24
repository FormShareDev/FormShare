---
description: Superset gives you a full Apache Superset workspace pre-configured with read access to your form repositories — drag-and-drop charts, dashboards, and a SQL Lab for power users.
---

# Superset

**Superset** brings [Apache Superset](https://superset.apache.org/) — a leading open-source business intelligence platform — into your FormShare workflow. It's the right tool when you want to build interactive dashboards and charts that update as new submissions arrive, without exporting data or writing code.

## Where to find it

Open the Analytics page from your user menu (`/user/<your-username>/analytics`). A **Superset** tab appears next to the other Analytics tabs. Click it to manage your Superset access.

<!-- TODO screenshot: the Superset tab on the Analytics page showing the Enable button before activation. -->

## First-time setup

The first time you open the Superset tab, you'll see a message saying the Superset module is not yet enabled. Click **Enable**.

FormShare then:

* Provisions a Superset account for you, matched to your FormShare identity.
* Connects that Superset account to your repositories — every form you own or collaborate on is exposed as a table you can query.
* Sets a temporary password.

The page reloads with a warning: **You need to set your Superset password**. Click the button, enter a new password (twice), and save. From then on, that is the password you use to sign in to Superset.

<!-- TODO screenshot: the "Set your Superset password" modal. -->

## What you see after setup

Once your password is set, the Superset tab shows:

* Your **Superset username** (the same as your FormShare query user).
* An **Open Superset** button.
* A **Change your Superset password** button — for whenever you want to rotate it.

Click **Open Superset**. Superset opens in a new browser tab, signed in as you.

## What you do in Superset

Inside Superset, your repositories appear under a database connection bearing your name. From there you can:

* **Browse your data**: every form you own or collaborate on appears as a table. The columns match the field names in the form's [data dictionary](../data-management/data-dictionary/).
* **Create charts**: the chart builder (Create Chart from the Superset menu) lets you pick a table, drag fields onto X / Y / colour / size, choose a chart type (bar, line, pie, scatter, map, table — there are dozens), apply filters, and save. No SQL required.
* **Build dashboards**: drag your saved charts onto a dashboard, arrange them in tiles, add filters that apply across all charts, and share the dashboard with collaborators.
* **Run SQL** in **SQL Lab**: write any query you like against your repositories. Useful for one-off questions or for prototyping a query before turning it into a chart.

<!-- TODO screenshot: Superset's chart builder with a FormShare table selected and a bar chart in progress. -->

Superset queries are **live** — every time a chart loads, it hits the underlying repository directly. There is no snapshot or cache by default; new submissions show up automatically.

## Permissions and what you see

* The Superset database connection is **private to you**. Each user who enables Superset gets their own connection scoped to the forms they have access to.
* When you accept a new collaboration, the new form's schema is added to your Superset connection automatically.
* When you lose access to a form (removed as collaborator, form deleted), the corresponding schema is removed from Superset.
* If your FormShare account is deactivated, your Superset account is deactivated too.

Dashboards and charts you build inside Superset can be shared with other Superset users, subject to whatever access rules your administrator has configured inside Superset itself.

## Relation to FormShare data

Superset queries the **same live repositories** as [Analytics](analytics.md) and [R Analytics](r-analytics.md). New submissions are visible to your charts immediately; values fixed through the [web cleaning interface](../data-management/cleaning/in-a-repository/web-interface.md) or [API](../data-management/cleaning/in-a-repository/api.md) update the next time a chart refreshes.

## When to use Superset vs other options

* **Superset**: you want interactive dashboards and charts you and your team will look at repeatedly.
* **[Analytics](analytics.md)**: you want quick SQL exploration without building visuals.
* **[R Analytics](r-analytics.md)**: you need statistical modelling or scripted analysis.
* **[OData](odata.md)**: your team already uses Power BI, Tableau, or Excel and you want to plug FormShare into those.

## What's next

* "[Analytics](analytics.md)" — the SQL counterpart to Superset.
* "[Data products](../data-management/data-products/)" — for delivering data as a file.
