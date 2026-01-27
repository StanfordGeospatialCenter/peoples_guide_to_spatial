# Great Circles and More Cartographic Tips & Tricks in QGIS

## Introduction

Great circles are the shortest paths between two points on a sphere—critical for understanding global-scale spatial relationships like airline routes, migrations, and long-distance connections. This lab uses airline hub networks to teach both **Dana Tomlin's Cartographic Model Framework** (the conceptual pipeline for all GIS operations) and QGIS's powerful vector processing tools.

By building an airline hub map with great circle routes, you'll learn how geographic data flows through representation, processing, and analysis stages—while mastering QGIS's geometry tools, attribute joins, and advanced styling.

## Learning Objectives

By the end of this lab, you will be able to:

- Explain Tomlin's cartographic model framework (representation → processing → analysis → communication)
- Create great circle lines between geographic points in QGIS
- Build and manage spatial data through the full GIS workflow
- Apply advanced cartographic styling (graduated symbols, transparency, blending modes)
- Understand why flat maps distort great circle routes
- Generate professional aviation-style hub maps

## 1. Tomlin's Cartographic Model Framework

### 1.1 The Four-Stage Pipeline

Dana Tomlin's framework breaks every GIS operation into four conceptual stages:

**1. Representation** — How we model reality as data
- Points for airport locations (latitude/longitude)
- Lines for flight routes (connecting point pairs)
- Attributes for airport names, codes, passenger counts

**2. Processing** — Transforming and generating new geographic data
- Creating great circle lines from point pairs
- Calculating route distances
- Buffering flight paths
- Joining attribute tables

**3. Analysis** — Extracting meaningful patterns and relationships
- Counting routes per hub
- Measuring total network distance
- Finding shortest paths
- Identifying hub centrality

**4. Communication** — Presenting results effectively
- Cartographic design (color, symbols, hierarchy)
- Layout composition (legend, scale, title)
- Export formats (PNG, PDF, web maps)

### 1.2 Why This Matters

Every GIS workflow follows this pipeline, whether you're:
- Analyzing disease spread (point locations → buffer zones → risk analysis → choropleth map)
- Planning infrastructure (road network → service areas → accessibility scores → suitability map)
- Tracking environmental change (satellite pixels → classification → change detection → time series chart)

Understanding this framework helps you think systematically about spatial problems and choose the right tools for each stage.

## 2. Great Circles vs. Rhumb Lines

### 2.1 The Geometry of Global Navigation

**Great Circles:**
- Shortest distance between two points on a sphere
- Created by intersecting a plane through Earth's center
- Constantly changing bearing (except along equator/meridians)
- Used by long-distance flights and ships

**Rhumb Lines (Loxodromes):**
- Constant compass bearing between two points
- Longer distance than great circles (except along equator/meridians)
- Appear as straight lines on Mercator projections
- Used for short-distance navigation

**Why Flat Maps Lie:**
- On a Web Mercator map, great circles appear curved
- This isn't distortion—it's accurate representation of spherical geometry
- The "straight" line on a flat map is actually longer than the curved great circle

### 2.2 Real-World Example: SFO to Dubai

Let's visualize why airlines fly "curved" paths:

- **Great circle distance**: ~8,100 miles (over the Arctic)
- **Rhumb line distance**: ~8,400 miles (constant eastward bearing)
- **Fuel savings**: ~300 miles = significant cost reduction
- **Flight time**: ~45 minutes saved

## 3. Building Your Airline Hub Map in QGIS

This lab follows Tomlin's framework from data creation through final map communication.

### 3.1 REPRESENTATION: Creating Airport Hub Data

**Step 1: Create a Point Layer for Airports**

1. In QGIS, go to **Layer → Create Layer → New GeoPackage Layer**
2. Name the database: `airline_network.gpkg`
3. Set layer name: `hub_airports`
4. Set geometry type: **Point**
5. Set CRS: **EPSG:4326 - WGS 84** (geographic coordinates)

**Add these attribute fields:**
- `airport_code` (Text, length 3) — IATA code
- `airport_name` (Text, length 50) — Full name
- `city` (Text, length 30) — City name
- `country` (Text, length 30) — Country name
- `passengers` (Integer) — Annual passenger count

**Step 2: Digitize Major Hub Airports**

