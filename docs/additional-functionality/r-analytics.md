---
description: R Analytics gives you a guided way to pull FormShare data into R from your own machine — connection examples, the right credentials, and a starter script for each form.
---

# R Analytics

**R Analytics** is for data analysts who work in R. It doesn't run R inside FormShare — instead, it tells you exactly how to connect from your local R session to a specific form's data, with the right credentials and a copy-paste starter script you can adapt to your analysis.

## Where to find it

Open any form's details page. A tab marked with the **R** logo appears alongside the existing tabs. Click it to open the R Analytics view.

<!-- TODO screenshot: the form details page showing the R tab in the tab bar. -->

## What it shows

The R tab displays:

* A check on whether you have API credentials. If you don't yet have an API key and secret, the page tells you to generate them from "[Your account](../fundamentals/your-account.md#your-api-key)" and shows a direct link.
* A **read-only code editor** with a complete starter script in R, syntax-highlighted, with your form's schema name and connection details already filled in.

The starter script shows you:

1. How to install the FormShare R package, if you don't have it.
2. How to open a connection from R to your FormShare instance using the URL of the instance, your user name, and your API key and secret.
3. How to log in.
4. How to list every repository your user account can see.
5. How to run a SQL query against the current form's repository and load the result into an R `data.frame`.

The skeleton query selects everything from the form's main table — replace it with the SELECT statement that fits your analysis.

## Workflow

1. From your account profile, [generate an API key and secret](../fundamentals/your-account.md#your-api-key) and save them in your password manager. The secret is only shown once.
2. Open the form's R tab in FormShare. Copy the starter script.
3. In your local R / RStudio session, paste the script. Replace the placeholder API key and secret with the values you saved.
4. Run the script. You now have a `data.frame` with the form's submission data ready for analysis.
5. From there, do what you would normally do in R — wrangle with `dplyr`, model with `lm` / `glmer`, visualize with `ggplot2`, render to a report with `quarto`.

{% hint style="info" %}
**R runs on your machine.** The R tab is a connection guide, not an in-browser R interpreter. The code runs in whichever R / RStudio session you launch on your own computer; the connection pulls data from FormShare over HTTPS into that session.
{% endhint %}

## Permissions

* Every user who has access to a form's details page can see the R tab.
* The connection uses **your user's** API key and secret, so what R can pull from FormShare is exactly what you can see in the FormShare UI — no more, no less.
* If you cannot generate API credentials on your account, ask your FormShare administrator; some instances restrict this.

## Relation to FormShare data

R Analytics talks to the **same repositories** as the [Analytics](analytics.md) and [Superset](superset.md) features — they all query the live MySQL backing the form. New submissions are visible to your R session as soon as they land in the repository.

## When to use R vs other options

* **R Analytics**: you want to script statistical analyses, build models, or produce reproducible reports.
* **[Analytics](analytics.md)**: you want quick interactive SQL on the same data without leaving the browser.
* **[Superset](superset.md)**: you want interactive charts and dashboards.
* **[OData](odata.md)**: you want to connect Power BI, Tableau, or Excel directly.
* **[Data products](../data-management/data-products/)**: you want a one-shot export to ship to someone.

## What's next

* "[Your account — Your API key](../fundamentals/your-account.md#your-api-key)" — how to generate the credentials R needs.
* "[Analytics](analytics.md)" — the interactive SQL counterpart.
