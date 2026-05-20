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

Excel can connect to FormShare in **two** ways:

* The **built-in OData feed connector** — no installation, read-only, fine for quick imports.
* The **FormShare Excel Add-in** — a task-pane that loads data into the sheet, lets you edit it inline, and commits changes back to FormShare. See "[Using the Excel Add-in](#using-the-excel-add-in)" below.

For the built-in feed:

1. Copy the OData URL.
2. In Excel, **Data** → **Get Data** → **From Other Sources** → **From OData Feed**.
3. Paste the URL. **Basic authentication** with API key and secret.
4. Pick the tables to load.

> Add screenshot of Power BI Desktop's navigator with several FormShare tables listed (main table, repeats, lookups).

## Using the Excel Add-in

The **FormShare Excel Add-in** is an Office JS task pane that connects Microsoft Excel directly to the OData service. It is the right choice when you want to **view, filter, edit, and commit** changes to survey data without leaving Excel. It works on Windows, Mac, and Excel Online — and unlike the built-in OData feed, it supports write-back: changes you make in the sheet are pushed back into the form's repository.

### What you need

* Microsoft Excel on Windows (Microsoft 365 v2102+), Excel 2021, Excel on Mac (16.50+), or Excel Online.
* The add-in installed by your IT administrator — or follow the individual-install steps below.
* Your **API key** and **API secret** from [your account](../fundamentals/your-account.md#your-api-key).

### Install the add-in yourself

If your IT department hasn't already deployed the add-in to your account, install it yourself:

1. Download `manifest.xml` from [https://odata.formshare.app/excel-addin/manifest.xml](https://odata.formshare.app/excel-addin/manifest.xml). If your browser opens the file inline, press **Ctrl+S** to save it.
2. Save the file somewhere stable on your machine — for example `Downloads\FormShare_Add-in`.
3. Right-click the `FormShare_Add-in` folder and pick **Properties**.
4. Open the **Sharing** tab → **Share** → share the folder with yourself → **Done**. Copy the **Network Path** shown at the end of the dialog.
5. Open Excel → **File** → **Options** → **Trust Center** → **Trust Center Settings** → **Trusted Add-in Catalogs**.
6. Paste the Network Path you copied, click **Add Catalog**, tick **Show in menu**, then **OK** → **OK**.
7. Close Excel.

> Add screenshot of Excel's Trusted Add-in Catalogs dialog with the Network Path added and "Show in menu" ticked.

### Link the add-in to your workbook

After installing the catalog, attach the add-in to a workbook:

1. Open Excel.
2. **Developer** → **Add-ins** → **Shared Folder** tab.
3. Double-click **FormShare OData**.
4. The FormShare OData task pane opens on the left side of your workbook.

### Connect and load data

1. Click **Open FormShare** in the ribbon to bring up the task pane.
2. Click **New connection** and provide:
   * The **server base URL** of your FormShare instance.
   * Your **API key**.
   * Your **API secret**.
   Click **Test** to verify the credentials, then **Save**.
3. Select the saved connection. Pick the form table you want from the dropdown.
4. Click **Load table**. The data lands in the active sheet as a formatted Excel table.
5. For large datasets, use **Load more** to bring in additional rows incrementally.

{% hint style="info" %}
**Pro tip — Row filter**: Before loading, filter by `rowuuid` or `root_rowuuid` to load only the rows you need to clean instead of the full dataset. This is the fastest way to focus on a specific subset for data-cleaning work.
{% endhint %}

### Edit and commit changes

The add-in is built around a read-edit-commit loop:

1. Click any cell and type to edit. The add-in highlights what has changed: **rows you have touched** turn yellow, and **individual edited cells** turn amber. This makes it easy to see at a glance what is staged for commit.
2. When you're done editing, click **Review & commit** in the task pane. A summary of your pending changes appears.
3. Click **Commit**. The add-in sends each row independently to FormShare. Rows that fail (validation rejected by the repository — for example a value that violates a lookup constraint) turn red so you can see exactly which records didn't go through. A failure on one row does not affect the others; the rest commit normally.
4. After committing, click **Close** to keep your current data in the sheet, or **Close & refresh** to reload the data from FormShare (useful if other people have also been editing).

> Add screenshot of the task pane showing the Review & commit panel with several rows highlighted in different colours.

### What the add-in does and doesn't do

The add-in is purpose-built for **data curation in Excel**. It is excellent for:

* Reviewing a batch of submissions in a familiar tool.
* Making structured edits to many cells at once (find/replace, fill down, formula-based cleanup).
* Working offline-ish: the data sits in the sheet, edits are local until you choose to commit.

It is not a replacement for the [web cleaning interface](../data-management/cleaning/in-a-repository/web-interface.md) or the [cleaning API](../data-management/cleaning/in-a-repository/api.md):

* The add-in cannot delete submissions.
* The add-in does not edit multi-select selections (use the web interface for those).
* The add-in does not change primary keys or `rowuuid` values.
* Every change still goes through the same audit log — you can see exactly which assistant or user made which edit, regardless of whether it came from Excel, the web, or the API.



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
