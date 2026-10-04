# NUTS3 Mapper Serbia

A lightweight, browser-based service for generating publication-ready choropleth maps of Serbia from Excel or CSV data.

The application is specifically designed around the administrative geometry of Serbia included with the project. Users do not need GIS software, shapefiles, GeoJSON files, or any knowledge of spatial data processing. They only need to upload a table containing data for the Serbian administrative regions.

The service automatically matches uploaded data to the built-in Serbia geometry, generates a choropleth map, places regional labels and values, creates a continuous colour scale, and exports the finished map as a high-resolution JPEG.

---

## Overview

The Serbia Map Generator was developed as a simple web-based alternative to a Python/GeoPandas map-generation workflow.

The main goal is to make production of standardized thematic maps of Serbia accessible to users who may not work with GIS software.

Instead of requiring the user to:

- load a shapefile,
- load centroid geometry,
- load the national outline,
- configure coordinate systems,
- perform attribute joins,
- configure map symbology,
- manually position labels,
- construct a colour scale,
- and export the resulting figure,

the web application handles these operations automatically.

The normal workflow is simply:

**Upload data → Select variable → Configure map → Generate → Export**

All processing is performed locally in the user's browser.

---

# Features

## Serbia-specific geometry

The application is specifically designed for Serbia and includes the required spatial information directly within the service.

The geometry consists of:

- administrative region polygons;
- predefined label/centroid positions;
- Serbia outline geometry;
- regional names and identifiers.

Users therefore **do not upload any spatial data**.

The original GIS source files are retained in the `shapefiles` directory for documentation and project organization, while browser-compatible geometry is incorporated into the application.

---

## Excel and CSV input

The application accepts:

- `.xlsx`
- `.xls`
- `.csv`

Only the first worksheet of an Excel workbook is processed.

The uploaded table should contain one row for each Serbian administrative region.

---

## Automatic spatial join

Uploaded data are linked to the built-in Serbia geometry using the:

`shapeName`

field.

Conceptually, the operation corresponds to:

```python
pd.merge(
    geometry,
    data,
    left_on="shapeName",
    right_on="shapeName",
    how="left"
)
