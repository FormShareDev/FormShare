---
description: WorkSpace lets you group related projects under one umbrella so a team can share access and management responsibilities at a higher level than individual projects.
---

# WorkSpace

**WorkSpace** is an organisational layer above projects. Where a single FormShare project is the right unit for a single data-collection effort (one survey, one form set), a workspace is the right unit for a portfolio of related projects — a country office's research portfolio, a long-running programme with many surveys, a research consortium's joint operations.

Workspaces let you grant access at the portfolio level, so adding a new analyst to "Kenya Operations" automatically gives them access to every project under that umbrella, rather than inviting them to each project one by one.

## Where to find it

A **WorkSpaces** entry appears in the main navigation. Click it to open the workspaces list at `/user/<your-username>/workspaces`. You'll see every workspace you own or have been added to as a collaborator.

> Add screenshot of the workspaces list with two or three workspaces visible.

If you have no workspaces yet, the page invites you to create your first one with a **Create a new workspace** button.

## Create a workspace

Click **Add workspace**. The "Add workspace" form appears with two fields:

* **Workspace ID** — a short identifier (letters, digits, underscores). Must be unique across your account and cannot be changed later.
* **Workspace name** — the display name. Free text.

Click **Add workspace**. You are now the workspace owner. The workspace is empty — you populate it by creating projects inside.

## Add projects to a workspace

Open the workspace and click **Go to projects**. This is the standard project-creation flow ("[Creating your first project](../guides/creating-your-first-project.md)"), but the new project is attached to this workspace instead of being a free-standing project on your account.

From inside a workspace you can:

* See the list of projects in the workspace with their codes, names, icons, and colours.
* Open any project to drill into its forms, submissions, and the rest of the standard FormShare features.
* Edit project metadata or delete projects.

## Invite collaborators to a workspace

A workspace has its own collaborator list separate from its projects'. From the workspace, click **Edit collaborators**.

> Add screenshot of the workspace collaborators page with the search field and the existing collaborator list.

* Search for users by username, full name, or email.
* Pick the user and click **Add collaborator**.
* Assign a role — **Administrator** or **Editor** — that applies across the whole workspace.

Roles inside a workspace:

* **Owner** — the user who created the workspace. Owners have full control and the role cannot be transferred.
* **Administrator** — can create, edit, and delete projects in the workspace; can invite and remove collaborators.
* **Editor** — can create, edit, and delete projects, but cannot manage the workspace's collaborator list.

Workspace collaborators automatically have the corresponding access to every project inside the workspace — no per-project invitation needed.

## Edit and delete

* **Edit workspace** lets you change the workspace name. The ID is immutable.
* **Delete workspace** removes the workspace and, by extension, every project inside it (and their forms, repositories, and submissions). This is irreversible — generate private [data products](../data-management/data-products/) for anything you want to keep first.

## Permissions

* Any user with workspace permissions on the FormShare instance can create workspaces. Whether you have that permission depends on how the administrator has configured your account.
* Only the **owner** and **administrators** can add or remove workspace collaborators.
* Workspace **editors** can manage projects inside the workspace but cannot change workspace membership.
* Assistants and partners are not workspace collaborators — they operate at the project / form level, not at the workspace level.

## When to use workspaces

* You manage a portfolio of related projects and want one place to see them all.
* Your team handles many projects and you want collaborators to share access to a whole portfolio at once.
* You want to keep operational areas separate (e.g., one workspace per country office).

## When *not* to use workspaces

* You only run one or two projects total. Workspaces add overhead without benefit at small scale.
* The projects you'd group together have completely different teams. Sharing a workspace means sharing access — if the teams shouldn't see each other's data, keep the projects separate.

## What's next

* "[Projects](../fundamentals/projects.md)" — the unit of work inside a workspace.
* "[Collaborators](../fundamentals/members.md)" — the per-project collaborator model.
