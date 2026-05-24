---
description: KML export turns the GPS submissions of a form into a Google Earth–compatible map file, with one placemark per submission and configurable popup data.
---

# KML for geographic data

The **KML export** produces a [Keyhole Markup Language](https://en.wikipedia.org/wiki/Keyhole_Markup_Language) file — the format used by Google Earth, QGIS, and most mapping software — from the GPS submissions of a form. Each submission with a geopoint becomes a placemark; the placemark's popup carries whichever fields you decide are useful at-a-glance.

## When KML is available

A form qualifies for KML export when:

* The form has a `geopoint` variable **outside any repeat**. FormShare reads the latitude/longitude from this variable (the same one shown on the dashboard and project maps).
* At least one submission has a non-empty value for that variable.

If neither condition is met, the KML option appears greyed out or the generation fails with a message saying no geopoints were found.

## Where you see it

On the form details page, click **Export data** → pick **KML**. The product appears in the **Products and tasks** tab when the background task completes. KML is **non-publishable** — there is one variant, controlled by the [sensitive-field configuration](../data-dictionary/marking-sensitive-fields.md) just like a private product. GPS coordinates are sensitive by their nature, so FormShare deliberately keeps KML in the team's hands rather than offering a public version.

<!-- TODO screenshot: the "Export data" dialog for KML showing the field-selector multi-select. -->

## What's in the KML

A standard KML file with:

* One `<Placemark>` per submission that has a geopoint. The placemark's coordinates come from the geopoint variable.
* An `<ExtendedData>` block on each placemark listing the values of the fields you chose to include. These render as a popup when the placemark is clicked in Google Earth or QGIS.
* A `<Schema>` block documenting each field's name and display label.
* A consistent icon for all placemarks (currently the default yellow square; FormShare does not yet support custom icons per form).

When opened in Google Earth, the placemarks appear at their coordinates; clicking one shows the popup with each selected field as a labelled row.

## Options at generation

* **Fields to include in the popup**: a multi-select dropdown of every field in the main table. Pick the ones useful for inspecting submissions on the map (typically the primary key, the case label if you have one, and a handful of identifying fields). The geopoint itself is always included.
* **Label rendering**: code only, label only, or both — for `select_one` fields included in the popup.

Fields marked as [sensitive](../data-dictionary/marking-sensitive-fields.md) are not available for inclusion regardless of what you pick — KML output respects the same protection rules as a public product.

## When to use KML

* Quick visual inspection of where submissions came from, especially for QA.
* Sharing the spatial pattern of submissions with a partner who isn't on FormShare but has Google Earth.
* Geographic analysis: load the KML into QGIS or ArcGIS and overlay it on basemaps, perform spatial joins, etc.
* Field debriefs: project a printed map of placemarks with the popup data printed alongside.

## When *not* to use KML

* You need every field of every submission — KML is meant for the headline metadata, not the full dataset. Use [zipped CSV](zipped-csv.md), [Excel](excel.md), or [JSON](json.md) for that.
* The form has no geopoint outside a repeat. Geopoints inside repeats are not picked up by the KML export.
* You want to share GPS data publicly. The KML deliberately has no public variant — be deliberate about who you hand it to.

## Practical tips

* **Coordinate order in KML is lng/lat**, the opposite of how most people say coordinates ("lat, lng"). FormShare emits them in the correct KML order automatically.
* **Altitude is 0.** The geopoint's altitude component (if present in ODK Collect's geopoint payload) is not exported — placemarks render at ground level.
* **Each regeneration produces a fresh KML.** Cache after generation; regenerate to pick up new submissions.

## What's next

* "[Marking fields as sensitive](../data-dictionary/marking-sensitive-fields.md)" — controls what can appear in the KML.
* "[Zipped CSV](zipped-csv.md)" / "[JSON](json.md)" — for full-data exports.
* "[Media](media.md)" — for the photos and audio associated with submissions.
