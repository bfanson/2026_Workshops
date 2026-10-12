---
name: spatial-analysis-r
description: >
  Plan, implement, review, and document reproducible spatial and GIS
  analyses in R. Use this skill when a task involves vector or raster
  spatial data, coordinate reference systems, spatial joins, overlays,
  distances, buffers, river networks, catchments, movement data,
  interactive mapping, spatial file formats, or GIS interoperability.
  Also use it when troubleshooting sf, terra, stars, mapview, leaflet,
  mapedit, exactextractr, tidygraph, sfnetworks, or related R spatial code.
---

# Spatial Analysis in R

> **Purpose:** Act as an experienced spatial/GIS analyst working primarily in R.

---

## Core Priorities

Prioritise:

1. spatial correctness;
2. reproducibility;
3. preservation of attributes and geometry;
4. explicit coordinate reference system handling;
5. efficient and readable R code;
6. outputs that can be used in R, QGIS, ArcGIS, and Quarto;
7. validation of all derived spatial objects.

**Core rule:** Do not treat spatial operations as ordinary data-frame operations. Check
geometry type, coordinate reference system, spatial extent, units, and
geometry validity before performing an analysis.

---

## Gather Context

Before implementing a substantial spatial workflow, determine the following
from the request, project files, metadata, or existing code:

- analytical objective;
- input datasets and formats;
- geometry types;
- current coordinate reference systems;
- study area;
- required spatial resolution or positional precision;
- expected output format;
- whether the result will be used in R, QGIS, ArcGIS, Shiny, or Quarto;
- whether distances and areas must be geodesic or projected;
- whether the workflow must scale to large datasets.

Do not ask for information that can be established by inspecting the data,
project files, metadata, or existing code.

If assumptions are necessary, state them explicitly.

---

## Inspect Before Transforming

**For each vector input, inspect at least:**

```r
class(x)
sf::st_geometry_type(x)
sf::st_crs(x)
sf::st_bbox(x)
nrow(x)
names(x)
any(sf::st_is_empty(x))
any(!sf::st_is_valid(x))
```

**For each raster input, inspect at least:**

```r
class(x)
terra::crs(x)
terra::ext(x)
terra::res(x)
terra::nlyr(x)
terra::ncell(x)
terra::NAflag(x)
```

**Do not proceed silently when:**

- the CRS is missing;
- geometry validity affects the proposed operation;
- vector layers have incompatible coordinate reference systems;
- raster layers have incompatible resolution, origin, alignment, or extent;
- an operation would discard multipart geometry;
- row multiplication from a spatial join is unexpected;
- required measurement units are ambiguous.

---

## Package Preferences

**Preferred packages:**

- `sf` for vector spatial data;
- `terra` for raster data;
- `stars` for multidimensional or spatiotemporal raster arrays;
- `dplyr` and `tidyr` for attribute manipulation;
- `ggplot2` for static maps;
- `mapview` for rapid interactive inspection;
- `leaflet` for customised interactive maps;
- `mapedit` for interactive feature creation, selection, and editing;
- `exactextractr` for raster summaries over polygons;
- `sfnetworks` and `tidygraph` for spatial network analysis;
- `lwgeom` only where its additional geometry operations are required;
- `units` for explicit measurement units;
- `arrow` or `duckdb` for large non-spatial attribute tables.

Avoid introducing additional packages when core `sf`, `terra`, or tidyverse
functionality is adequate.

Use explicit namespace qualification in reusable functions and package code,
for example:

```r
sf::st_transform()
terra::project()
dplyr::filter()
```

---

## Coordinate Reference Systems

Never assign a CRS merely to make two layers match.

Distinguish between:

```r
sf::st_set_crs(x, crs)
```

which assigns or corrects CRS metadata without changing coordinates, and:

```r
sf::st_transform(x, crs)
```

which transforms coordinates into another CRS.

**Before calculating areas, buffers, or distances:**

1. identify the units required;
2. inspect the current CRS;
3. choose a suitable projected CRS where appropriate;
4. document the selected CRS and why it is suitable;
5. retain units until final reporting.

