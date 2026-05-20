---
description: Enketo turns any FormShare form into a web-based form respondents can fill in from a browser — for online surveys, public crowdsourcing, supervised data entry, or quick previews.
---

# Enketo

**Enketo** lets respondents fill in your ODK forms directly in a web browser, without installing ODK Collect. It's the right tool when your respondents are on desktops, when you're running an online survey, when you need a public link anyone can use, or when you want to preview the form without a device.

Enketo and ODK Collect can be used at the same time on the same form — submissions from both end up in the same FormShare repository, treated identically.

## Where to find it

Open any form's details page and click the **Links** tab. A section labeled **Web browser data collection** appears with:

* A **mode selector** (the dropdown that picks which kind of Enketo link you want).
* A **Copy** button (clipboard icon) to copy the selected URL.
* A **Play** button (arrow icon) to open the form immediately in your browser for a test.
* An **Edit thank you page** link for customising what respondents see after submitting.
* A **Re-generate URLs** button for refreshing the links if you ever need to.

> Add screenshot of the form details "Links" tab showing the web browser data collection section with the mode dropdown open.

If you haven't activated Enketo yet, the section shows a **Generate the URLs** button — click once and FormShare prepares the links.

## Five submission modes

The mode dropdown lets you pick how respondents interact with the form. Each mode produces a different URL:

* **Online-Only (multiple submissions)** — Respondents fill and submit through the browser. The form resets after submit and they can do it again. Use for repeated submissions and continuous data collection.
* **Online-Offline (multiple submissions)** — Same as above, but Enketo caches the form so respondents can fill it offline and submit when they're back online. Use for field work where connectivity is unreliable.
* **Online-Only (single submission)** — Respondents fill once. The form resets after they submit, but the same browser can submit again. Use when most respondents will submit once but you don't need to enforce it.
* **Online-only (once per respondent)** — Each respondent can submit exactly once. Attempts to submit again show an error. Use for one-shot surveys, voting, or anywhere duplicate submissions would harm the dataset.
* **View only (for testing)** — A read-only preview. Submission is disabled. Share this with stakeholders to review the form's design without risking test data in the repository.

Pick the mode that matches your use case, then **Copy** the URL and share it.

## How respondents fill the form

For a respondent, the workflow is:

1. Open the link you sent them.
2. Fill in the form in the browser. Every ODK question type is supported — text, numbers, single- and multi-select, dates, times, GPS, photos, audio, video, cascading selects, conditional logic.
3. Submit.
4. See the customised thank-you message.

For respondents on mobile devices, the form is responsive — it works on phones and tablets as well as on desktops. Most modern browsers (Chrome, Firefox, Safari, Edge) are supported.

## Customising the thank-you page

After a respondent submits, they see a thank-you page. Click **Edit thank you page** to customise it:

* Rich-text editor (bold, italics, links, images, lists).
* Image upload — embed a logo or completion icon.
* Per-language customisation — if the form has multiple languages, you can write a thank-you message in each language; respondents see the one matching the language they used.

> Add screenshot of the thank-you page editor with formatting toolbar and a sample message.

This is a small detail with outsized impact — a personalised thank-you message reads as far more professional than the default "Thank you" and is the right place to provide instructions ("Watch your inbox in 24 hours for results", "Contact us at …").

## How submissions reach FormShare

Enketo submissions follow exactly the same path as ODK Collect submissions:

* They are checked against the form's structure (primary key uniqueness, required fields, lookup validity) at submission time.
* Valid submissions enter the form's repository.
* Invalid submissions land in the [error log](../data-management/cleaning/submissions-with-errors.md) for an assistant to handle.
* Media files (photos, audio, video) attached through the browser are stored alongside the data, the same as media from ODK Collect.
* The audit log treats Enketo submissions like any other — every change is attributed and timestamped.

You can mix and match Enketo and ODK Collect on the same form indefinitely. Many projects do — Enketo for online respondents, ODK Collect for enumerator-led face-to-face interviews.

## Permissions

* **Project owners, collaborators, and assistants** can generate URLs, view them, and customise the thank-you page.
* **Respondents** do not need a FormShare account — they just need the URL. This is what makes Enketo useful for crowdsourcing and online surveys.
* **Partners** do not have access to Enketo URLs by default.

If your form's project is configured to require authentication (the "Requires authentication to accept data" project setting), the Enketo URL still works but only for authenticated assistants — the URL itself doesn't bypass FormShare's project-level auth setting. For truly public surveys, ensure the project allows anonymous submissions.

## What Enketo can't do

* **Forms that require ODK Collect's hardware integration** (specific Bluetooth sensors, NFC readers) won't work in a browser.
* **Very long forms with hundreds of cascading questions** can be slow in some browsers — test before sending widely.
* **No batch import** through Enketo — it's a per-submission interface. For bulk loads, use [Import external data](../fundamentals/submissions/import-external-data.md).

## What's next

* "[Forms](../fundamentals/forms/)" — the form lifecycle ODK Collect and Enketo share.
* "[Setting up ODK Collect](../guides/odk-collect-setup.md)" — when the same form needs to work on mobile devices.
* "[Submissions](../fundamentals/submissions/)" — what happens to data once it arrives.
