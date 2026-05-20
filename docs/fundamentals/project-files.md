---
description: Project files are supporting documents — data management plans, policies, briefing notes — attached at the project level and shared with everyone collaborating on the project.
---

# Project files

A **project file** is any file you attach at the project level for the benefit of the people collaborating on it. The most common examples are PDFs of data management plans, ethics protocols, field manuals, and code lists, but FormShare does not restrict the file type — you can attach any file you would normally share with a teammate.

Project files are different from [form files](forms/form-files.md): project files are governance and documentation, available to every collaborator on the project; form files are operational data (CSVs and media) that ODK Collect needs to render and submit a specific form.

## Where to find them

On the **Project details** page, scroll to the **Project files** section. The list shows every file currently attached to the project, with a download link and (if you have edit access) controls to upload a new file or remove an existing one.

> Add screenshot of the Project files section on the project details page with a few PDF files listed.

## Upload a file

Click the **+** button in the Project files section. A file picker appears.

* Pick the file you want to attach.
* Choose whether to overwrite an existing file with the same name.

Click **Upload**. The file is stored in FormShare's centralized storage and added to the project. The new file appears in the list as soon as the upload completes.

## Download a file

Click the file's name in the list. FormShare serves the file with its original filename and content type, so the file opens in the right application on the user's machine.

Any collaborator on the project — owner, administrator, editor, or read-only member — can download project files. Partners do not see them; project files are intended for the internal team.

## Remove a file

Click and hold (or right-click, depending on the device) on the file in the list. FormShare offers to remove it; on confirmation, the file is unlinked from the project. Only collaborators with editor-or-above access can remove files.

{% hint style="info" %}
**Storage vs. unlink**: When you remove a project file through the UI, FormShare unlinks it from the project but the underlying storage entry is preserved for audit purposes — consistent with FormShare's approach of never physically deleting data. The file stops being accessible through the project but is not erased from the disk.
{% endhint %}

## What project files are good for

* **Data management plans** — the document you submitted to the ethics board, kept alongside the project itself.
* **Field manuals and SOPs** — the procedures enumerators were trained on. Useful for onboarding new collaborators.
* **Policies and consent forms** — copies of the consent text shown to participants, ethics approvals, sharing agreements.
* **Code lists and codebooks** — for reference, when the form itself doesn't carry the labels you want for analysis.
* **Pre-analysis plans, statistical analysis plans** — the things you committed to do before the data started flowing.

## What project files are *not* good for

* **Files ODK Collect needs at submission time** — those are [form files](forms/form-files.md), which are served to Collect through the manifest. Project files are not exposed to ODK clients.
* **Large media archives** — for exporting the photos and audio that submissions produced, use the [Media export](../data-management/data-products/media.md). Project files are for documentation, not data.
* **Sensitive credentials** — API keys, passwords, server credentials. Anything readable by every collaborator should not be a secret.

## What's next

* "[Form files](forms/form-files.md)" — the form-level equivalent for files ODK Collect needs.
* "[Projects](projects.md)" — the project model overall.