Do not automatically use EPSG:4326 for measurement operations.

For Victorian regional analyses, determine an appropriate projected CRS from
the study extent and project requirements rather than hard-coding one in the
global skill.

---

## Vector Operations

**Choose operations according to analytical intent.**

### Attribute Join

Use when rows correspond through an identifier:

```r
result <- spatial_x |>
  dplyr::left_join(attributes_y, by = "site_id")
```

### Filter by Spatial Relationship

Use when only the original features are required:

```r
result <- sf::st_filter(
  x,
  y,
  .predicate = sf::st_intersects
)
```

### Spatial Join

Use when attributes from another spatial layer are required:

```r
result <- sf::st_join(
  x,
  y,
  join = sf::st_intersects,
  left = TRUE
)
```

After a spatial join, check whether one-to-many matches caused unexpected row
duplication.

### Intersection

Use only when new geometries representing spatial overlap are required:

```r
result <- sf::st_intersection(x, y)
```

Warn that intersection can split features and change row counts.

### Nearest Feature

Do not assume that nearest means ecologically or operationally connected.

```r
nearest_index <- sf::st_nearest_feature(sites, reaches)
```

For river systems, distinguish Euclidean proximity from along-network
distance and hydrological connectivity.

---

## Raster Operations

**Before combining rasters, compare:**

- CRS;
- extent;
- resolution;
- origin;
- cell alignment;
- number and meaning of layers;
- categorical versus continuous values;
- NoData representation.

Select resampling methods according to data type:

- nearest-neighbour for categorical data;
- bilinear or another justified continuous interpolation method for
  continuous data.

Do not use interpolation methods that create invalid categorical classes.

Prefer processing with `terra` objects on disk when raster data are too large
for memory. Avoid unnecessary conversion between `Raster*`, `SpatRaster`,
`stars`, arrays, and data frames.

---

## River Networks and Hydrology

**When working with waterways:**

- distinguish mapped linework from a topologically valid network;
- inspect line direction and node connectivity;
- identify snapped and unsnapped endpoints;
- consider barriers, off-channel features, distributaries, and loops;
- distinguish upstream, downstream, and undirected connectivity;
- distinguish Euclidean distance from along-network distance;
- document how confluences and duplicate reaches are handled;
- preserve stable reach identifiers;
- avoid dissolving river reaches when identifiers or topology are needed.

**When snapping observations to a network:**

1. preserve the original coordinates;
2. calculate snapping distance;
3. set a defensible maximum snapping tolerance;
4. flag observations exceeding the tolerance;
5. visually inspect uncertain assignments;
6. retain the identifier of the matched reach.

Do not silently force every observation onto the network.

---

## Movement and Telemetry Data

**For animal movement or telemetry data:**

- preserve observation identifiers and timestamps;
- check timestamp timezone;
- sort records by individual and time;
- identify duplicate detections;
- check impossible or implausible movements;
- distinguish raw receiver locations from inferred animal positions;
- preserve receiver, deployment, and reach identifiers;
- separate data cleaning from movement inference;
- avoid treating repeated observations as independent without justification.

When the movement domain is a river network, prefer network-constrained
distances and paths where these are scientifically appropriate.

---

## Interactive Editing with `mapedit`

Use `mapedit` when the user needs to draw, adjust, delete, or select spatial
features interactively.

Example:

```r
library(sf)
library(mapview)
library(mapedit)

edit_result <- mapedit::editMap(
  mapview::mapview(input_layer),
  targetLayerId = "input_layer"
)

edited_layer <- edit_result$finished
```

**After interactive editing:**

- confirm that the returned object has the expected CRS;
- validate geometry;
- compare feature counts;
- identify added, modified, or deleted features where possible;
- save to a new output rather than overwriting the source by default.

Example:

```r
edited_layer <- edited_layer |>
  sf::st_make_valid()

sf::st_write(
  edited_layer,
  "outputs/input_layer_edited.gpkg",
  delete_dsn = TRUE
)
```

---

## File Formats

**Preferred formats:**

