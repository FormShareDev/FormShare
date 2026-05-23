---
description: Add, edit, and deactivate FormShare accounts when self-registration is disabled. Administrator-only feature for super users.
---

# Manage users

When self-registration is disabled on a FormShare instance, end users cannot create accounts on the login page. In that case, an administrator (a FormShare **super user**) creates accounts on their behalf — and uses the same screens to edit accounts, regenerate API credentials, deactivate users, and change passwords.

{% hint style="info" %}
**Who sees this feature?** Only users marked as **super user**. The link in the sidebar — **Manage users** (yellow icon under the dashboard entry) — only appears when (1) you are a super user, and (2) the instance is configured so that users cannot register themselves through the website. If you don't see the link, your instance is in a different mode and there is nothing for you to manage here.
{% endhint %}

> Add screenshot of the FormShare sidebar with the yellow "Manage users" entry highlighted, just below "Dashboard".

## Open the Manage users page

Click **Manage users** in the left sidebar.

The page is split into two cards:

* **FormShare users** — short heading with an **Add user** button on the right.
* **Search for a user** — a search box plus an **Edit user** button.

> Add screenshot of the Manage users page showing both cards (Add user button at top, search box below).

## Search for a user

The search box lets you find an existing account by **username**, **full name**, or **email address**. Start typing — results appear as you type, each with the user's avatar.

Select a result from the dropdown, then click **Edit user** to open that user's edit page.

> Add screenshot of the search dropdown showing two or three matching users with avatars next to their names.

## Add a user

From the Manage users page, click **Add user** (top-right of the first card). The "Add user" form opens.

> Add screenshot of the Add user form with every visible field labelled.

Fill in the fields:

* **User name** — the unique identifier this person will type at login. Only letters, digits, underscore (`_`), and dot (`.`) are allowed. **This cannot be changed after the account is created**, so choose carefully.
* **Full name** — the display name shown across the interface.
* **Email** — must be a valid address and must not already belong to another account.
* **Password** and **Password confirmation** — must match. Set something temporary; the user can change it later from [their own account page](your-account.md#change-your-password) (if password change is enabled on the instance) or you can change it for them from the edit screen.

Depending on how the instance is configured, additional fields may appear:

* **Roles** — when role-based permissions are enabled, you choose what the user is allowed to do. The two built-in roles are **Can create projects** and **Can upload forms**. Additional roles may be available depending on the instance configuration. Select all roles that apply.
* **Tenant** — on multi-tenant deployments, choose which tenant the user belongs to. You can only see this field if you yourself are in the **main** tenant; otherwise the user is automatically created in your tenant.
* **Is super user** — toggle on to grant this person the same administration capabilities you have (including access to Manage users). Use sparingly.

Click **Add user** to create the account. On success, you are returned to the Manage users page with a success message at the top.

{% hint style="warning" %}
**Common mistakes when adding a user**:

* The user name contains a space, hyphen, or other forbidden character → use only letters, digits, `_`, or `.`.
* The email is already in use → FormShare blocks duplicate emails so users have a single account per address.
* The two password fields don't match → re-type both carefully.

In all three cases, the form shows an error at the top of the page and no account is created.
{% endhint %}

## Edit a user

Search for the user on the Manage users page, then click **Edit user**. The edit screen has two cards stacked vertically: **Current information** at the top and **Change password** below.

> Add screenshot of the Edit user screen showing the Current information card with all fields and switches.

### Current information

* **User ID** — read-only. This is the username chosen when the account was created and it cannot be changed.
* **Full name**, **Email** — both can be edited. The email must remain unique across FormShare.
* **Roles**, **Tenant** — same fields as on the Add user form when those features are enabled on the instance.
* **Is super user** — toggle the user in or out of the super-user group.
* **Active** — toggle to deactivate or re-activate the account. A deactivated user cannot log in but their data and contributions are preserved. This is FormShare's way of "removing" a user — accounts are never deleted, they are deactivated.
* **API Key** and **API Secret** — read-only fields used by the user's [API access](your-account.md#your-api-key). Click the circular arrows next to a field to **generate a new value**. Generating a new key or secret immediately invalidates the user's existing access token, so they will need to log in again the next time they use the API.

Click **Modify user** to save your changes.

{% hint style="warning" %}
**Regenerating an API key or secret is disruptive**. Any external integration the user has set up using the previous credentials — Excel Add-in connections, custom scripts, BI tools — will stop working until the user updates them. Coordinate with the user before regenerating.
{% endhint %}

### Change password

The second card on the edit screen lets you set a new password for the user without knowing the old one.

> Add screenshot of the Change password card with the two password fields and the red "Change password" button.

Enter the **New password** twice (the second field confirms the first), then click **Change password**. The user can sign in with the new password immediately.

Use this when:

* A user has forgotten their password and the instance does not allow self-service password recovery.
* You need to revoke a user's access urgently — change the password, then deactivate the account from the **Current information** card above.

## Notes for administrators

* **There is no "delete user" action.** Accounts persist forever and are deactivated instead. This protects the integrity of audit logs, project ownership, and submission history. If you really need to remove a user from the interface, deactivate the account and remove them from each project's collaborator list.
* **Email and full name changes are visible immediately.** Other collaborators will see the new name as soon as they reload pages that show user lists.
* **Tenant moves are sensitive.** If your instance is multi-tenant and you move a user between tenants, double-check the user's project memberships afterwards.
* **All super-user actions on accounts are logged** to FormShare's server log. Password changes, API key regenerations, and API secret regenerations each produce a warning-level log line naming the administrator who made the change.