Use the **Toggle Editing** tool and **Add Point Feature** to create airports. Research actual coordinates using Wikipedia or [ourairports.com](http://ourairports.com).

Suggested major hubs:
- **SFO** - San Francisco International (37.619, -122.375)
- **DXB** - Dubai International (25.253, 55.364)
- **LHR** - London Heathrow (51.470, -0.454)
- **SIN** - Singapore Changi (1.350, 103.994)
- **JFK** - New York JFK (40.640, -73.779)
- **NRT** - Tokyo Narita (35.765, 140.386)
- **SYD** - Sydney Airport (-33.946, 151.177)
- **GRU** - São Paulo Guarulhos (-23.432, -46.469)
- **MEX** - Mexico City Airport (19.436, -99.072)
- **JNB** - Johannesburg OR Tambo (-26.133, 28.242)

**Step 3: Populate Attributes**

For each airport point, fill in the attribute table with:
- IATA code (SFO, DXB, etc.)
- Full airport name
- City and country
- Approximate annual passengers (use realistic millions: 50M-90M for major hubs)

### 3.2 PROCESSING: Creating Great Circle Routes

**Step 4: Create Route Connections**

Now we generate lines between airports. This demonstrates **geometry processing**.

1. Go to **Processing Toolbox** (Ctrl/Cmd+Alt+T)
2. Search for **"Create hub lines"**
3. Run **Vector Creation → Create hub lines** with parameters:
   - Hub point layer: `hub_airports`
   - Spoke point layer: `hub_airports` (same layer)
   - Hub ID field: `airport_code`
   - Hub layer name attribute: `airport_name`
   - Output: Save as `routes` in `airline_network.gpkg`

Wait—this creates *every possible connection*! For a hub model, we want routes radiating from one central hub.

**Better Approach: Hub-and-Spoke Model**

**Option A: Manual Route Creation**

1. Create new line layer: **Layer → Create Layer → New GeoPackage Layer**
2. Set geometry type: **LineString**
3. Set CRS: **EPSG:4326**
4. Add fields:
   - `origin` (Text) — Origin airport code
   - `destination` (Text) — Destination airport code
   - `distance_km` (Decimal) — Great circle distance

5. Use **Add Line Feature** tool to digitize routes
6. Click origin airport, then destination airport
7. Repeat for all hub-spoke routes

**Option B: Processing with Expression (Advanced)**

If you have origin-destination pairs in a CSV:
1. Import CSV with columns: `origin_code, dest_code`
2. Use **Geometry by Expression** to create lines:

```
make_line(
    geometry(get_feature('hub_airports', 'airport_code', "origin_code")),
    geometry(get_feature('hub_airports', 'airport_code', "dest_code"))
)
```

**Step 5: Convert to Great Circles**

Standard LineString geometry connects points with *straight lines in the CRS*. For true great circles:

1. Open **Processing Toolbox**
2. Run **Vector Geometry → Geodesic line split at antimeridian**
   - Input: `routes` layer
   - Output: `routes_greatcircle` in `airline_network.gpkg`

This densifies vertices along the geodesic (spherical) path and handles routes crossing the antimeridian (date line).

**Step 6: Calculate Great Circle Distances**

Add distance calculations to routes:

1. Open `routes_greatcircle` attribute table
2. Toggle editing
3. Open **Field Calculator**
4. Create new field `distance_km` (Decimal, precision 2)
5. Use expression:

```
length($geometry) / 1000
```

This calculates geodesic length in kilometers (QGIS automatically computes great circle distance when working in EPSG:4326).

### 3.3 ANALYSIS: Network Metrics

**Step 7: Count Routes per Hub**

Determine hub centrality by counting connections:

1. Open `hub_airports` attribute table
2. Add field `route_count` (Integer)
3. Use expression:

```
aggregate(
    layer:='routes_greatcircle',
    aggregate:='count',
    expression:=1,
    filter:="origin" = attribute(@parent, 'airport_code')
)
```

This counts how many routes originate from each hub.

**Step 8: Calculate Total Network Distance**

For entire network summary:

1. Open **Processing Toolbox → Vector Analysis → Basic statistics for fields**
2. Select `routes_greatcircle` layer
3. Choose field: `distance_km`
4. Review output: sum, mean, max, min distances

### 3.4 COMMUNICATION: Cartographic Design

**Step 9: Style Airport Points (Hub Emphasis)**

Create visual hierarchy based on route count:

1. Right-click `hub_airports` → Properties → Symbology
2. Choose **Graduated** renderer
3. Value: `route_count`
4. Method: **Natural Breaks (Jenks)**
5. Symbol: Simple marker, circle
6. Size range: 3mm to 10mm
7. Color: Single hue with varying saturation (e.g., orange to red)
8. Add **Drop Shadow** effect for depth

**Step 10: Style Great Circle Routes (Layered Transparency)**

Create aviation-style route visualization:

1. Right-click `routes_greatcircle` → Properties → Symbology
2. Use **Simple Line** with:
   - Color: Bright accent (cyan, yellow, or orange)
   - Width: 0.5mm
   - Opacity: 30%
   - Blend mode: **Addition** or **Screen** (overlapping routes glow brighter)

**Advanced: Graduated Routes by Distance**

For routes styled by length:
1. Change to **Graduated** renderer
2. Value: `distance_km`
3. Width range: 0.3mm to 1.5mm (longer = thicker)
4. Color ramp: Sequential (light to dark)

**Step 11: Basemap Selection**

Choose projection and basemap for context:

**For Global Hub Networks:**
- Projection: **World Robinson** (ESRI:54030) or **Winkel Tripel**
- Basemap: Dark canvas (Natural Earth II with reduced saturation) or **QGIS → XYZ Tiles → CartoDB Dark Matter**

**For Regional Hub:**
- Projection: **Appropriate regional UTM** or **Azimuthal Equidistant** centered on hub
- Basemap: Light/neutral OSM or Esri World Gray Canvas

Apply basemap:
1. **Browser Panel → XYZ Tiles → Add CartoDB Dark Matter**
2. Or add **QuickMapServices plugin** for more options
3. Adjust layer order: Basemap → Routes → Airports

**Step 12: Create Print Layout**

Professional map composition:

1. **Project → New Print Layout**
2. Name: "Global Aviation Hub Network"
3. Add map canvas: **Add Item → Add Map**
4. Add title: Use bold sans-serif font, 24pt
5. Add legend:
   - Customize labels (remove layer names if redundant)
   - Use simple symbols
   - Group by hub size categories
6. Add scale bar: Choose graphic style, set units to kilometers
7. Add graticule (optional): Right-click map → Item Properties → Grids → Add Grid
8. Add data sources text: "Airport data: OurAirports.com | Basemap: © OpenStreetMap contributors"
9. Add production credits: Your name, date, course

**Export:**
- **Layout → Export as Image** (PNG at 300 DPI)
- **Layout → Export as PDF** (for printing)

## 4. Extensions & Advanced Techniques

### 4.1 Animated Hub Evolution

Use **QGIS Temporal Controller** to show route growth over time:

1. Add `year_established` field to routes
2. Enable temporal properties with start/end years
3. Animate playback showing network expansion

### 4.2 3D Globe Visualization

Use **Qgis2threejs plugin** to render routes on a 3D sphere:

1. Install plugin via **Plugins → Manage and Install Plugins**
2. Configure globe with DEM = 0 (sphere)
3. Style routes with altitude offset for visual effect
4. Export as WebGL for interactive presentation

### 4.3 Integration with Flight Data APIs

Connect to real-time flight data:

1. Use **OpenSky Network API** or **FlightAware API**
2. Query routes between airport pairs
3. Import JSON responses as vector layers
4. Automate updates with Python plugin

### 4.4 Network Analysis

Use QGIS Network Analysis tools:

1. **Processing → QNEAT3 plugin** for hub-based accessibility
2. Calculate shortest paths between hub pairs
3. Generate service areas (reachable airports within N connections)
4. Identify critical connector hubs

## 5. Reflection: Mapping Tomlin's Framework to QGIS Tools

Let's trace our lab workflow through Tomlin's stages:

| **Tomlin Stage** | **What We Did** | **QGIS Tools Used** |
|------------------|-----------------|---------------------|
| **Representation** | Created point layer for airports with coordinates and attributes | Create Layer, Add Feature, Attribute Table |
| **Processing** | Generated great circle lines from point pairs, calculated distances | Hub Lines, Geodesic Line Split, Field Calculator |
| **Analysis** | Counted routes per hub, computed network statistics | Aggregate expression, Basic Statistics |
| **Communication** | Styled by hub importance, created professional layout | Graduated Symbology, Print Composer, Export |

This pipeline applies to *every GIS workflow*—from ecology field surveys to urban planning to climate modeling. Mastering this conceptual model makes you a better spatial thinker, not just a better QGIS user.

## Conclusion

You've now built a sophisticated airline hub network map that demonstrates both conceptual rigor (Tomlin's framework) and technical skill (QGIS processing and cartography). Great circles reveal how spherical geometry differs from flat map intuition—the "curved" routes are actually the shortest paths.

Key takeaways:
- **All GIS work follows representation → processing → analysis → communication**
- **Great circles are spherical shortest paths** (geodesics)
- **QGIS processing tools transform geometry and attributes** systematically
- **Cartographic design creates visual hierarchy** (hub emphasis, route transparency, basemap contrast)

These skills extend far beyond aviation maps: any network visualization (trade routes, migrations, telecommunications, ecological corridors) uses the same geometric and analytical approaches. You now have a reusable template for hub-based spatial networks.

Next up: [Week 02](../week02/README.md) where we explore georeferencing and bringing historical maps into GIS workflows.
