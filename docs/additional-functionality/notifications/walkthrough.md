---
description: >-
  Turn on a project's notifications, tell the whole field team about a
  training, tell one group what to check, announce a new version of a form,
  see who has read it, and remind everyone every Friday to send their work.
---

# Walkthrough: notify a field team

The example project, *Early childhood teachers*, has two field officers and a supervisor, all with FormShare Collect on their tablets. The supervisor is in the assistant group *Supervisors*. In this walkthrough you send them three notifications and set a weekly reminder.

To send notifications when someone reaches a stage of a journey, follow "[Notifications in a journey](walkthrough-journey.md)" instead.

{% stepper %}
{% step %}
## Turn the project's notifications on

On the project's page, click **Notifications**, beside the forms. The button appears once the project has a form. Open the **Settings** tab.

<figure><img src="../../.gitbook/assets/notif-settings-off.png" alt="The Settings tab with notifications off: FormShare Collect shows no bell for this project, and the button Turn notifications on."><figcaption><p>A project starts with notifications off.</p></figcaption></figure>

Click **Turn notifications on**. The badge at the right of the tabs turns from **Off** to **On**.

<figure><img src="../../.gitbook/assets/notif-settings-on.png" alt="The Settings tab with notifications on, explaining that turning them off takes the bell away at each phone's next check and deletes nothing, and the button Turn notifications off."><figcaption><p>Turning them off later deletes nothing. Turned on again, what is still current comes back.</p></figcaption></figure>

The bell appears on each phone with FormShare Collect at its next check, which can take up to a day. Turn notifications on before the day you need them.
{% endstep %}

{% step %}
## Tell everyone about the training

Open the **Send** tab and fill it in:

| Field | Value |
| --- | --- |
| Title | `Training on Friday` |
| Text | `All field officers meet at the district office on Friday at 9:00. Bring your tablet charged.` |
| Kind | A message |
| Form | None |
| Shown until the end of | *(leave empty)* |
| To | Everyone in the project |

<figure><img src="../../.gitbook/assets/notif-send.png" alt="The Send tab filled in: the title Training on Friday, the text, Kind A message, Form None, no last day, and To Everyone in the project."><figcaption><p>Phones may show a notification on their lock screen. Write nothing personal or sensitive in it.</p></figcaption></figure>

Click **Send**. Each phone gets it at its next check: within minutes while FormShare Collect is in use, and later otherwise. It shows as the phone's own notification and under the bell.
{% endstep %}

{% step %}
## Tell the supervisors what to check

Open **Send** again. Title it `Check this week's interviews`, write `Look over the interviews sent this week before Friday's meeting.`, and under **To** choose **Only some**, then the group *Supervisors*.

FormShare reads who is in the group each time a phone checks. Someone who joins *Supervisors* next week still gets this notification, and someone who leaves the group loses it.
{% endstep %}

{% step %}
## Announce a new version of a form

When you publish a new version of the teacher interview, tell the officers to get it. Open **Send**, title it `New version of the teacher interview`, write `Get blank forms to update the interview before your next visit.`, choose the kind **A new version of a form** and the form *Teacher interview*, and send it to everyone.

On the phone, the notification has a **Download** button, which opens the download screen with the interview ticked.
{% endstep %}

{% step %}
## See who has read it

The **Sent** tab lists what was sent, newest first, to whom, and how many have read it.

<figure><img src="../../.gitbook/assets/notif-sent.png" alt="The Sent tab: New version of the teacher interview to everyone, read by 1; Check this week's interviews to the group Supervisors, read by 1; Training on Friday to everyone, read by 2."><figcaption><p>The number under <strong>Read by</strong> counts assistants, whatever the device. In a crowdsourcing project it counts devices.</p></figcaption></figure>

Click a title to open the notification. Its **Message** tab changes the title, the text or the last day: phones that have it replace it at their next check, without announcing it again. **Withdraw it**, at the bottom, takes it off every phone at its next check, read or not.

<figure><img src="../../.gitbook/assets/notif-message.png" alt="The Message tab of Training on Friday: kind, recipients and sending time, the title and text to change, the last day, Save the changes, and Withdraw it."><figcaption><p>A notification's page.</p></figcaption></figure>

Its **Read by** tab says who read it, on which device, and when.

<figure><img src="../../.gitbook/assets/notif-read.png" alt="The Read by tab of Training on Friday: read by 2 assistants, the Field supervisor and the Kampala field officer, each with the device and the time."><figcaption><p>A phone counts a notification read when it is opened, its button is used, it is tapped, everything is marked read, or it is deleted unread.</p></figcaption></figure>
{% endstep %}

{% step %}
## Remind everyone every Friday

Open **Reminders** and click **Add a reminder**:

| Field | Value |
| --- | --- |
| Title | `Send this week's interviews` |
| Text | `Send the interviews you finished this week before the weekend.` |
| Form | Teacher interview |
| When | Every week, on Friday |
| At | 16:00 |
| From | today, with no last day |
| To | Everyone in the project |

<figure><img src="../../.gitbook/assets/notif-reminder-add.png" alt="A new reminder: Send this week's interviews, its text, the form Teacher interview, every week on Friday at 4:00 PM from today, to everyone in the project."><figcaption><p>The time is on each phone's own clock: 16:00 is four in the afternoon wherever the phone is.</p></figcaption></figure>

Click **Save the reminder**. The **Reminders** tab shows when it comes next.

<figure><img src="../../.gitbook/assets/notif-reminders.png" alt="The Reminders tab: Send this week's interviews, every Friday at 16:00, to everyone in the project, next time 2026-11-27 16:00."><figcaption><p>Phones take the next few times ahead, so a reminder comes on time even offline.</p></figcaption></figure>
{% endstep %}

{% step %}
## Check which phones are listening

The **Devices** tab lists every phone that has asked for this project's notifications, with the assistant signed in on it and when it first and last checked.

<figure><img src="../../.gitbook/assets/notif-devices.png" alt="The Devices tab: three devices, those of the Field supervisor, the Wakiso field officer and the Kampala field officer, each with its first and last check."><figcaption><p>Only FormShare Collect asks, and only while the project's notifications are on.</p></figcaption></figure>

A field officer missing from this list does not get notifications. Their phone may run another app, such as ODK Collect, which shows nothing, or it may not have checked since you turned notifications on. See "[When a notification does not arrive](README.md#when-a-notification-does-not-arrive)".
{% endstep %}
{% endstepper %}

## What's next

* Read "[Sending notifications](sending-notifications.md)" for every field and the limits on what you can send.
* Read "[Calendar reminders](reminders.md)" for the other schedules.
