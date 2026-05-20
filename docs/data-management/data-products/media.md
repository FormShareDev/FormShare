---
description: The Media export collects every media file (photos, audio, video, signatures) submitted with a form into one Zip archive, organized by submission.
---

# Media

The **Media export** produces a Zip archive of every media attachment ever submitted to a form: photos, signatures, audio recordings, short videos. Files are organized into one folder per submission so you always know which submission each file came from.

Use this when you need to look at the raw media without going submission-by-submission in the FormShare UI, or when you need to hand the files to someone who isn't using FormShare at all.

## Where you see it

On the form details page, click **Export data** → pick **Media**. The product appears in the **Products and tasks** tab when the background task finishes. Like KML, Media is a single non-publishable variant — there is no public/private distinction (media attachments are inherently sensitive and not designed for open distribution).

The "Download submitted media" option that appears for forms in the [testing stage](../../fundamentals/forms/#the-testing-stage) produces the same kind of archive, just from the testing-stage submissions instead of the repository.

## What's in the zip

The archive contains one folder per submission that has media attached. Folder names are derived from the submission's primary key (with any unsafe characters escaped). Inside each folder, files are saved with **their original names** as submitted by ODK Collect:

```
H-001/
  photo_household.jpg
  signature_consent.png
  audio_interview.m4a
H-002/
  photo_household.jpg
  photo_plot_1.jpg
```

Submissions without any media do not get a folder.

If **no** submission in the form has any media, the export still produces a zip, but it contains a single placeholder `empty.txt` file noting that nothing was attached.

## Options at generation

The Media export has **no options**. You click the export button and FormShare bundles every media file currently in the repository. The bundle reflects the state of the data at the moment of generation; submissions added after the export are not included until you regenerate.

## When to use the Media export

* You want to inspect media outside FormShare — review photos for quality, transcribe audio, scrub video.
* You need to deliver media to a partner along with the data — pair this with a [zipped CSV](zipped-csv.md) so they have the records and the attachments together.
* You're archiving a closed project. The Media export plus a private data export captures the full state of the form's outputs.

## Practical tips

* **Sizes can be large.** Forms with image fields and many submissions can produce multi-gigabyte zips. Generate during off-hours if your network is limited.
* **Original filenames are preserved.** This is intentional — if the same enumerator used the same filename across submissions (`photo.jpg`), each submission's folder keeps its own copy, and there's no collision.
* **Folders are named by primary key, not submission ID.** If two submissions have the same primary key (which should be impossible — the [error log](../cleaning/submissions-with-errors.md) catches duplicates — but can happen during migration), there is a folder-name collision. Resolve any duplicate keys before exporting media.
* **The export is cached.** Like every product, the zip is generated once and downloaded as many times as you want until you regenerate.

## What this is *not*

* The Media export is **not** an export of the data fields associated with media. There is no CSV in the zip that says which file went with which question. If you need that, generate a [zipped CSV](zipped-csv.md) alongside — the data files reference each media filename in the corresponding column.
* It is **not** a thumbnail browser. The zip is the raw files; use your operating system's photo viewer (or a tool like Adobe Bridge) to browse efficiently.

## What's next

* "[Zipped CSV](zipped-csv.md)" — generate alongside to get the data referencing the media files.
* "[Working with submissions](../cleaning/working-with-submissions.md)" — what happens to media when a submission is deleted (the data row is removed but the underlying media files are kept on disk).
