---
description: Manage your FormShare account — view your profile, edit your details, change your password, and generate the API key and secret used for programmatic access.
---

# Your account

This page covers what a signed-in FormShare user can do to manage their own account: view the profile, edit details, change the password, regenerate the API key, and choose a default time zone. It complements "[Registering and logging in](../guides/create-an-account-login.md)", which covers sign-up, sign-in, and password recovery.

{% hint style="info" %}
**Different from assistants and partners**. This page is for FormShare account holders (the users who create projects, upload forms, and manage data). [Assistants](tasks/) and [partners](partners.md) have their own dedicated profile pages inside the Assistant Access and Partner Access portals — they cannot use the routes described here.
{% endhint %}

## View your profile

Click your name at the top right of any FormShare page to open the menu, then click **Profile**. The profile page appears at `/user/<your-username>/profile`.

> Add screenshot of the profile page showing the bio, joined date, project count, and the API key with copy-to-clipboard button.

The profile page shows:

* Your **user name** and your **full name**.
* Your **bio** — a free-form Markdown description of yourself. Useful when you collaborate across projects so other users can see who you are.
* The **date you joined** FormShare.
* Your **time zone** (when time zones are enabled on the instance).
* Your **API key** — readable with a copy-to-clipboard button. See [Your API key](#your-api-key) below.
* A summary of your activity: number of projects, number of forms, last project activity, and number of collaborators.

A tabbed area at the bottom of the page lists your projects and the projects you collaborate on.

The profile page is read-only at a glance — click **Edit** to make changes.

## Edit your profile

From the profile page, click **Edit**. The profile-edit page appears at `/user/<your-username>/profile/edit`.

> Add screenshot of the profile-edit page with the three sections (Profile, API keys, Password) visible.

The page is organised as a single form with three sections — Profile, API keys, and Password. You can submit any section independently with its **Save** button.

### Profile section

* **Full name**: Your display name. Whether this is editable depends on how the FormShare instance is configured; on some instances the administrator locks the name so it can only be changed centrally.
* **Bio**: Markdown-formatted free text. There is a toolbar above the textarea for the common formatting actions.
* **Time zone**: Pick the time zone you operate in. Time zones appear in the dropdown only if the FormShare instance has them enabled. Once set, FormShare displays all dates and times throughout the application in your chosen zone.

Click **Save changes** in the Profile section to apply.

## Change your password

The third section of the profile-edit page is **Change password**. To change it, you need to enter:

* **Current password** — to confirm it is really you.
* **New password** — what you want it to be from now on.
* **Confirm new password** — typed again to catch typos.

Click **Change password** in the Password section. FormShare verifies the current password, updates the stored value, and **signs you out**. Sign back in with the new password to continue.

The password section will not appear if your FormShare instance has been configured to disallow self-service password changes — in that case, ask your administrator to reset it for you.

{% hint style="info" %}
**Lost the password?** If you cannot sign in to change it, use the "[Forgot password?](../guides/create-an-account-login.md#forgot-your-password)" link on the sign-in page instead. That flow does not require you to know the current password.
{% endhint %}

## Your API key

The second section of the profile-edit page is **API keys**. You have two credentials here:

* **API key** — a UUID-style identifier. Programmatic clients send this on every request.
* **API secret** — a hex token. Used as a shared secret alongside the key by FormShare features that require it.

> Add screenshot of the API keys section showing the read-only API key field, the read-only API secret field, and the two "Generate new" buttons.

Both fields are read-only. To produce a new value, click **Generate new key** or **Generate new secret**. The new value appears in the field; click **Save API key** to persist it.

{% hint style="warning" %}
**Save the API secret on first generation.** The secret is stored encrypted on the server. When you reload the page, the secret field is empty — only the API key remains visible. If you forget to copy the secret to your password manager when you generate it, you'll need to regenerate it (which invalidates the previous value).
{% endhint %}

### What is the API key for?

Your API key authenticates you when calling FormShare's HTTP endpoints from outside the web interface — for example, when fetching data products programmatically or hitting any private endpoint as yourself. The [data-cleaning API](../data-management/cleaning/in-a-repository/api.md) for editing values inside a repository uses a **separate** key belonging to an [assistant](tasks/), not your user key — your key alone cannot edit submission data.

If you suspect your key has leaked, click **Generate new key**, save the new value, and update any script or integration that was using the old one. The old key stops working as soon as the new one is saved.

## Profile picture

FormShare does not let you upload a profile picture. Instead, every user gets an avatar **automatically generated from their full name**. The avatar is deterministic: the same name produces the same image every time, in a consistent visual style across the application.

If you change your full name through the profile edit page, your avatar will change to match.

## What you cannot do here

A few things are intentionally **not** self-service:

* **Two-factor authentication** is not shipped in core FormShare. If you need it, ask your administrator whether a plugin is available on your instance.
* **Account deletion** is not available through the UI. If you want your account removed, ask the administrator of the FormShare instance.
* **Email change** is not part of the profile edit page in core. The email you registered with is the email FormShare uses; to change it, contact the administrator.
* **Username (user name)** cannot be changed. It was set at registration and is the durable identifier across audit logs, URLs, and collaborator lists.

## What's next

* "[Registering and logging in](../guides/create-an-account-login.md)" — sign-up, sign-in, and password recovery.
* "[Assistants](tasks/)" — how assistant accounts work (different from your own account).
* "[API data cleaning](../data-management/cleaning/in-a-repository/api.md)" — using API keys to edit data programmatically (note: uses *assistant* API keys, not yours).
