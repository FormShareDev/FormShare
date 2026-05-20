---
description: Analytics gives you direct SQL access to your form repositories and a personal workspace where you can build views, save intermediate results, and combine data across forms.
---

# Analytics

**Analytics** opens up FormShare's submission storage to direct querying. Where the [data products](../data-management/data-products/) section produces snapshot exports for offline analysis, Analytics gives you a live, server-side query interface against the same repositories — useful when you need to answer ad-hoc questions, combine data across forms, or build derived datasets without round-tripping through Excel.

## Where to find it

A new entry labeled **Analytics** appears in the left-hand sidebar. Click it to open the Analytics page at `/user/<your-username>/analytics`.

> Add screenshot of the Analytics page with the "Repositories" tab open, listing several form schemas with their project codes and form IDs.

## First-time setup

The first time you open Analytics, you'll see an **Enable** button. Click it. FormShare provisions a personal query account and a private workspace for you — this is a one-time action; from then on the page opens straight to your repositories.

## What you see

After enabling, the Analytics page is organized around two listings:

* **FormShare Repositories** — every form repository you have access to, whether you own the project or were added as a collaborator. Each row shows the project code, the form name and ID, and the schema identifier you would reference in a SQL query.
* **Personal Repository** — your private read-write workspace, identified by a unique ID (it starts with `FU_`). This is where you save query results, create views that combine data across forms, or stash intermediate tables for ongoing analyses. It starts empty; you fill it as you work.

## What you do with it

Once Analytics is enabled, you have direct query access to every repository in your list. The mechanics depend on the FormShare instance — some instances expose Analytics through an in-browser SQL console, others through downloadable client tools, others through both. Either way, the credentials you need are tied to the Analytics account that was provisioned when you clicked Enable.

Typical things to do from Analytics:

* **Ad-hoc questions about a single form**: "How many submissions were received in March?", "Which assistant has the highest error rate?", "What's the distribution of household sizes in this region?".
* **Cross-form joins**: combine baseline and follow-up forms by case ID; merge two regional surveys into one analytic dataset.
* **Derived tables in your personal workspace**: build a cleaned, deduplicated, recoded dataset once and keep it as a view for downstream consumers.
* **Pre-export validation**: run the same SQL you'd run after exporting — catch issues at the source before you generate a product.

## Permissions

* Every signed-in FormShare user can enable Analytics for themselves.
* You see only the repositories of forms you have access to — the same access rules that apply elsewhere in FormShare.
* When you accept a new collaboration, that project's forms appear in your Analytics list automatically. When you are removed from a project, they disappear.
* Your personal workspace is private to you; collaborators do not see it.

## Relation to FormShare data

Analytics queries the **same MySQL repositories** described in "[How does FormShare stores my data?](../fundamentals/repositories/how-does-formshare-stores-my-data.md)". You see the same tables (main, repeats, lookups, multi-selects) with the same names and the same join keys.

Analytics is **live** — there is no snapshot or delay. New submissions appear as soon as they land in the database, and edits made through the [web interface](../data-management/cleaning/in-a-repository/web-interface.md) or the [API](../data-management/cleaning/in-a-repository/api.md) are reflected immediately.

{% hint style="info" %}
**Analytics vs. data products**: Analytics is for *interactive* analysis on live data. [Data products](../data-management/data-products/) are for *delivering* data as a file. Use Analytics while you're exploring; use a product when you have a deliverable to share.
{% endhint %}

## What's next

* "[R Analytics](r-analytics.md)" — run R against the same repositories.
* "[Superset](superset.md)" — build interactive charts and dashboards on top.
* "[OData](odata.md)" — connect Power BI, Tableau, or Excel to live data.
