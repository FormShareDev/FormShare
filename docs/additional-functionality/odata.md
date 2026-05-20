---
description: OData exposes every form repository as a live OData v4 endpoint, so Excel, Power BI, Tableau, and any other OData-aware tool can query FormShare directly without exports.
---

# OData

**OData** turns each form's repository into a live OData v4 service that Microsoft Excel, Power BI, Tableau, and other business-intelligence tools can connect to natively. Once you've configured a connection in your tool, your dashboards and worksheets refresh from FormShare directly — no file exports, no manual syncs, no stale data.

OData is the bridge of choice when your team already lives in a specific BI tool and you want FormShare to feel like just another data source.

## Where to find it

Open any form's details page and click the **Links** tab. The OData section appears with:

* The **OData URL** for this form, with a copy-to-clipboard button.
* A **What is OData?** info button that opens a help modal with tool-specific setup instructions (Power BI Desktop, Tableau, Excel).

> Add screenshot of the form details "Links" tab with the OData URL field and copy button.

## What you need to connect

To authenticate against the OData endpoint you need an API key and secret. Generate them once from "[Your account — Your API key](../fundamentals/your-account.md#your-api-key)" and keep them in your password manager. Save the secret on first generation — it is only shown once.

When a tool asks for credentials to connect to FormShare, choose **Basic authentication**:

* **Username**: your API key
* **Password**: your API secret

## Workflow

### Power BI Desktop

1. Copy the OData URL from the Links tab.
2. In Power BI Desktop, click **Get Data** → **OData feed**.
3. Paste the URL. Click **OK**.
4. When asked for credentials, pick **Basic** and enter your API key and secret.
5. Power BI's navigator lists every table the form's repository exposes — main table, repeats, lookups, multi-selects. Tick the ones you want.
6. Click **Load** (or **Transform Data** to clean before loading). Build reports as usual.

For **scheduled refresh** on Power BI Service, configure a gateway with the same basic-auth credentials.

### Tableau Desktop

1. Copy the OData URL.
2. In Tableau, open the **Connect** pane and pick **OData** (under "To a Server").
3. Paste the URL and click **Sign In**.
4. Choose **Basic authentication**. Enter API key and secret.
5. The form's tables appear as data sources. Drag the one you want into your workbook.

### Excel

1. Copy the OData URL.
2. In Excel, **Data** → **Get Data** → **From Other Sources** → **From OData Feed**.
3. Paste the URL. **Basic authentication** with API key and secret.
4. Pick the tables to load.

> Add screenshot of Power BI Desktop's navigator with several FormShare tables listed (main table, repeats, lookups).

## What the OData endpoint exposes

There is **one endpoint per form**, and the endpoint mirrors the form's repository:

* The form's **main table** as one entity set.
* Each **repeat group** as its own entity set, with navigation properties back to the main table so your tool can drill into nested data.
* Each **lookup table** as an entity set holding the code / label pairs.
* Each **multi-select** as an entity set holding the (submission, selected option) pairs.

Columns match the field names in the [data dictionary](../data-management/data-dictionary/) and carry their correct types — numbers as numbers, dates as dates, booleans as booleans.

## Permissions and what you see

* Project owners and editors get full read/write access via OData. PATCH updates flow through the same audit log as [API edits](../data-management/cleaning/in-a-repository/api.md).
* Read-only collaborators get read access only.
* Assistants with the **Can clean** permission on the form can also use OData to read and edit.
* Assistants without cleaning permission, partners, and anonymous users cannot use the endpoint.

The endpoint respects the form's [sensitive-field configuration](../data-management/data-dictionary/marking-sensitive-fields.md). Tools authenticated as users with full access see the underlying values; tools authenticated as partners or limited collaborators see the public projection.

## Live data and pagination

OData queries are **live** — each request hits the form's repository directly, so dashboards never go stale. New submissions are visible to BI tools as soon as they're in the database.

Pagination follows OData's standard `$skip` / `$top` model. By default, responses return a page of up to ~1,000 rows; the maximum page size is just over 10,000. Power BI, Tableau, and Excel all handle pagination transparently — there's nothing you need to configure.

## What you can do beyond reads

OData supports the standard query operators:

* `$filter` for filtering rows.
* `$select` for projecting columns.
* `$orderby` for sorting.
* `$top` and `$skip` for paging.
* `$count` for record counts.
* `$expand` for joining repeat tables to their parent.
* `$apply` for aggregations (sum, count, average, min, max) — useful when you want the server to compute totals rather than pulling raw rows.

For users with write permissions, **PATCH** is supported on individual rows — the OData service can therefore be used to clean data from a BI tool that supports OData write-back. Bulk deletes and inserts are not supported.

## Standards

* **OData v4.0** — the version Excel, Power BI, and Tableau speak natively.
* **HTTP Basic authentication** for credentials.
* **JSON** for payloads, with OData metadata for entity-set relationships.

## What's next

* "[Your account — Your API key](../fundamentals/your-account.md#your-api-key)" — how to obtain the credentials.
* "[Data products](../data-management/data-products/)" — for snapshot exports when live connectivity isn't possible.
* "[API data cleaning](../data-management/cleaning/in-a-repository/api.md)" — FormShare's native HTTP API for write-back without an OData layer.
