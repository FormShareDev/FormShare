---
description: How to configure ODK Collect on an Android device to talk to a FormShare project — using the QR code, or manually with assistant credentials.
---

# Setting up ODK Collect

[ODK Collect](https://docs.getodk.org/collect-intro/) is the Android app enumerators use to fill in forms and submit data to FormShare. This guide walks through configuring a new device the first time, both with the project's QR code (the fast way) and manually (when the QR code is not accessible).

Before you start, you need:

* An Android device with [ODK Collect](https://play.google.com/store/apps/details?id=org.odk.collect.android) installed.
* A FormShare project with at least one form uploaded — see "[Creating your first project](creating-your-first-project.md)" and "[Uploading your first ODK Form](creating-your-first-task/)".
* At least one [assistant](../fundamentals/tasks/) created in the project and **assigned to the form**. The assistant's username and password are the credentials the device will use to sign in.

## Method A — QR code

The fastest way is to let ODK Collect read the project's QR code. It encodes the server URL, your project's preferred Collect settings, and the project's branding (icon and color) in one scan.

### Find the QR code

In FormShare, open the **Project details** page of the project. The QR code is in the main panel of the page, captioned "Use this QR code to configure ODK Collect".

<!-- TODO screenshot: the project details page highlighting the QR code panel. -->

If you need to tweak the encoded Collect settings before sharing, click **Edit QR data** below the code. The "Project QR" page lets you change form update behavior, auto-send, video resolution, image size, and other Collect preferences for everyone scanning the QR from now on (see "[Collect settings the QR controls](#collect-settings-the-qr-controls)" below).

### Scan the QR

On the device, open ODK Collect and tap the three-dot menu in the top right, then **Configure via QR code**. Point the camera at the FormShare QR code on the project details page. ODK Collect reads it, applies the settings, and returns you to the home screen.

The device is now talking to FormShare — but it still needs credentials to actually pull the form list.

### Sign in with the assistant credentials

From ODK Collect's main screen, tap **Get Blank Form**. Collect asks for a username and password the first time. Enter the **assistant's username and password** that you created and assigned to the form.

ODK Collect retrieves the list of forms that assistant is assigned to. Tap any to download it; the form is now ready to fill in.

## Method B — Manual configuration

When the QR code isn't usable — air-gapped device, broken camera, you're talking the user through it over a phone — configure Collect by hand:

1. In ODK Collect, tap the three-dot menu → **General Settings** → **Server**.
2. **Type**: ODK.
3. **URL**: Copy from the project details page in FormShare. The page shows the URL alongside the QR code with a copy-to-clipboard button.
4. **Username**: The assistant's username.
5. **Password**: The assistant's password.

Save the settings and return to the main screen. **Get Blank Form** now connects to FormShare and lists the forms the assistant can collect.

## What the assistant credentials do

Every submission lands in FormShare attributed to **whichever assistant sent it**, based on the credentials configured in ODK Collect. The assistant identity is the foundation of:

* **Audit trail**: every submission, every clean, every disregard is logged against the assistant who took the action.
* **Permission control**: an assistant can only see and submit to forms they are assigned to. Assistants not assigned to a form don't see it in their form list.
* **Multiple devices**: it's fine for one assistant to be signed in on several devices simultaneously — submissions from all of them are attributed to the same identity.

If a device changes hands (an enumerator leaves the project), update the assistant's [password](../fundamentals/tasks/#edit-an-assistant) in FormShare so the old credentials stop working.

## Collect settings the QR controls

The QR encodes a number of Collect preferences in addition to the server URL. From the **Edit QR data** page in FormShare, you can adjust:

* **Form update mode**: how Collect detects new versions of a form (manual / previously-downloaded / match-exactly).
* **Periodic updates check**: how often Collect should look for new form versions (every 15 minutes / 1 hour / 6 hours / 24 hours).
* **Auto-send**: whether and when Collect submits finalized forms without asking (off / WiFi only / cellular only / both).
* **Delete after send**: whether to delete the finalized form from the device after a successful submission.
* **Video resolution**: high or standard for forms that record video.
* **Image size**: original / very small / small / medium / large for forms that take photos.
* **External app recording**: allow Collect to delegate audio recording to another app.
* **Navigation**: how enumerators move between questions (swipe / buttons / both).
* **Project branding**: a project name, icon (emoji), and color, which Collect shows in its project switcher.

Changes to these settings take effect for **new** scans of the QR code; devices already configured do not pull updates automatically.

{% hint style="info" %}
**Re-scan after changing QR settings**: If you update the QR settings to, say, change auto-send to WiFi-only, devices that already scanned the old QR keep their old setting. Either ask enumerators to scan the new QR, or instruct them to change the setting manually in ODK Collect's general settings.
{% endhint %}

## Crowdsourcing (anonymous submissions)

If your project has the **Requires authentication to accept data** setting unchecked (see "[Creating your first project](creating-your-first-project.md)"), enumerators do not need to enter a username or password. Anyone with the project URL can pull the form list and submit. Use this for public-facing crowdsourcing — feedback forms, citizen-science reporting, public-health symptom tracking.

Be aware that anonymous submissions are still attributed to a special "anonymous" identity in the audit log; you lose the ability to distinguish individual contributors.

## Troubleshooting

* **"Authentication required" loop**: The credentials are wrong, or the assistant is deactivated. Verify the username (it's the assistant ID, not the email), reset the password if needed, and ensure the assistant is **active**.
* **Forms don't appear in Get Blank Form**: The assistant exists but is not **assigned** to any form. Edit the form in FormShare and add the assistant.
* **Form downloads but doesn't open**: A form file is missing. Check the [Form files](../fundamentals/forms/form-files.md) tab in FormShare and upload anything the form references.
* **Submissions fail to send**: The form may have been deactivated, or the device is offline. Check the form's active state in FormShare and the device's network connection.

## What's next

* "[Test your form](creating-your-first-task/test-your-form.md)" — the testing-stage walkthrough.
* "[Assistants](../fundamentals/tasks/)" — managing the accounts ODK Collect signs in with.
* "[Submissions](../fundamentals/submissions/)" — what happens to data after it leaves the device.
