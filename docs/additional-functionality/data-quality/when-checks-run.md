---
description: >-
  When the Data quality checks run: as a submission arrives, after an answer
  is changed, when a check is saved, on Run checks and at night. How runs over
  all records take turns, and what a run leaves out.
---

# When checks run

The checks run by themselves. A record is checked as it arrives and again whenever one of its answers changes, a saved check runs over every record, and the nightly run catches up with whatever changed during the day.

| When | What runs | How |
| --- | --- | --- |
| A submission enters the repository | The checks on records, on that record | In the background, moments later |
| An answer is changed in Clean data, through the cleaning API, by recoding "other" answers, or through the OData plugin (from the Excel Add-in, for example) | The checks on records, on that record | At once: the grid shows the new score when it reloads |
| A check is saved | That check, on all the records | In the background |
| Someone presses **Run checks** | All the checks, on all the records | In the background |
| Every night | All the checks, on all the records, of each form that changed | Started by the server |
| A dismissed flag of a check is reopened | The checks on records, on its record | At once |

A dismissal, a post of flags from a script, the reopening of a script's flag and the removal of a check score the records they touch again at once, without running any check. A reopened warning waits for the next run of its check over all records (see "[Reopening an issue](flags-and-scores.md#reopening-an-issue)").

## When a submission arrives

Only the checks on records run, on the record that arrived. The checks on the whole form wait for the next run over all records.

The checks that compare a record with the others are judged against every record: an outlier, a duplicate and a near-duplicate. A new duplicate also flags the record it repeats, and a new near-duplicate flags again the records it is near.

A submission that goes to the error log instead of the repository has no record to check. An import of external data, and a submission pushed into the repository from the error log, are not checked on arrival: the next run over all records checks them.

## After an answer is changed

The checks on records run again on the changed record as soon as the change is stored, before the page or the API answers. A flag the change fixed is marked as no longer found, and the grid of Clean data shows the new score when it reloads.

When the change names an issue in its reason, the issue records it (see "[Naming the issue in the reason](flags-and-scores.md#naming-the-issue-in-the-reason)").

