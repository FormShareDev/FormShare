---
description: Form files are the supporting files an ODK form needs at submission time — typically CSVs for pulldata() and select_one_from_file, plus any media referenced by the form.
---

# Form files

ODK forms can depend on **supporting files** at submission time. The most common case is a CSV that the form pulls data from — a national-region list used by `pulldata()`, an option list used by `select_one_from_file`, a roster of subjects for a follow-up form. FormShare stores these files alongside the form, serves them to ODK Collect through the form manifest, and tells you when one is missing.

Form files are different from [project files](../project-files.md): project files are documentation for the team (PDFs, briefing notes) and never reach ODK Collect; form files are operational data the form itself needs in order to render and accept submissions.

## Where to find them

On the **Form details** page, look for the **Form files** tab. It lists every file currently attached to the form, with a download link and (if you have edit access) controls to upload or remove a file.

> Add screenshot of the Form details page with the "Form files" tab open and several files listed, plus the red "missing files" warning if any are required and absent.

## Missing-file detection

When you upload an ODK form, FormShare reads the form's XML and identifies every file the form **references** — every CSV named in a `select_one_from_file`, every dataset in a `pulldata()` call, every media file referenced by an `image::` or `audio::` column.

If any of the referenced files are not yet attached, FormShare:

* Shows a **red banner** on the form details page listing the names of the missing files.
* Marks the form as **incomplete** — you cannot test it on ODK Collect or [create a repository](README.md#create-a-repository) for it until the files are present.

Once you attach the missing files, the banner clears and the form proceeds.

## Upload a file

In the Form files tab, click the **+** button. A file picker opens.

* Pick the file you want to attach.
* If the file's name matches an existing one and you're updating it, choose to **overwrite** the previous version.
* For [longitudinal projects](../../data-management/longitudinal/), there is an extra checkbox to mark a CSV as the **real-time case CSV** — see "[The real-time CSV file](../../data-management/longitudinal/real-time-csv.md)" for what this means.

Click **Upload**. FormShare validates the file before storing it:

* **CSV files** must parse as valid CSV.
* **CSV files used as lookup datasets** must contain the columns the form expects (typically `name` and `label`, plus whatever additional columns the form references).
* **GeoJSON files** must parse as valid GeoJSON.
* Other formats are accepted without content validation.

If validation fails, FormShare shows the error and rejects the upload; the previous version of the file (if any) is unchanged.

## Case-sensitive filenames

{% hint style="warning" %}
**File names are case-sensitive.** If your ODK form references `Regions.csv` (capital R), you must upload `Regions.csv`. Uploading `regions.csv` (lowercase) will not satisfy the requirement, and the file appears as missing. This catches a class of bug that's silent on case-insensitive filesystems (Windows, macOS) but breaks at runtime on the server. Match the form's spelling exactly.
{% endhint %}

## Download a file

Click the file name in the list to download it. This is useful for retrieving the file the team is actually using — say, before adjusting it in a spreadsheet and re-uploading.

## Remove a file

Click and hold (or right-click) the file in the list. FormShare asks to confirm, then removes the file from the form. If the file was satisfying a "missing required file" check, the warning banner reappears until it is re-uploaded.

## How ODK Collect gets the files

ODK Collect doesn't pull form files directly. It pulls the **form manifest** — an XML document listing every file the form depends on, with the file's name, size, and MD5 hash. Collect then downloads any file whose hash differs from its local copy.

This is why FormShare cares about whether files are present and valid: a missing file in FormShare means Collect cannot prepare the form for a submission, and the enumerator's device errors out.

For [longitudinal projects](../../data-management/longitudinal/), the real-time case CSV is special: FormShare regenerates it on demand from the live case list and includes it in the manifest with a fresh hash whenever cases have changed. See "[The real-time CSV file](../../data-management/longitudinal/real-time-csv.md)".

## When to update a form file

* The lookup CSV needs a new option (a new region was added to the country, a new code was approved).
* A typo in a label needs fixing.
* The reference dataset (price list, roster) has been updated.
* A media file (an instructional photo embedded in the form) needs a higher-resolution version.

Upload the new version; check **Overwrite** so FormShare replaces the existing one. The next time ODK Collect syncs, the new file rolls out to every device.

## What's next

* "[Forms](README.md)" — the form lifecycle (upload, testing, repository).
* "[Project files](../project-files.md)" — the project-level equivalent for team documentation.
* "[The real-time CSV file](../../data-management/longitudinal/real-time-csv.md)" — for longitudinal projects, the auto-managed case lookup file.
