---
description: MassiveSpatial is a built-in geospatial data manager. Organise raster and vector layers into databases, classify them with colour rules, and surface them as map context inside your projects.
---

# MassiveSpatial

**MassiveSpatial** is FormShare's spatial data management workspace. It lets you bring in your own geographic reference layers — satellite imagery, administrative boundaries, conservation areas, infrastructure, conflict zones — organise them into databases, define how they should be visualised, and then surface those layers as background context on the maps inside your projects.

It is for when your data collection needs spatial context that doesn't come from submissions. Enumerators in the field need to see the boundary of the protected area they're surveying; analysts need to overlay submission points on land-use classifications; project managers need to know which roads were passable last quarter. MassiveSpatial is where you load, classify, and surface those layers.

## Where to find it

A new entry labeled **MassiveSpatial** (globe icon) appears in the main navigation. Click it to open the **MassiveSpatial Databases** page — the home of all your spatial work.

<!-- TODO screenshot: the MassiveSpatial Databases page with a couple of databases listed. -->

## The four objects

MassiveSpatial is organised around four nested objects:

* **Databases** — top-level containers for related layers. Typically one database per project or per geography.
* **Layers** — individual spatial datasets inside a database. Either **raster** (a GeoTIFF — satellite imagery, elevation, NDVI) or **vector** (Shapefile, GeoJSON, GeoPackage, KML, or FlatGeobuf — boundaries, points, lines).
* **Classifications** — named groupings of colour rules applied to one layer. A layer can have multiple classifications (e.g. one "by region", another "by risk level") and you choose which one to use when surfacing the layer on a project map.
* **Classes** — the individual rules inside a classification, mapping data values to colours. For raster, each class is a value range with a colour gradient; for vector, each class matches an attribute value and assigns a solid colour.

## The workflow

### 1. Create a database

From **MassiveSpatial Databases**, click **Add Database**. Provide a name (letters, digits, underscores) and an optional description. Submit. The database is created and ready to receive layers.

### 2. Add a layer

Open the database and click **Add Layer**. Provide:

* A layer name (letters, digits, underscores).
* An optional description.
* The **type** — Raster (`.tif`/`.tiff`) or Vector (`.zip` Shapefile, `.geojson`, `.json`, `.gpkg`, `.kml`, `.fgb`).
* The file itself. For Shapefiles, upload the entire bundle as a single Zip archive.

Click **Upload & Import**. The layer enters a **Processing** state while MassiveSpatial reads it; this can take from seconds (a small vector file) to several minutes (a large raster). When the status flips to **Ready** the layer is available for classification.

<!-- TODO screenshot: the Layers view showing several layers with their status badges (Ready, Processing). -->

### 3. Create a classification

On the layer's row, click **Classify** (palette icon). The **Classifications** page opens. Click **Add Classification**:

* Give it a name (e.g. `risk_levels`, `land_use`).
* Optional description.
* **For vector layers**: pick the **attribute field** the classification will read from. All classes in this classification will match against that field.

Submit. The classification is created, empty.

### 4. Add classes

Click **Classes** (list icon) on the classification's row. Then **Add Class** for each rule:

* Class name.
* Optional description.
* Display order — lower numbers come first.
* **For raster layers**: a **Value From** / **Value To** range to match raster pixel values, plus a **Color From** / **Color To** gradient. Use the same colour twice for a solid fill.
* **For vector layers**, the form adapts to the attribute type:
  * **Text attribute** — enter an exact **Attribute Value** to match.
  * **Numeric attribute** — enter **Value From** / **Value To** range.
  * **Date attribute** — enter **Date From** / **Date To** range.
  * Pick a single solid colour.

Add as many classes as the classification needs (typically 3–10).

### 5. Surface the layer on a project map

The work above lives inside MassiveSpatial. To make a layer visible inside a project, the project owner assigns it to the project (in the project's settings).

Once assigned, the layer appears on the **map panel of the project's forms**, with a sidebar where users can:

* Toggle layers on and off.
* Switch between classifications when a layer has more than one.
* Pick the colour of the submission dots themselves.
* Save the current map view to revisit later.

<!-- TODO screenshot: a form's map with the MassiveSpatial layers sidebar open, showing toggleable layers grouped by database. -->

## Editing and deleting

Every object — database, layer, classification, class — has Edit and Delete actions. Deletes cascade: deleting a layer removes its classifications and classes; deleting a database removes everything inside it. There is no undo, so the typical pattern is to mark layers obsolete in a description rather than delete them.

## Permissions

* MassiveSpatial is restricted to FormShare users with the relevant access role. Most accounts have it by default; assistants and partners do not.
* Only the user who creates a database / layer / classification can edit or delete it.
* Layers assigned to a project become visible to **everyone who can see the project's maps** — that's the point of the feature. Decide carefully which layers you assign, especially if they contain sensitive boundaries (active conflict zones, household locations, etc.).

## Relation to FormShare data

MassiveSpatial is independent of submissions — you can have a richly populated MassiveSpatial workspace before any form has been uploaded. But the two intersect in two useful ways:

* **Layers as context for submissions**: assigned layers appear behind submission pins on project maps. This is the most common use — enumerators and analysts see where each submission sits relative to the reference geography.
* **Submission points on MassiveSpatial maps**: forms with a geopoint variable contribute points that the MassiveSpatial map renders alongside the layers, so spatial analysis can be done in one view.

## What's next

* "[ElasticMap](elastic-map.md)" — the dynamic map view that activates on dense submission sets.
* "[KML for geographic data](../data-management/data-products/kml.md)" — for taking the data out into Google Earth or QGIS.
