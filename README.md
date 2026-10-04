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

## Data structure template

A ready-to-use Excel template (`data_structure_template.xlsx`) is provided with the application.

Users are strongly encouraged to use this template when preparing data for the Serbia Map Generator. It already contains all 26 administrative regions in the format expected by the application.

The template contains three predefined columns:

| Column | Purpose | Can it be changed? |
|---|---|---|
| `NSTJ3` | Regional administrative identifier | **No** |
| `shapeName` | Links the uploaded data to the built-in Serbia geometry | **No** |
| `reg_ozn` | Controls the region name displayed on the map | **Yes, with caution** |

### `shapeName` — do not modify

The `shapeName` column is the key field used to connect each row of the uploaded table with the corresponding region in the built-in Serbia geometry.

**Do not rename this column and do not modify its values.**

For example:

| Correct | Incorrect |
|---|---|
| `Beogradski` | `Beograd` |
| `Zapadnobački` | `Zapadna Bačka` |
| `Nišavski` | `Niš` |
| `AP Kosovo i Metohija` | `Kosovo` |

Changing these values may prevent the corresponding region from being matched.

The application reports the number of successfully matched regions after a file is uploaded. A complete dataset based on the supplied template should report:

`26/26 Serbia regions matched by shapeName`

### `NSTJ3` — leave unchanged

The `NSTJ3` column contains the predefined regional administrative identifiers.

Users should leave both the column name and its existing values unchanged.

This column is treated as structural information and is not offered as a variable for mapping.

### `reg_ozn` — map label

The `reg_ozn` column determines the **region name displayed on the finished map**.

Unlike `shapeName`, the contents of this column can be edited if a different displayed label is desired.

The supplied template already contains labels formatted to fit the geometry of individual regions. Some longer names contain deliberate line breaks, for example:

`Zapadno-`
`bački`

or:

`Severno-`
`banatski`

These line breaks help keep labels inside smaller or narrower regions.

Users may modify `reg_ozn`, but the supplied values are recommended because they have been formatted for the predefined map layout.

**Changing `reg_ozn` does not affect the spatial join.** The `shapeName` column is used for matching regions.

---

## Adding data to the template

Users should add their own variables as **new columns to the right of the three predefined columns**.

For example:

| NSTJ3 | shapeName | reg_ozn | Population | Unemployment rate | Number of cases |
|---|---|---|---:|---:|---:|
| RS111 | Beogradski | Beogradski | 1685000 | 7.2 | 125 |
| RS121 | Zapadnobački | Zapadno-bački | 154000 | 8.4 | 18 |
| ... | ... | ... | ... | ... | ... |

There is no need to modify the application when adding new variables.

Each additional numeric column will automatically appear in the **Variable to map** selector after the file is uploaded.

### Column names

Users are free to choose descriptive names for their data columns.

For example:

- `Population, 2022`
- `Average age`
- `Unemployment rate (%)`
- `Number of Salmonella cases`
- `Doctors per 1000 inhabitants`
- `Natural population change (2022)`

The selected column name is also automatically used as the title of the colour scale on the exported map, so descriptive column names are recommended.

---

## What users can change

Users can:

- add as many data-variable columns as required;
- change the names of their own data columns;
- enter integer or decimal numeric values;
- leave observations blank when data are unavailable;
- modify `reg_ozn` if a different displayed regional label is required;
- use line breaks in `reg_ozn` to control how long region names are displayed.

## What users should not change

Users should **not**:

- rename the `shapeName` column;
- modify the predefined values in `shapeName`;
- rename or modify the predefined `NSTJ3` identifiers;
- delete regions from the template unless they intentionally want an incomplete dataset;
- add additional geographic regions that are not part of the built-in Serbia geometry.

The safest approach is to leave the first three columns exactly as provided and simply add new data columns to the right.

---

## Missing data

If a value is unavailable for a particular region, leave the corresponding cell blank.

Do **not** enter `0` unless the actual value is zero.

The application distinguishes between zero and missing data:

- `0` = an actual numeric value of zero;
- blank cell = missing observation.

Missing observations are displayed on the map using the `ø` symbol.

For example:

| shapeName | Number of cases |
|---|---:|
| Beogradski | 15 |
| Nišavski | 0 |
| Pčinjski | |

In this example:

- Beogradski has 15 cases;
- Nišavski has an observed value of zero;
- Pčinjski has no available observation.

---

## Recommended workflow

1. Download or copy `data_structure_template.xlsx`.
2. **Do not modify the first three columns.**
3. Add the variables you want to map as new columns.
4. Enter one value for each region.
5. Save the file as `.xlsx`.
6. Upload it to the Serbia Map Generator.
7. Confirm that the application reports `26/26` matched regions.
8. Select the desired variable and generate the map.

Using the supplied template is the recommended way to ensure that regional names and identifiers remain fully compatible with the built-in Serbia geometry.