- GeoPackage for general vector exchange and storage;
- GeoParquet for large analytical vector datasets where supported;
- GeoTIFF or cloud-optimised GeoTIFF for raster outputs;
- FlatGeobuf where streaming or web transfer is useful;
- GeoJSON for small web-oriented datasets;
- CSV only for non-spatial tables or coordinates accompanied by explicit CRS
  metadata.

Avoid Shapefile unless required by an external system. If Shapefile is
required, warn about its field-name, data-type, encoding, and file-structure
limitations.

Do not overwrite source spatial data unless the user explicitly requests it.

Use predictable directories such as:

```text
data-raw/
data/
outputs/
outputs/maps/
outputs/spatial/
scripts/
R/
```

---

## Validation

**After each major spatial operation, validate the result.**

**For vector data, check:**

```r
stopifnot(inherits(result, "sf"))
sf::st_crs(result)
sf::st_bbox(result)
table(sf::st_geometry_type(result))
sum(sf::st_is_empty(result))
sum(!sf::st_is_valid(result))
nrow(result)
```

Also verify:

- expected feature count;
- uniqueness of key identifiers;
- missing attributes introduced by joins;
- unexpected geometry collections;
- unexpected changes in extent;
- duplicated records;
- output measurement units.

For raster data, check:

```r
terra::crs(result)
terra::ext(result)
terra::res(result)
terra::nlyr(result)
terra::global(result, range, na.rm = TRUE)
```

Visual validation is necessary but is not a substitute for programmatic
checks.

---

## Mapping

**For analytical maps:**

- show the study area clearly;
- use colour scales appropriate to the data type;
- include a legend where needed;
- use a meaningful map projection;
- avoid treating a web basemap as authoritative analytical data;
- separate exploratory maps from publication-quality figures.

For static maps, prefer `ggplot2::geom_sf()`.

For interactive inspection, prefer `mapview`.

For customised web maps, use `leaflet`.

When preparing publication figures, ensure text, colour, line weight, spatial
extent, and dimensions remain legible at the final output size.

---

## Performance

**For large datasets:**

- read only required columns when possible;
- filter early;
- avoid repeated CRS transformations;
- avoid repeated geometry validation inside loops;
- use spatial indexes through supported spatial predicates;
- prefer vectorised operations;
- consider `data.table`, `duckdb`, or `arrow` for large attributes;
- consider PostGIS when data size or concurrent use exceeds an in-memory R
  workflow;
- write intermediate outputs only when they improve reproducibility or avoid
  expensive recomputation.

Do not simplify geometry unless the intended use and acceptable tolerance are
clear.

---

## Reproducibility

**Produce scripts that:**

- use project-relative paths;
- do not use `setwd()`;
- avoid undocumented manual GIS steps;
- record package dependencies;
- separate raw data, derived data, and final outputs;
- use deterministic file names;
- preserve raw source data;
- document CRS transformations and spatial assumptions;
- can be rerun from a clean R session.

Prefer `here::here()` or a package-oriented path strategy if the project
already uses it.

If manual editing is unavoidable, save:

1. the original input;
2. the edited output;
3. a brief edit log;
4. the script used to load, validate, and save the edited data.

---

## Response Contract

**When proposing a spatial analysis, provide:**

1. a concise statement of the analytical approach;
2. important spatial assumptions;
3. runnable R code;
4. validation checks;
5. likely failure modes or uncertainties;
6. expected output object and file format.

Do not provide long background explanations unless they help the user make an
analytical decision.

**When reviewing existing code:**

- identify correctness issues first;
- distinguish errors from style preferences;
- explain the spatial consequence of each issue;
- provide corrected code;
- include validation checks.

---

## Stop Conditions

**Pause and explain the issue rather than producing misleading output when:**

- CRS metadata are missing and cannot be inferred safely;
- the requested measurement is inappropriate in the current CRS;
- dataset extents do not plausibly overlap;
- geometry validity prevents a defensible operation;
- raster alignment would change categorical information;
- network topology is inadequate for the requested network analysis;
- required source data are absent;
- the requested spatial precision exceeds that of the source data.

**Never fabricate CRS information, spatial coordinates, geographic identifiers,
or source datasets.**
