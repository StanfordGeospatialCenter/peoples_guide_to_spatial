# Spatial Data Format of the Week: GPX (GPS Exchange Format)

## The Fitness Tracker's Native Language

**File extension**: `.gpx`

**What it is**: The standard XML-based format for GPS data, created specifically for exchanging routes, tracks, and waypoints between GPS devices and applications. If you've ever used Strava, Garmin Connect, AllTrails, or your smartphone's fitness app, you've created GPX files.

## Born for Movement

GPX was designed around a simple question: **"How do I share where I've been with someone else?"**

### Three Core GPX Data Types

**1. Waypoints** — Individual points of interest
```xml
<wpt lat="37.619" lon="-122.375">
  <name>San Francisco Airport</name>
  <ele>4.0</ele>
  <time>2024-01-15T08:30:00Z</time>
</wpt>
```

**2. Tracks** — Your actual path with timestamps (what you recorded)
```xml
<trk>
  <name>Morning Run - Golden Gate Park</name>
  <trkseg>
    <trkpt lat="37.7694" lon="-122.4862">
      <ele>15.2</ele>
      <time>2024-01-15T07:00:00Z</time>
    </trkpt>
    <trkpt lat="37.7695" lon="-122.4863">
      <ele>15.8</ele>
      <time>2024-01-15T07:00:05Z</time>
    </trkpt>
  </trkseg>
</trk>
```

**3. Routes** — Planned paths (what you intend to follow)
```xml
<rte>
  <name>Half Dome Trail</name>
  <rtept lat="37.7459" lon="-119.5332">
    <name>Trailhead</name>
  </rtept>
  <rtept lat="37.7459" lon="-119.5332">
    <name>Summit</name>
  </rtept>
</rte>
```

## Common Data Sources

### Fitness & Activity Tracking
- **Strava**: Export individual activities or bulk download
- **Garmin Connect**: Routes, activities, courses
- **AllTrails**: Hiking trail recordings and planned routes
- **Komoot**: Bike touring and adventure planning
- **Ride with GPS**: Cycling routes and navigation

### Smartphone Apps
- **iPhone Health/Fitness**: Walking, running, cycling routes
- **Google Fit**: Android activity tracking
- **Gaia GPS**: Backcountry navigation and waypoint recording
- **Organic Maps**: Offline hiking navigation

### Handheld GPS Devices
- Garmin, Magellan, TomTom devices
- Dedicated hiking/marine GPS units
- Survey-grade GNSS receivers

## What Makes GPX Special

### Temporal Tracking
Every trackpoint includes a **timestamp**, enabling:
- Speed calculations: distance ÷ time between points
- Elevation profiles: altitude changes over time
- Activity duration and pace analysis
- Temporal animations in GIS

### Elevation Data
Includes vertical dimension (often from barometric altimeter or GPS):
```xml
<ele>1524.3</ele>  <!-- Elevation in meters -->
```

### Rich Metadata
Beyond coordinates, GPX stores:
- Activity type (running, cycling, hiking)
- Heart rate, cadence, power (from sensors)
- Description and notes
- Creator software information

## Analyzing GPX in QGIS

### 1. Import GPX File

**Method A: Drag and Drop**
1. Drag `.gpx` file into QGIS
2. Select layer type: **tracks**, **routes**, or **track_points**

**Method B: Add Vector Layer**
1. **Layer → Add Layer → Add Vector Layer**
2. Source: Select `.gpx` file
3. Choose sublayer (tracks, routes, waypoints)

### 2. Common Analyses

**Calculate Total Distance:**
```python
# In Field Calculator
$length  # For line layers in layer CRS units
```

**Calculate Average Speed (requires time field):**
1. Convert track points to line
2. Add field: `distance = $length`
3. Calculate time difference between points
4. Speed = distance ÷ time

**Elevation Gain:**
1. Use **Profile Tool** plugin
2. Or sum positive elevation changes in track points

**Create Activity Heatmap:**
1. Collect multiple GPX files in one folder
2. Use **Heatmap (Kernel Density Estimation)** on track points
3. Style by activity frequency

## Converting GPX for Analysis

### To Shapefile/GeoJSON
1. **Vector → GPS → GPS Tools**
2. Or right-click layer → Export → Save Features As
3. Choose format (Shapefile, GeoJSON, GeoPackage)

### From CSV to GPX
If you have lat/lon/time data in CSV:
1. Import CSV as delimited text layer
2. Use **GPS Tools → Create GPX** (if available)
3. Or export to KML, then convert to GPX online

## Common Use Cases in GIS

### 1. Trail Mapping
- Collect hiking trail data with smartphone
- Import to QGIS for cartographic display
- Overlay on satellite imagery for trail maintenance

### 2. Field Data Collection
- Record sample site locations with GPS app
- Add waypoints with photos and notes
- Import to QGIS for spatial analysis

### 3. Activity Pattern Analysis
- Analyze running routes for urban planning
- Identify popular recreational corridors
- Compare planned vs. actual routes

### 4. Volunteer Geographic Information (VGI)
- Community trail mapping projects
- OpenStreetMap contributions
- Citizen science data collection

## GPX Limitations for GIS

### 1. Not Designed for Analysis
- Optimized for navigation and exchange
- No spatial indexing
- XML parsing overhead

### 2. Coordinate System
- **Always WGS 84 (EPSG:4326)** latitude/longitude
- No projection information needed (standardized)
- May require reprojection for local analysis

### 3. Large File Sizes
- XML text format is verbose
- High-frequency GPS logging creates massive files
- 1-second interval track = 3,600 points per hour

### 4. Limited Attribute Structure
- Designed for GPS-centric data
- Not ideal for complex non-temporal attributes
- Better formats exist for non-GPS field data

## When to Use GPX

**Best for:**
- Recording movement with GPS devices
- Fitness and recreation tracking
- Field data collection with smartphones
- Sharing routes and waypoints
- Temporal analysis with timestamps
- Elevation profile analysis

**Avoid when:**
- Static point data without temporal component
- Large-scale spatial databases
- Complex attribute relationships
- Non-GPS spatial data
- Performance-critical applications

## Processing Tips

### Simplify Dense Tracks
High-frequency GPS creates unnecessary detail:
1. **Vector → Geometry Tools → Simplify**
2. Use Douglas-Peucker algorithm
3. Tolerance: 0.0001° (≈10m) for most applications

### Clean GPS Noise
GPS signal often includes erratic points:
1. Filter by **speed** (remove impossible velocities)
2. Remove **elevation outliers** (barometric noise)
3. Smooth tracks with **moving average**

### Batch Process Multiple Files
Collect many GPX files:
1. Use **Processing → Batch Processing**
2. Apply same workflow to folder of GPX files
3. Merge results with **Vector → Data Management Tools → Merge**

## The Bottom Line

GPX is the **universal format for GPS data**. If you're collecting spatial data in the field with consumer devices, you're creating GPX files. Learn to import, clean, and analyze them—they're the bridge between field work and desktop GIS.

**Key takeaway**: GPX files are "spatial data with timestamps"—perfect for anything that moves.

## See Also

- Week 00: Shapefile (convert GPX to this for analysis)
- Week 01: GeoJSON (modern alternative for web display)
- Week 03: CSV with lat/lon (simpler field data format)
- [GPX 1.1 Schema](https://www.topografix.com/GPX/1/1/) — Official specification
- [Garmin Connect](https://connect.garmin.com) — Export your activities
- [Strava](https://www.strava.com) — Bulk export all activities
