---
description: >-
  How a script in R, Python or any other language posts Data Quality flags on
  a form's records and lists them through the plugin's flags API, and the
  FormShare R package functions that do it.
---

# The flags API

Checks of your own, written in R, Python or anything else, can raise flags on a form's records through the flags API. Their flags count in the score beside the flags of the form's checks, show on the Data quality page and in Clean data, and a supervisor can dismiss them like any other.

The **Send flags from a script** box of the Data quality page shows the two addresses for the form, with an example.



## Who can use it

The project's owner, administrators and editors, with the API key and secret of their own account (see "[Your API key](../../fundamentals/your-account.md#your-api-key)"). An assistant's key does not work here.

The form must have a repository with room for a score (see "[What a form needs](README.md#what-a-form-needs)"). A form without a repository answers HTTP 404, and so does a project where you are only a member. A repository without room for a score refuses every call with HTTP 400 and the message the Data quality page shows.

## Signing in

Ask for a token with the key and the secret:

```
POST /api/1/security/login
{"X-API-Key": "...", "X-API-Secret": "..."}
```

The answer holds the token and when it expires, 24 hours later:

```json
{"status": "200", "result": {"token": "...", "expires_on": "..."}}
```

Send the token with every call, in the header `Authorization: Bearer <token>`. An unknown or expired token is answered with HTTP 401.

## Posting flags

```
POST /user/{owner}/project/{project}/form/{form}/dq/flags
```

`{owner}` is the user name of the project's owner, `{project}` the project's code and `{form}` the form's ID, as in the address of the form's page. The body is JSON:

```json
{
  "source": "script:outliers",
  "results": [
    {"rowuuid": "...", "flags": [
      {"check": "income_outlier", "severity": "major",
       "message": "Income is 3 IQR above the median",
       "fields": ["income_30d"], "value": "450000"}
    ]},
    {"rowuuid": "...", "flags": []}
  ]
}
```

| Field | Required | What it holds |
| --- | --- | --- |
| `source` | Yes | Who raises the flags: 1 to 64 letters, digits or `_ . : -`, such as `script:outliers`. Give each script its own. |
| `results` | Yes | One entry for each record the script checked, 5,000 at most. |
| `rowuuid` | Yes | The record's `rowuuid`, the identity of its row in the main table. Each record once. |
| `flags` | No | The record's flags, 100 at most. An empty list says the record was checked and found clean. |
| `check` | Yes, in a flag | The name of the check that failed: 1 to 64 letters, digits or `_ . : -`. |
| `severity` | Yes, in a flag | `critical`, `major` or `minor`. |
| `message` | Yes, in a flag | What is wrong, 1 to 1,000 characters. |
| `fields` | No | The columns the flag is about, 20 at most. A flag that names an answer someone may change sends assistants to fix it in Clean data; one that does not points to the submission as a whole. |
| `value` | No | The value judged, 1,000 characters at most. |

### What a post does

For each record it lists, a post replaces the open flags its `source` had on that record:

* a flag the post names that is not there yet is added, open, with the next issue number of the repository;
* a flag that is there takes the new severity, message and value, and keeps its number and when it was first found;
* an open flag of the source that the post no longer names is marked as no longer found;
* a flag a supervisor dismissed stays dismissed, and one no longer found opens again when the post names it.

The records the post does not list keep their flags. Posting the same results twice changes nothing.

Within one source, a flag is identified by its `check` and its `fields`, in any order. The same check on the same fields twice for one record is refused.

Sources add up: a record's score counts the open flags of every source, the form's own checks included. The checks use sources that start with `check:`, so do not give a script a source that starts with `check:`.

### What a post answers

A post is checked whole before anything is stored, and stored all of it or none. A record that is not in the form's repository, a wrong severity or a missing message refuses the whole post with HTTP 400, and `errors` names each part, such as `results[3].flags[0].severity: critical, major or minor`.

A post that is stored answers:

