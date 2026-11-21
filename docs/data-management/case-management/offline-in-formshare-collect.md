---
description: >-
  FormShare Collect registers cases and follows them up without a connection.
  It gives each new case its identity on the device, keeps the cases it
  registered until they are sent, and runs the form's actions on them at
  finalize.
---

# Offline in FormShare Collect

In ODK Collect and Enketo, case management works online. A published list is an ordinary form attachment, refreshed when the device checks for form updates, and the server checks every follow-up and runs the [actions](case-actions.md) when the submission arrives. A case registered this morning reaches another form's list only after its submission reaches the server and the device updates its forms.

FormShare Collect also works offline. A device can register a school and its teachers in the morning and interview one of those teachers in the afternoon, with no connection in between.

## What the server sends

Once a project publishes its first list, every form of the project carries a few more files: a description of its repository, the lists the form takes part in, its properties and its actions. FormShare Collect uses them to keep its own copy of the cases it registers and to run the actions. ODK Collect and Enketo download them like any form file and do not use them.

Those files change whenever the form's repository, lists, properties or actions do, so a device that checks for updates gets them with the form.

## An identity made on the device

FormShare Collect gives each new row its identity, its [rowuuid](how-cases-are-identified.md), when the form is filled in: the main row and each repeat entry. The server keeps that identity rather than making one. So a case registered offline already has the identity its follow-ups will store, and a submission sent twice never becomes two rows.

Other clients leave the identity to the server, which makes one when it stores the row.

## Cases registered on the device

A case registered on the device can be selected at once in the follow-up forms that use its list, before it is sent. The device applies the list's own [filter](list-filters.md) to it, so a case the filter leaves out does not appear.

The device keeps such a case in its lists until the submission is sent, whatever the list's choice of [who receives which cases](who-receives-which-cases.md): the enumerator always sees what they just registered. After the send, the case comes back through the list only if the list serves it to that assistant.

## Actions at finalize

When a follow-up is finalized, FormShare Collect runs the form's actions if its case was registered on the same device and has not been sent yet. The enumerator sees the effect at once: a teacher deactivated by the interview leaves the list on that device, and a property the interview sets shows its new value in the lists that serve it.

For a case the device received in a list, the device runs no action. The server runs the actions when the submission arrives, and the device sees the effect at its next update.

If an action fails on the device, nothing of that run is applied there, and the submission is kept and sent as usual. The run that counts is the server's, which the **Runs** table of the form's actions records.

A *days since* condition counts by the phone's own date on the device, and by the server's on the server. Near midnight, a phone in another time zone can count one day more or less than the server.

## Sending

The server refuses a follow-up whose case it does not have, so a registration must reach the server before its follow-ups. A follow-up that arrives first goes to the [error log](../cleaning/submissions-with-errors.md), like any follow-up whose case is missing.

When the server stores a submission, it runs the form's actions itself, over the case as the server holds it, and that run is the one recorded.

## What a device learns only at its next update

* A case deactivated by a follow-up sent from another device stays selectable on this one until its next update. A further follow-up on it is refused by the server and goes to the error log.
* A case a list's filter drops, or one given to another assistant, stays selectable too. The server still takes its follow-up.

{% hint style="info" %}
**Offline case management is a feature of FormShare Collect.** It needs a version of the app with case management. ODK Collect and Enketo work with the same forms and lists online.
{% endhint %}

## What's next

* See what actions can do in "[Form actions](case-actions.md)".
* See how each assistant's list is chosen in "[Who receives which cases](who-receives-which-cases.md)".
