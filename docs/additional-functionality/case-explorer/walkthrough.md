---
description: >-
  See which teachers have been interviewed and when, read one teacher's
  history, and give a school's teachers to the supervisor while their field
  officer is away.
---

# Walkthrough: follow up teachers and share them out

This walkthrough uses the project built in "[Walkthrough: schools and teachers](../../data-management/case-management/walkthrough.md)": eight schools registered with their teachers, `teacher_list.csv` published from the repeat of teachers, and a teacher interview whose case link is that list. The interview has an [action](../../data-management/case-management/case-actions.md) that marks each teacher interviewed and records their experience in bands, as in "[Update cases with actions](../../data-management/case-management/walkthrough-actions.md)".

The field officers have been interviewing teachers for five weeks. You want to see where they stand, and the Wakiso field officer is about to go on leave: the supervisor will take over one of their schools.

{% stepper %}
{% step %}
## Serve each officer the teachers assigned to them

On **Published lists**, click **Edit** on `teacher_list.csv`, open the **Case accessibility** tab, choose **Each assistant receives the cases assigned to them** and click **Change**.

Each officer's device now receives the teachers of the schools that officer registered. The rest of this walkthrough changes that for one school. See "[Who receives which cases](../../data-management/case-management/who-receives-which-cases.md)" for the other choices.
{% endstep %}

{% step %}
## Open the Case Explorer

On the project's page, click **Case explorer** in the Forms panel.

<figure><img src="../../.gitbook/assets/case-explorer-button.png" alt="The Forms panel of the project page, with the Case explorer button under Published lists, Workflow diagram, Notifications, Journey and Add new form."><figcaption><p>The button appears once the project has a list of cases.</p></figcaption></figure>
{% endstep %}

{% step %}
## Choose the teachers, and the dates that matter

Under **Cases of**, choose `teacher_list.csv (school_registration / teachers)`.

Then tell the timeline which date places each form's submissions. Set **School registration** to **Form opened (start)**, the day the officer was at the school, and **Teacher interview** to **Date of the interview (interview_date)**, the date the officer recorded. Left at **Submission date**, a week of interviews sent together would land on one day.

Under **More columns**, type `experience` and choose **Years of experience, in bands (experience_band)**: the grid gains the property the interview's action writes.

<figure><img src="../../.gitbook/assets/case-explorer-grid.png" alt="The Case Explorer with teacher_list.csv chosen, School registration placed by Form opened and Teacher interview by Date of the interview, the experience band added under More columns, and the grid of ten teachers with their school, role, status, registration, assignee and experience band."><figcaption><p>Your browser remembers the dates and the added column for this list.</p></figcaption></figure>

The grid already answers a few questions: who has been interviewed (**status**), who registered each school and who now has its teachers (**Registered by**, **Assigned to**), and Brian Ssemakula's **Active** is 0, because his interview said he no longer works at his school.
{% endstep %}

{% step %}
## Read the timeline

Under the grid, each teacher on the page has a row: a green dot for the day their school was registered, a purple dot for each interview.

<figure><img src="../../.gitbook/assets/case-explorer-timeline.png" alt="The timeline of ten teachers: green dots for the schools' registrations between 11 and 14 October, and purple dots for interviews between 20 October and 19 November; Sarah Nakato, Joseph Okello and Henry Lubega have two each, Grace Achieng, Esther Namubiru, Florence Auma and Juliet Atim none."><figcaption><p>Four teachers on this page have not been interviewed yet.</p></figcaption></figure>

Grace Achieng, Esther Namubiru, Florence Auma and Juliet Atim have no purple dot: they are still waiting for their interview. Click a dot to open that submission. Use the pager under the grid to see the next ten teachers.
{% endstep %}

{% step %}
## Open a teacher's history

Click **Sarah Nakato** in the grid. Her history opens under the timeline.

<figure><img src="../../.gitbook/assets/case-explorer-history.png" alt="The history of Sarah Nakato: assigned to the Wakiso field officer; her registration with her school; an interview whose action set her experience band to 6 years or more and her status from pending to interviewed; and a second interview that changed nothing."><figcaption><p>The demo's submissions were all sent the same day, so their arrival times are close together; the timeline places them by the dates chosen above.</p></figcaption></figure>

The first interview's action set two of her properties; the second found them already set and changed nothing. Any edit in Clean data, or a reassignment, would show here too. See "[A case's history](case-history.md)".
{% endstep %}

{% step %}
## Give a school's teachers to the supervisor

Kasangati Community School is the Wakiso officer's. While they are away, the field supervisor will interview its teachers.

Click **Rose Nalwoga**, Kasangati's head teacher. At the top of her history, choose **Field supervisor (field_supervisor)** and click **Assign**.

<figure><img src="../../.gitbook/assets/case-explorer-assign.png" alt="The top of Rose Nalwoga's history: assigned to the Wakiso field officer, Field supervisor chosen, the Assign button, and the note that the 3 cases of this list her school's registration registered move together."><figcaption><p>The list offers only the assistants who receive the teacher list.</p></figcaption></figure>

The note says what happens to the rest of the school: a teacher is a row of the registration's repeat, so the three teachers that registration registered move together. You give the supervisor the whole school, which is what you wanted.
{% endstep %}

{% step %}
## Check the change

Rose's history now ends with the reassignment, by you, from `wakiso_officer` to `field_supervisor`, and the grid's **Assigned to** column says the same for her school's three teachers.

<figure><img src="../../.gitbook/assets/case-explorer-reassigned.png" alt="Rose Nalwoga's history after the change: assigned to the Field supervisor, and a new entry, Reassigned by Project Owner, assigned to changed from wakiso_officer to field_supervisor."><figcaption><p>Every reassignment is recorded, with who made it.</p></figcaption></figure>

At their next update of the forms, the supervisor's device receives Kasangati's teachers in `teacher_list.csv`, and the Wakiso officer's device no longer offers them. When the officer is back, give the school back the same way.
{% endstep %}
{% endstepper %}

## What's next

* Read "[The grid and the timeline](grid-and-timeline.md)" for every column and date choice.
* Read "[Reassigning cases](reassigning-cases.md)" for who can reassign and what devices see.