```json
{"result": {"records": 2, "added": 1, "updated": 0, "resolved": 0,
            "scores": {"<first rowuuid>": 85, "<second rowuuid>": 100},
            "gone": []}}
```

| Key | Meaning |
| --- | --- |
| `records` | How many records it stored results for. |
| `added`, `updated`, `resolved` | How many flags it added, updated and marked as no longer found. |
| `scores` | Each record's new score. |
| `gone` | The records deleted, or moved to the logs, while the post was stored. They were left out. |

## Listing flags

```
GET /user/{owner}/project/{project}/form/{form}/dq/flags
```

The same address, with GET, lists the form's flags, the most severe first and then the most recently found.

| Parameter | What it selects |
| --- | --- |
| `status` | `open`, `dismissed`, `resolved` (no longer found) or `removed` (with their submission). All by default. |
| `severity` | `critical`, `major` or `minor`. |
| `check` | A check's name. |
| `source` | A source, such as `script:outliers`. |
| `rowuuid` | One record. |
| `issue` | One issue, as `DQ-57` or `57`. |
| `page`, `rows` | Which page, and how many flags to a page: 100 by default, 1,000 at most. |

The answer holds `total`, `page`, `rows` and `flags`. Each flag has, among others:

| Key | Meaning |
| --- | --- |
| `issue`, `number` | Its issue, as `DQ-57`, and the number alone. |
| `rowuuid`, `record` | The record's identity and its label. |
| `row` | The row of a repeat it is about, when it is. |
| `source`, `source_label` | Who raised it, and what the pages call it: a check's description, or the script's source. |
| `check`, `severity`, `message`, `fields`, `value` | As posted, or as the check found them. A measure of the record shows as its own name, such as `measure:length`. |
| `status` | `open`, `dismissed`, `resolved` or `removed`. |
| `first_seen`, `last_seen` | When it was first and last found. |
| `resolved_by`, `resolved_on`, `note` | Who dismissed it, or the source that no longer found it; when; and a supervisor's reason. |
| `removed_by`, `removed_on`, `removed_reason`, `removed_as` | For a flag removed with its submission: who removed it, when, why, and how (`deleted` or `logs`). |
| `record_score`, `submitted_by` | The record's score now, and its officer. |

## From R

The FormShare R package, from version 1.2.0, posts and lists flags. It finds the form by the name of its repository, as `get_repositories()` lists it, which needs the Remote SQL plugin (version 2.1 or later) on the server.

| Function | What it does |
| --- | --- |
| `set_flags(repository, flags, checked = NULL, source = "script:R")` | Posts a data frame of flags, one row per flag, with the columns `rowuuid`, `check`, `severity` and `message`, and optionally `fields` (names separated by commas) and `value`. `checked` lists the records checked, by default those in `flags`: each one loses the flags of `source` that `flags` does not give again. It checks the flags before posting, sends them in posts of 5,000 records, and returns the counts and a data frame of the new scores. |
| `get_flags(repository, status, severity, check, source, rowuuid, issue)` | Lists the flags as a data frame, every page of them. Every argument but `repository` is optional, and filters as the parameters of the API do. |

```r
library(FormShare)
my_connection <- FormShare$new(server_url = "https://formshare.example.org",
                               user_id = "my_user", api_key = "my_api_key",
                               api_secret = "my_api_secret")
my_connection$login()
flags <- data.frame(rowuuid = "a_rowuuid", check = "income_outlier",
                    severity = "major", message = "Income is 3 IQR above the median",
                    fields = "income_30d", value = 450000)
my_connection$set_flags("a_repository", flags, source = "script:outliers")
my_connection$get_flags("a_repository", status = "open")

# The same record, checked again and found clean: its flag is no longer found
my_connection$set_flags("a_repository", NULL, checked = "a_rowuuid",
                        source = "script:outliers")
```

IMPACT's `cleaningtools`, in the FormShare branch of the `qlands/cleaningtools` fork, has `fs_flag_checks()`, which posts what its checks found as flags.
