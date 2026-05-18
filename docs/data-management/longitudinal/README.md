---
description: How FormShare supports panel studies, cohort studies, and any other workflow where the same subject is surveyed many times.
---

# Longitudinal data collection

In a **longitudinal** project, the same subject — a household, a farmer, a patient, an animal, a plot of land — is visited and surveyed repeatedly over time. The dataset is not "one row per household" but "many rows per household, linked together so you can study them as a single trajectory".

FormShare supports this pattern natively with the concept of **cases**. A case is the long-lived identity of a subject (the household ID, the farmer ID, etc.). Each case is created once with a **case creator form**, and is then updated through **follow-up forms** at every subsequent visit. FormShare maintains a live list of the cases enumerators can visit — the **real-time CSV file** — and exposes it to ODK Collect so the field team always picks the right case at the right time.

## When you need it

You probably want a longitudinal project if any of these are true:

* You revisit the same subjects over time (baseline + endline; quarterly check-ins; weekly observations).
* The forms you collect are split by *what* you're measuring (registration, vitals, livelihoods, compliance) but always against the same subject.
* You need to enforce that a subject is registered before any follow-up data can be collected for it.
* You need to mark some subjects as "no longer in the panel" without losing their history.

If your project is a one-off cross-sectional survey — every submission is a distinct unit of analysis, you never revisit — you don't need longitudinal mode. Keep things simple with a standard project.

## The shape of a longitudinal project

A longitudinal project has three moving parts:

* **One case creator form**. The very first form you upload becomes the case creator: it registers new subjects into the project. The case creator form designates which variable is the **case ID** (the long-lived identifier), which is the **case label** (the human-readable display name), and which is the **case date** (when the case was registered). See "[Case creator forms](case-creator-forms.md)".
* **Any number of follow-up forms**. Every subsequent form added to the project attaches data to existing cases rather than creating new ones. Each follow-up form has a **case selector** field that the enumerator uses in ODK Collect to pick which case they are visiting. See "[Follow-up forms](follow-up-forms.md)".
* **The real-time CSV file**. FormShare automatically maintains a CSV with the currently active cases. ODK Collect downloads this file along with each follow-up form, so enumerators always see an up-to-date list. The file is regenerated whenever cases change. See "[The real-time CSV file](real-time-csv.md)".

In addition, follow-up forms can carry an **activation effect**: the same submission that adds data to a case can also mark it inactive (so it disappears from the picker) or reactivate a previously inactive case. See "[Activating / de-activating a case](activate-deactivate-case.md)".

## Turning the feature on

A longitudinal project is set up at creation time by ticking **Use a case/longitudinal workflow** on the "Add new project" page. Once any form has been added to the project, the setting becomes effectively read-only: you cannot convert a regular project to a longitudinal one or the other way around without starting over.

> Add screenshot of the "Add new project" form with the case/longitudinal checkbox highlighted.

{% hint style="warning" %}
**Plan this up front.** Whether a project is longitudinal is a structural decision, not a configuration knob. Once you upload the case creator form, the project's primary key shifts from "submission ID" to "case ID + visit date" semantics, and FormShare cannot retrofit a non-longitudinal project into a longitudinal one. If in doubt, start a new test project to try both modes before committing to your real project.
{% endhint %}

## What's next

* "[Case creator forms](case-creator-forms.md)" walks through uploading the first form and configuring the case fields.
* "[The real-time CSV file](real-time-csv.md)" explains how ODK Collect picks up the case list and how to choose which fields appear in the picker.
* "[Follow-up forms](follow-up-forms.md)" covers adding subsequent forms and wiring them to the case picker.
* "[Activating / de-activating a case](activate-deactivate-case.md)" describes how cases get marked active or inactive and what that means for follow-up data collection.
