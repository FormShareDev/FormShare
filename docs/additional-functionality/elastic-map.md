---
description: ElasticMap is the high-volume map view that automatically replaces the standard project and form maps once you have enough geotagged submissions for clustering to be useful.
---

# ElasticMap

**ElasticMap** is an enhanced map experience that takes over when a project or form has a large enough number of geotagged submissions for the default map to become unwieldy. It uses smart clustering and on-demand loading so the map stays responsive even with tens of thousands of points.

You don't turn ElasticMap on — it activates automatically once the submission count crosses a threshold. The user-visible difference is that the map starts showing clusters of submissions instead of individual pins, and the map remains snappy at any zoom level.

## Where you see it

ElasticMap replaces the standard map on three pages:

* **The dashboard**, when the active project has enough geotagged submissions across its forms.
* **The project details page**, when the project as a whole crosses the threshold.
* **The form details page**, when an individual form has enough geotagged submissions.

The pages look the same — the map panel is in the same place. What changes is how data is rendered and how navigation feels.

<!-- TODO screenshot: an ElasticMap view at a continental zoom level showing several large cluster badges. -->

## How the map behaves

ElasticMap operates in two modes that switch automatically with zoom level:

* **Approximation mode** — at broader zoom levels (continent, country, region), submissions are grouped into circular cluster badges with the count shown on each. Clusters are sized and coloured by how many submissions they contain. A label at the top of the map says "Approximation".
* **Precise mode** — when you zoom in enough that fewer than a couple of hundred submissions are visible, the map switches to showing each submission as an individual dot. The label switches to "Precise".

The map only loads the data you can currently see. Panning to a new area triggers a fresh fetch for that region; the rest of the world is not paid for until you go there.

## What you do with it

* **Pan and zoom** as on any map.
* **Click a cluster** to zoom in on the area it represents. The cluster expands into its constituent sub-clusters or, eventually, individual points.
* **Click an individual point** (in precise mode, on a form's map) to open the submission's detail in a popup — the same submission details panel you'd see elsewhere in FormShare.
* **Expand to fullscreen** with the control in the map's top right. The map redraws to use the full browser width; click again to collapse.
* **Search and filter** if your form provides those controls — narrows what appears on the map.

<!-- TODO screenshot: a cluster being clicked, zooming in to show smaller sub-clusters or individual points. -->

## Performance

The main reason ElasticMap exists is performance. Drawing tens of thousands of pins on a single map slows browsers to a crawl; clustering at the server side and streaming only what's in view keeps the map fast no matter how much data is behind it.

For small forms (a few dozen submissions), ElasticMap does not activate — the default map is already fast and showing every individual pin is more useful than clustering.

## Permissions

* ElasticMap respects exactly the same access rules as the rest of FormShare. You see only the projects, forms, and submissions you have access to.
* Fields marked as [sensitive](../data-management/data-dictionary/marking-sensitive-fields.md) follow the same handling on the map as elsewhere — sensitive submissions appear as points but the popup respects the sensitivity flags.

## Setup and data source

There is nothing to configure. ElasticMap reads the same geopoint variable as the default map — the one outside any repeat that the form's author designated. It uses the existing submission index, so there is no separate indexing step.

The first time a project or form crosses the threshold, the map switches automatically on the next page load. Below the threshold, the standard map continues to render.

## What's next

* "[The dashboard](../fundamentals/the-dashboard.md)" — where the project map appears.
* "[KML for geographic data](../data-management/data-products/kml.md)" — for exporting the map data to Google Earth / QGIS.
* "[MassiveSpatial](massive-spatial.md)" — for layering reference spatial data on top.
