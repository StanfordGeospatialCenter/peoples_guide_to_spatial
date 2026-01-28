# Spatial Data 101: Points, Lines, Polygons, and Pixels

Welcome to Week 01. This is where we make Tobler’s law ("near things are more related") and "everything is somewhere and that somewhere matters" practical. You’ll learn what spatial data actually is, how it’s modeled, and which tools we’ll lean on to work with it.

## Why this matters
- Spatial data is everywhere (phones, transit feeds, health dashboards, climate maps) and every decision has a location.
- Picking the right data model early keeps you from repainting the map later.
- Good habits now (projections, metadata, file naming) save weeks of cleanup.

## Core ideas to carry forward
- **Two laws**: Tobler’s law + "everything is somewhere" as the course anchors.
- **Spatial relationships**: adjacency, proximity, containment, intersection; start looking for them in every dataset.
- **Not just ArcGIS**: we’ll use open tools (QGIS, Google Earth Engine, Whitebox Tools) and light programming, but it’s beginner-friendly.

## Data models at a glance
- **Points**: use for discrete locations (sensors, addresses, incidents). Smallest unit, fast to collect.
- **Lines**: use for connections and paths (roads, rivers, cables). Great for distance/network questions.
- **Polygons**: use for areas (parcels, neighborhoods, habitats). Best for containment and overlay.
- **Pixels/rasters**: use for continuous surfaces (elevation, imagery, temperature). Resolution matters; every cell has a value.

## Attributes: the table behind the map
- Geometry is only half the story; attributes live in rows and columns.
- **Columns/fields** describe things (name, type, units); **rows/records** are individual features; **labels** are human-readable; **values** power analysis.

## Choosing the right model
- Match the question to the model: proximity → lines/points; coverage/intensity → rasters; ownership/eligibility → polygons.
- Tradeoffs: vectors are precise and light; rasters are great for surfaces and math but heavier and resolution-dependent.

## Tools we’ll use this week
- **QGIS**: primary desktop GIS. Install + add **Whitebox Tools** plugin for raster/terrain work.
- **Google Earth Engine**: planetary-scale rasters and vectors. Sign up with your `@stanford.edu`; approval can take a bit—do it now.
- **Lab machines**: available if your laptop struggles; see lab/office-hour times.

## Logistics (from the lecture)
- Labs are drop-in style; we’ll publish times (Doodle if needed). Office hours are in the Stanford Geospatial Center (Mitchell Building).
- Slack: `stanford-geospatial.slack.com` channel for quick help; Canvas hosts the weekly lab videos.
- Live slides: `slides.com/staceymaples/spatial101/live` if you want to follow along during class.

## Quick start checklist
1) Install QGIS; enable Whitebox Tools plugin.
2) Request Google Earth Engine access with `@stanford.edu`.
3) Join the Slack workspace; bookmark `gis.stanford.edu` for data/support.
4) Skim the week’s labs; they’re self-paced and beginner-friendly.

## Micro-example to frame the week
- Same place, four models: a **point** (sensor), a **line** (bike route), a **polygon** (campus boundary), and a **raster** (elevation). Each answers different questions: where is it, how to get there, what’s inside, how steep is it.

## Accessibility notes for any slide images you reuse
- Add alt text and a 1–2 sentence caption per image. Call out what red boxes/circles highlight (titles, metadata panes, zones, labels). Describe key numbers/trends if showing dashboards.

## What’s next
- Proceed to [week01/00_introduction_to_formats_and_finding_data.md](00_introduction_to_formats_and_finding_data.md) for formats and data hunting.
- Then [week01/01_introducing_google_earth_engine_with_coordinates_and_pixels.md](01_introducing_google_earth_engine_with_coordinates_and_pixels.md) for coordinates & pixels, and [week01/02_projection_error_measurement_with_qgis.md](02_projection_error_measurement_with_qgis.md) to see why projections matter for measurement.
