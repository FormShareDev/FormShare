---
description: The HTTP API for cleaning repository data programmatically from R, Stata, Python, or any other language.
---

# API data cleaning

When cleaning involves many rows or follows a script (range checks, deduplication, recoding), use the data-cleaning API instead of the [web interface](web-interface.md). The API lets a program update field values directly, with the same audit-log behavior and the same permission checks.

## Who can call the API

The API is authenticated with the **API key** of an [assistant](../../../fundamentals/tasks/) flagged with the **Can clean** permission on the form. Each assistant has a unique API key that can be generated and regenerated from the assistant's edit page in FormShare (look for the **API Key** field with the **Generate new key** button).

Treat the API key like a password — anyone with the key can edit the form's data as that assistant, and every change will be attributed to them in the audit log. Project owners' and collaborators' API keys do not work for this endpoint; cleaning is reserved to assistants.

> Add screenshot of the assistant edit page highlighting the "Generate new key" button next to the API Key field.

## Endpoint

```
POST /api_update
```

There is exactly one endpoint, and the same URL is used to update any record in any repository of any form on the FormShare instance. The combination of **API key** and **submission ID** (`rowuuid`) tells FormShare which assistant is acting and which row in which form they want to modify.

The body of the request carries:

* **`apikey`** — the assistant's API key.
* **`rowuuid`** — the unique row ID of the record you want to update.
* One or more **field names and values** — the columns you want to change.

The request can be sent either as **JSON** (with `Content-Type: application/json`) or as **form-encoded POST data**. Both work identically.

### Successful response

```json
{ "status": "OK", "message": "Update completed" }
```

### Error responses

On failure, the response body is JSON with an `error` message and an `error_type` for programmatic handling. Common error types:

* `api_key_missing` — no `apikey` was provided in the request.
* `rowuuid_missing` — no `rowuuid` was provided.
* `assistant_not_found` — the API key does not match any assistant.
* `project_not_found` — the rowuuid does not exist in any repository the instance knows about.
* `unauthorized` — the assistant exists but does not have the "Can clean" permission on the form that owns this rowuuid.
* `update_error` — the database rejected the update (type mismatch, lookup-table foreign-key violation, primary-key duplication, etc.). The `error` field contains the database's message.

## Examples

### curl (JSON)

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
        "apikey": "'"$FS_API_KEY"'",
        "rowuuid": "9c84c1d0-a8e3-4e69-9c2a-e2f53bca0f1a",
        "household_income": "12500"
      }' \
  https://your-formshare-instance/api_update
```

### Python

```python
import os
import requests

response = requests.post(
    "https://your-formshare-instance/api_update",
    json={
        "apikey": os.environ["FS_API_KEY"],
        "rowuuid": "9c84c1d0-a8e3-4e69-9c2a-e2f53bca0f1a",
        "household_income": "12500",
    },
)
response.raise_for_status()
print(response.json())
```

### R

```r
library(httr)
POST(
  url = "https://your-formshare-instance/api_update",
  body = list(
    apikey = Sys.getenv("FS_API_KEY"),
    rowuuid = "9c84c1d0-a8e3-4e69-9c2a-e2f53bca0f1a",
    household_income = "12500"
  ),
  encode = "json"
)
```

### Stata

Stata's `curl`-via-shell or the user-contributed `libjson` / `insheetjson` packages can hit the endpoint. The simplest approach is to write a `.do` file that loops over rows and invokes `curl` for each update.

## What the API can and cannot do

The API supports **updating field values** in any data table of the repository (main table, repeat tables, lookup tables). It does **not** support:

* **Deleting submissions** — only the project owner can delete, through the "Manage submissions" screen described in "[Working with submissions](../working-with-submissions.md)".
* **Changing multi-select selections** — multi-selects live in a separate junction table that is not exposed through this endpoint. Use the [web interface](web-interface.md) for multi-select edits.
* **Editing the primary key** of a submission — these columns are stripped from the payload before the update runs.
* **Editing the submission ID** (`rowuuid`) — this is FormShare's internal identifier and is immutable.

If you send a value that the database rejects (wrong type, lookup-table foreign-key violation, primary-key duplication), the update fails and no change is persisted.

## Audit log

Every successful update writes one entry per changed column to the form's audit log, with the assistant identified by the API key, the timestamp, the table, the column, the previous value, and the new value. API edits and web-interface edits look identical in the log — see "[The audit log](audit-log.md)".

## Practical tips

{% hint style="warning" %}
**Test on a small sample first.** Mistakes through the API can quickly affect thousands of records. Before running a bulk script, run it against one rowuuid and inspect the audit log to confirm the change went where you expected.
{% endhint %}

* **Rate**: There is no enforced rate limit, but very tight loops can exhaust database connections. Add a short sleep between requests if you're updating tens of thousands of rows.
* **Rollback**: There is no automatic rollback. If a script makes incorrect updates, you'll need to identify them through the audit log and run a corrective script with the original values (which the audit log preserves).
* **Multiple fields per call**: You can include as many field updates as you want in a single request body, as long as they all belong to the same `rowuuid`. Bundling reduces the number of round-trips.
* **API key storage**: Keep the API key out of source control. Put it in an environment variable (`FS_API_KEY`) or a secrets manager.
* **Getting the rowuuid**: The `rowuuid` is included in the data exports (CSV, JSON, Excel). Filter your dataset locally, then loop over the matching rowuuids to apply corrections.