When the changed record is a case that other forms follow up, the follow-ups of that case are checked again in the background, if they have [checks across forms](check-types.md#across-forms).

## When a check is saved

Saving a check, new or edited, runs that check over all the records in the background. The Data quality page says whether the run started, is waiting for its turn, or had nothing to do because nothing changed since a run that checked the same.

## Run checks

**Run checks**, in the Checks box of the Data quality page, runs every check of the form over all the records, the checks on the whole form included. The button shows once the form has a check.

After you press it, the page says what happened:

| Message | Meaning |
| --- | --- |
| The checks are running on all records | Your run started. |
| Your last run of the checks is still going: this one starts when it ends | You have a run going on this form already. |
| Someone from your organization has a run of the checks going: yours starts when it ends | Someone from your organization has a run going on this form, and the list of runs names them. |
| 2 runs of the checks are going on this form: yours starts when one of them ends | Every place for a run is taken, by other organizations or the nightly run. The number is how many runs the server allows at once; with one place, the message reads "A run of the checks is going on this form: yours starts when it ends". |
| Your run of the checks is waiting for its turn already: it checks every change made until it starts | You pressed again while your run waited. |
| Someone from your organization has a run of the checks waiting for its turn: it checks every change made until it starts, yours too | Your organization's waiting run covers your request. |
| A run of the checks going now covers every change: its results show when it ends | A run that started after the last change checks everything yours would. |
| Nothing changed since the checks last ran on all records: their results are current | No run was needed. |

Saving a check shows the same messages, worded for the saved check: "The check was saved and is running on all records", for example.

## How runs over all records take turns

A run over all records is a saved check, **Run checks** or the nightly run. Several can be asked for on one form, and they take turns.

* Up to two runs go at once on a form, each for a different organization. People of one organization take turns, and someone of another organization can run at the same time while a place is free. The nightly run counts as an organization of its own.
* An organization has at most one run waiting on a form. Pressing **Run checks**, or saving a check, while one waits joins it, and makes it a run of all the checks if the two differ. While it waits, **Run checks** is disabled, and its tooltip says whether the waiting run is yours or was asked for by someone from your organization.
* A waiting run checks every change made until it starts.
* A run is not started, and a waiting run is skipped when its turn comes, if a run that started after the last change already checked, or is checking, everything it would.
* The Checks box lists the runs going and waiting, who asked for each and what it waits for, and the page reloads by itself when one starts or ends.
* A run gives a sign of life every 30 seconds while it goes. If the server's worker stops, the run is taken for dead after two minutes without one: the next time someone opens the Data quality page, the open page checks on its runs, or another run ends, the run is marked as failed with the reason *It stopped before it finished: its worker may have stopped*, and the next run starts. However long a run takes, it is never taken for dead while its worker lives.


A change, for these rules, is a check saved, a submission arrived, or an answer edited or a submission deleted in the main table or in a table a check reads. A warning reopened counts too, for the run of its check, so that the run finds it again or marks it as no longer found. For a check across forms, a change to the case's repository counts too, and for **Cases without a follow-up** every new day does. The scores the plugin writes are not changes.

{% hint style="info" %}
**For server administrators.** `dq.parallel.runs` in FormShare's .ini sets how many runs over all records may go at once on one form (2 by default). `dq.stale_run_minutes` sets how long a run may give no sign of life before it is taken for dead (2 by default, 1 at least).
{% endhint %}

## After a run

Under the buttons, the Checks box says how the last run over all records ended: "Last run over all records", when and by whom, how many records it checked and how many it flagged; or "Last nightly run"; or *The last run failed*, with the reason.

Each check's **Last run** column says what its last run found: "N of M flagged" for a check on records, "N warnings, M rows read" for a check on the whole form, or *Not run yet*. Its notes follow, such as the limits of an outlier, or why the check did not run.

## What a run leaves out

| When | What happens |
| --- | --- |
| A check reads a column the repository does not have | The check is not run, and its line says *Not run: the repository has no* and names the column. |
| A new version moved a check's question onto a shared screen, or out of its repeat | The check is not run, and its line says so. |
| The form's audit no longer records locations | The GPS check of the audit is not run, and its line says so. |
| A record does not meet a check's **Only when** | It is not judged, and counts as clean for that check. |
| A record's version did not ask a question | See "[Records sent with different versions](check-types.md#records-sent-with-different-versions)". |
| A record has no audit | The checks of the audit do not judge it. |
| A field has too few answers for an outlier, or a group too few for its test | It is noted, and nothing is flagged. |
| A submission is deleted, or moved to the logs, during a run | It is left out, and the run goes on with the others. |
| The form is being deleted | It is left out of every run. |

## The nightly run

**Run all checks nightly**, in the Checks box, is ticked by default on every form with checks. Untick it and press **Save** to pause the nightly run of the form; tick it again to resume. The choice holds for every version of the form, and the last one saved counts.

At night the server goes through the forms one by one. A form runs when:

* it has checks, and its repository can hold a score;
* its box, **Run all checks nightly**, is ticked;
* it changed since the last run of all its checks started;
* a place for a run is free on it.

A form that has not changed is skipped. A form busy with other runs waits for the next night. A form with **Cases without a follow-up** runs every night, because the days without a follow-up grow.

Beside the box, the page says when the nightly run last looked at the form:

| The page says | Meaning |
| --- | --- |
| *Last nightly run*, and its date | That night the nightly run ran the form's checks on all its records. |
| *Last nightly run*, its date, and *nothing had changed since the checks last ran on all records.* | That night the nightly run looked at the form and found nothing to check: no change since a run of all its checks, yours or an earlier night's. |
| *No nightly run has checked this form yet.* | No nightly run has looked at the form. |

The date moves on every night the server's nightly job works. It stays the same while the form is paused, and on a night when the form was busy with other runs or the nightly run stopped before reaching it. So *No nightly run has checked this form yet.* means that the form got its first check after the last night, that its nightly run has been paused since then, or that the server has no nightly job. In the last case, ask the server's administrator: the page points them to this section, under *For server administrators*.

{% hint style="info" %}
**For server administrators.** The nightly run is a command, `dq_run_nightly`, that the plugin installs in FormShare's environment. Run it from cron, for example at 02:15 server time:

```
15 2 * * * <env>/bin/dq_run_nightly /path/production.ini >> /var/log/formshare/dq_nightly.log 2>&1
```

It runs in its own process, not through Celery, so a night of checks does not hold the workers that exports and merges need. `--max-minutes N` stops it starting new forms after N minutes; the rest wait for the next night. It prints one line for each form it checks, and a line of counts.
{% endhint %}
