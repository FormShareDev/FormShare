---
description: FormShare maintains a live CSV of currently active cases that ODK Collect downloads alongside follow-up forms, so enumerators always pick from an up-to-date list of cases.
---

# The real-time CSV file

In a [longitudinal project](README.md), enumerators in the field need to pick which case they are visiting before they can fill in a follow-up form. FormShare maintains a **real-time CSV file** of the currently active cases that ODK Collect downloads along with each follow-up form. The CSV is regenerated whenever the case list changes, so the picker the enumerator sees in the field is never more than one form-sync stale.

You don't need to upload or maintain this file yourself. FormShare creates it, updates it, and serves it. Your only job is to decide which fields from the case creator form should be visible in the picker.

## How ODK Collect gets the file

ODK Collect downloads the real-time CSV through the same mechanism it uses for any [external data file](https://docs.getodk.org/form-datasets/) referenced by an ODK form:

1. The follow-up form's case-selector field is declared as a `select_one_from_file` against a CSV that lives among the form's "media files" — for example `list_caseselector.csv`.
2. When ODK Collect syncs the form, it reads the form's manifest from FormShare. The manifest includes the real-time CSV with its current MD5 hash and a download URL.
3. ODK Collect compares the manifest's hash against the local copy:
   * If the hash matches, ODK Collect skips the download (the cached copy is still current).
   * If the hash differs, ODK Collect downloads the new CSV before letting the enumerator open the form.

The MD5 check means the CSV is only re-downloaded when it actually changed — useful for projects where enumerators are on intermittent mobile connections.

## When the file is regenerated

FormShare regenerates the CSV whenever the case list changes in a way that an enumerator would care about:

* A new case is registered through the case creator form (most common).
* A case is **deactivated** by a follow-up form — it drops out of the CSV. See "[Activating / de-activating a case](activate-deactivate-case.md)".
* A previously inactive case is **reactivated** — it reappears in the CSV.
* The case data is corrected through the [web interface](../cleaning/in-a-repository/web-interface.md) or [API](../cleaning/in-a-repository/api.md), and that correction affects a visible field.

Regeneration is on-demand: the CSV is rebuilt the next time ODK Collect requests the form manifest *and* the existing file is out of date. There is no background scheduler — if no client asks for the manifest, no work is done.

## What the CSV contains

Each row in the CSV is one **active** case. Inactive cases are not included; they reappear only if the case is reactivated.

Each row has, at a minimum:

* The **case ID** — the case's long-lived identifier.
* The **case label** — the human-readable name the enumerator will see in the picker.
* Any **additional fields** you have configured in the case lookup table (see below).

The exact filename, headers, and shape of the file follow ODK Collect's requirements for a `select_one_from_file` datasource — you don't need to know the details to use it, but if you want to build a custom client that consumes the same file, the format is standard ODK CSV.

## Configuring which fields appear

By default, the CSV contains only the case ID and case label. You can add more fields so enumerators can search and filter cases in the field — for example, by region, by enrollment date, or by household head's gender.

From the case creator form's details page, click **Case lookup table**. The case lookup configuration page opens, listing every variable from the case creator form's main table.

<!-- TODO screenshot: the case-lookup configuration page with several fields checked and aliased. -->

For each variable you can decide:

* **Include in CSV** — whether the field appears in the real-time CSV at all. Case ID and case label are always included; everything else is opt-in.
* **CSV column name (alias)** — what the field is called in the CSV. By default this matches the ODK variable name, but you can override it to give enumerators friendlier labels.

Click **Save**. The next time the CSV regenerates (e.g., the next time ODK Collect syncs the follow-up form), the new fields will appear.

{% hint style="info" %}
**Keep it lean.** Every field in the CSV is downloaded to every device every time the file changes. Adding ten extra columns to a 50,000-case project means tens of megabytes of CSV that have to ride down to every phone. Pick the fields enumerators actually search by; leave the rest out.
{% endhint %}

## Updating the case lookup configuration

You can change which fields are included at any time. The next regeneration of the CSV picks up your changes. Enumerators will see the new columns the next time ODK Collect syncs the form.

Changing the case lookup configuration does not change the underlying case data — it only changes what travels in the CSV.

## When a case doesn't show up in the picker

If an enumerator can't find a case they expect to see, check:

* **Is the case active?** A case marked inactive (by a follow-up form with the *deactivate* effect, or manually flagged) is excluded from the CSV. See "[Activating / de-activating a case](activate-deactivate-case.md)".
* **Has ODK Collect synced recently?** The picker shows whatever was in the CSV the last time the device synced. Force a manual form refresh on the device.
* **Did the case submission succeed?** A case creator submission can land in the [error log](../cleaning/submissions-with-errors.md) instead of the database — for example because the case ID duplicates an existing one. In that case the case was never registered. Drain the error log.
