# Spatial Data Format of the Week: Esoteric & Legacy Formats You'll Still Encounter

## The Format Museum

Welcome to the **spatial data format hall of fame**—formats you probably won't create, but you'll definitely encounter in legacy datasets, institutional archives, and "that one professor's hard drive."

This week covers:
1. **Personal Geodatabase (.mdb)** — Microsoft Access-based (deprecated)
2. **TAB (MapInfo)** — Pre-shapefile competitor
3. **E00 (Arc/INFO Export)** — Ancient Esri format
4. **KML/KMZ (Google Earth)** — Visualization format with analysis limitations
5. **DXF/DWG (CAD)** — Engineering drawings as GIS data
6. **GML (Geography Markup Language)** — XML-based OGC standard
7. **Miscellaneous Oddities**

---

## 1. Personal Geodatabase (.mdb)

**What it is**: Microsoft Access database with spatial extensions. **Deprecated by Esri** (don't use for new projects).

### Why It Exists

- **Pre-2000**: Esri's "modern" format before File Geodatabase
- **Built on Access**: Familiar to Windows users
- **Single file**: Unlike shapefiles

### Why It's Dead

**Problems**:
- **2GB size limit** (Access limitation)
- **Windows-only** (no Linux/Mac support)
- **32-bit only** (compatibility nightmare)
- **Corrupt easily** (Access reliability issues)
- **Deprecated**: Esri stopped supporting in 2016

### When You'll Encounter It

- **Old projects** (pre-2010 vintage)
- **Legacy archives** (university/government data)
- **"That one guy's workflow"** who refuses to upgrade

### Handling .mdb Files

**QGIS:**
1. Try opening with **Add Vector Layer** (GDAL driver)
2. **If it fails**: Open in old ArcGIS, export to .gdb or shapefile

**Conversion:**
```bash
ogr2ogr -f "ESRI Shapefile" output.shp input.mdb layer_name
```

**Recommendation**: **Convert immediately** to GeoPackage or shapefile. .mdb is a ticking time bomb (corrupts, platform issues, no future support).

---

## 3. MapInfo TAB Format

**What it is**: Vector format from MapInfo Professional—the GIS that almost beat Esri in the 1990s.

### Structure

**Four files** (like shapefiles):
- `.tab` — Metadata (coordinate system, symbology)
- `.dat` — Attribute data (dBASE format)
- `.map` — Spatial objects (geometry)
- `.id` — Index file

### When You'll Encounter It

- **Non-U.S. markets** (MapInfo strong in Europe, Australia)
- **Telecommunications** (utilities, infrastructure)
- **Legacy datasets** (1990s-2000s)
- **Government data** (countries where MapInfo was standard)

### Working with TAB in QGIS

**Import:**
1. **Add Vector Layer** → Select `.tab` file
2. QGIS uses GDAL MapInfo driver
3. Works reliably

**Export to TAB:**
1. **Save Features As** → Format: **MapInfo TAB**
2. QGIS can write TAB format

### Peculiarities

- **Coordinate systems**: Uses MapInfo's projection codes (not EPSG)
- **Symbology**: Stores styling in `.tab` file (rare for GIS formats)
- **Readable**: Text-based metadata (unlike shapefiles)

**Recommendation**: TAB format is fine—reads/writes reliably, but **shapefile or GeoPackage preferred** for new work (wider support).

---

## 4. E00 (Arc/INFO Export Format)

**What it is**: ASCII text format for Arc/INFO (pre-ArcGIS Esri software, 1980s-1990s).

### Why It Exists

- **Portability**: Transfer data between Arc/INFO systems
- **Platform-independent**: Text format (readable on any OS)
- **Archive format**: Long-term storage

### Structure

**Plain text** (you can open in text editor):
```
ARC  2
         1         1         1         1         1         1
  3.65489100E+02  2.08455100E+02
  3.65489800E+02  2.08454900E+02
 -1.00000000E+00
```

**Painful to parse manually** (use tools).

### When You'll Encounter It

- **Very old datasets** (1980s-2000s)
- **Government archives** (USGS, state agencies)
- **Historical GIS projects**

### Converting E00

**QGIS** doesn't open E00 directly.

**Command-line (GDAL):**
```bash
ogr2ogr -f "ESRI Shapefile" output.shp input.e00
```

**ArcGIS:**
1. **ArcToolbox → Conversion Tools → Import from Interchange File**
2. Converts E00 to coverage or shapefile

**Recommendation**: Treat E00 as **archaeological artifact**—convert to modern format immediately.

---

## 5. KML/KMZ (Google Earth)

**What it is**: Keyhole Markup Language, Google Earth's native format. Designed for **3D visualization and storytelling**, not spatial analysis.

### Why KML is Problematic for GIS

**Born for visualization:**
- Excellent for **Google Earth** tours and presentations
- Beautiful 3D buildings, flyovers, time animations
- Rich media (photos, videos, HTML descriptions)

**But analysis-hostile:**
- ❌ **No spatial analysis tools** — Google Earth is viewer-only (no buffer, overlay, network analysis)
- ❌ **WGS 84 only** — Can't use projected coordinate systems (measurements imprecise)
- ❌ **Limited attributes** — Data stored as Name/Description HTML blobs, not structured tables
- ❌ **No spatial indexing** — Slow for large datasets (>10,000 features)
- ❌ **Software ecosystem limited** — Google Earth-centric, not GIS-native

### When You'll Encounter It

**Public data sharing:**
- Government agencies share field sites as KML
- Historical markers, tourist attractions
- Environmental monitoring sites

**GPS device exports:**
- Garmin, Magellan devices export to KML
- Google Maps "Save place" → Export as KML

**Academic presentations:**
- Professors share research sites via KML
- Easy to email, everyone has Google Earth

**Legacy web mapping:**
- Pre-2010 web maps used KML overlays
- Replaced by GeoJSON in modern workflows

### The KML vs. KMZ Distinction

**KML (.kml)**:
- Plain XML text file
- Human-readable (like GeoJSON)
- No embedded images

**KMZ (.kmz)**:
- **Zipped KML** + images, 3D models, icons
- Smaller file size (compressed)
- Standard for sharing

### Working with KML in QGIS

**Import:**
1. **Drag `.kml` or `.kmz`** into QGIS
2. QGIS converts to temporary vector layer
3. **Note**: 3D features flattened to 2D

**Export to KML:**
1. Right-click layer → **Export → Save Features As**
2. Format: **Keyhole Markup Language (KML)**
3. **CRS**: Automatically converts to WGS 84 (EPSG:4326)
4. Options:
   - **Name field**: Attribute for placemark names
   - **Description field**: Attribute for pop-up HTML
   - **Altitude mode**: Clamp to ground (most common)

**Pro tip**: Create HTML description before export:
```sql
-- Field Calculator
'<b>Site:</b> ' || "site_name" || '<br>' ||
'<b>Type:</b> ' || "site_type" || '<br>' ||
'<img src="' || "photo_url" || '" width="300px"/>'
```

### Why KML Keeps Showing Up

**Universal recognition:**
- Everyone knows Google Earth
- Zero training required (point, click, explore)
- Works on phones, tablets, desktops

**N7 GIS software needed:**
- Email a KMZ file → recipient opens in Google Earth
- No QGIS, no ArcGIS, no technical skills

**Narrative cartography:**
- Tours with timed camera movements
- Historical maps as overlays
- 3D building extrusions for urban visualization

### The KML Trap

**Problem scenario:**
1. Government agency shares "spatial data" as KML
2. You download, try to analyze in QGIS
3. Discover:
   - No attributes (just Name/Description HTML)
   - Can't buffer (WGS 84 lat/lon issues)
   - No relationship to other datasets
   - Must convert, clean, georectify

**Resolution:**
1. **Import to QGIS**
2. **Parse HTML descriptions** to extract attributes
3. **Reproject** to appropriate CRS for analysis
4. **Export to shapefile or GeoPackage**
5. **Now** you can do real GIS work

### KML for What It's Good At

**Use KML when:**
- Sharing locations with **non-GIS audiences**
- 3D visualization (buildings, terrain draping)
- Storytelling (tours, historical overlays)
- Public outreach and engagement
- Mobile field reference (Google Earth on phones)

**Never use KML for:**
- Spatial analysis (buffer, overlay, network)
- Quantitative measurements (distance, area)
- Professional GIS workflows
- Attribute-heavy datasets
- Large datasets (>5,000 features = slow)

### Conversion Strategies

**KML → Shapefile:**
```bash
ogr2ogr -f "ESRI Shapefile" output.shp input.kml
```

**KML → GeoJSON:**
```bash
ogr2ogr -f GeoJSON output.geojson input.kml
```

**KML → GeoPackage:**
```bash
ogr2ogr -f GPKG output.gpkg input.kml
```

**In QGIS:**
1. Load KML layer
2. Right-click → **Export → Save Features As**
3. Choose modern format (GeoPackage, Shapefile, GeoJSON)

###8KML Bottom Line

KML is the **"PowerPoint of spatial data"**—excellent for presentations, terrible for analysis. Google Earth is a **viewer**, not a GIS. When you receive KML, **convert immediately** to a format designed for spatial analysis.

**Key insight**: KML's ubiquity is both strength and weakness. It's everywhere because Google Earth is free and easy. But "easy to view" ≠ "suitable for analysis."

**Modern alternative**: For web sharing, use **GeoJSON** + web mapping libraries (Leaflet, MapLibre). For analysis, use **GeoPackage** or **Shapefile**. Reserve KML for Google Earth-specific visualization projects.

---

## 6. DXF/DWG (AutoCAD)

**What it is**: Drawing formats from **AutoCAD** (engineering/architecture software), often used for cadastral data, infrastructure plans, and survey work.

### Why Engineers Use CAD, Not GIS

**CAD paradigm**:
- **Precise drafting** (millimeter accuracy)
- **Layers**: Organize by drawing element (line weight, color)
- **No coordinate systems**: Local grid or engineering coordinates
- **Symbology**: Stored as drawing style, not attributes

**GIS paradigm**:
- **Geographic reference**: Lat/lon, projected CRS
- **Layers**: Organize by **feature type** (roads, buildings)
- **Attributes**: Data tables linked to features

### When You'll Encounter CAD

- **Cadastral data**: Property boundaries, parcel maps
- **Infrastructure**: Utility networks, pipelines, roads (as-built)
- **Survey data**: Land surveying, construction sites
- **Building footprints**: Architectural site plans

### The DXF vs. DWG Problem

**DWG (.dwg)**:
- **Proprietary Autodesk format**
- Binary, compressed
- Requires AutoCAD or Autodesk license for full access

**DXF (.dxf)**:
- **Open exchange format** (ASCII or binary)
- Readable by most CAD/GIS software
- Lower precision, larger files

### Working with CAD in QGIS

**Import DXF:**
1. **Add Vector Layer** → Select `.dxf` file
2. QGIS imports as **multiple layers**:
   - `entities`: All features
   - `polylines`: Line features
   - `polygons`: Closed shapes
   - `points`: Point features
   - `annotations`: Text labels

**Problems**:
- **No CRS**: Coordinates usually in local grid (feet, meters)
- **Attributes minimal**: CAD "attributes" != GIS attributes
- **Styling**: Colors/line weights, not meaningful symbology
- **Topology**: Gaps, overlaps common (CAD drafting not spatially precise)

### Georeferencing CAD Data

**If coordinates are geographic** (rare):
1. Assign CRS: Layer Properties → CRS → Set to EPSG code

**If coordinates are local grid**:
1. Use **Georeferencer** plugin
2. Match CAD control points to known geographic coordinates
3. Transform to geographic CRS

### Converting CAD to GIS

```bash
ogr2ogr -f "ESRI Shapefile" output.shp input.dxf -s_srs EPSG:2227 -t_srs EPSG:4326
```

**Best practice**:
1. Import DXF to QGIS
2. Assign CRS (if known)
3. Clean topology (fix gaps, overlaps)
4. Add meaningful attributes
5. Export to shapefile/GeoPackage

**Recommendation**: CAD data requires **significant cleaning** before GIS use. Budget time for data prep.

---

## 6. GML (Geography Markup Language)

**What it is**: XML-based OGC standard for encoding geographic features.

### Structure

**Verbose XML:**
```xml
<gml:FeatureCollection>
  <gml:featureMember>
    <app:County>
      <app:name>Santa Clara County</app:name>
      <app:population>1936259</app:population>
      <app:geometry>
        <gml:Polygon>
          <gml:exterior>
            <gml:LinearRing>
              <gml:posList>
                -122.0 37.0 -121.0 37.0 -121.0 38.0 -122.0 38.0 -122.0 37.0
              </gml:posList>
            </gml:LinearRing>
          </gml:exterior>
        </gml:Polygon>
      </app:geometry>
    </app:County>
  </gml:featureMember>
</gml:FeatureCollection>
```

### Why It Exists

- **OGC standard**: Official geography encoding
- **Interoperability**: Cross-platform data exchange
- **Web services**: WFS responses often GML
- **Government mandates**: Some countries require GML

### Why You Won't Use It

**Problems**:
- **Extremely verbose**: 10-100× larger than shapefile/GeoJSON
- **Slow parsing**: XML overhead
- **Human-unreadable**: Too complex
- **Better alternatives**: GeoJSON simpler, faster, smaller

### When You'll Encounter It

- **WFS service responses** (government data portals)
- **INSPIRE directive** (European spatial data infrastructure)
- **Official government datasets** (mandated format)

### Handling GML

**QGIS:**
1. **Add Vector Layer** → Select `.gml` file
2. QGIS handles transparently (GDAL driver)

**Convert to useful format:**
```bash
ogr2ogr -f GeoJSON output.geojson input.gml
```

**Recommendation**: **Immediately convert** to GeoJSON or GeoPackage. GML is technically correct but painful to work with.

---

## 7. Miscellaneous Oddities

### SpatiaLite (.sqlite)

- **SQLite + spatial extensions** (predecessor to GeoPackage)
- **Open-source** (unlike Esri geodatabase)
- **QGIS native support**
- **Use GeoPackage instead** (newer, better standard)

### Coverage (Arc/INFO Coverage)

- **Ancient Esri format** (1980s-1990s)
- **Directory structure** (like .gdb but older)
- **Topology-enabled** (advanced spatial relationships)
- **Convert to shapefile/geodatabase** if encountered

### TIGER/Line Files (.shp with weird attributes)

- **U.S. Census Bureau format**
- **Shapefiles with cryptic field names** (`STATEFP`, `COUNTYFP`, `TRACTCE`)
- **Topologically integrated** (edges, nodes, faces)
- **Documentation required** to understand attributes

### Lidar Formats (LAS/LAZ)

- **Not strictly "GIS formats"** (point clouds)
- **LAS**: Uncompressed lidar points
- **LAZ**: Compressed LAS (use this)
- **QGIS support**: Install Point Cloud Processing plugin
- **Week 06 material** (terrain/3D analysis)

---

## The Bottom Line

**You won't create these formats**, but you'll encounter them in:
- Legacy archives (government, university, corporate)
- Multi-platform workflows (engineers using CAD, scientists using .gdb)
- International data portals (GML mandates)
- Old projects revived after years

**Best practice**: **Convert to modern open formats immediately**:
- **.gdb → GeoPackage** (open standard, similar features)
- **.mdb → GeoPackage** (escape the Access nightmare)
- **.tab → Shapefile or GeoPackage** (wider support)
- **.e00 → Shapefile** (modernize ancient data)
- **.dxf → Shapefile + cleaning** (CAD ≠ GIS)
- **.gml → GeoJSON** (readability, performance)

**Key takeaway**: Spatial data formats are like **archaeological layers**—each era leaves its mark. Modern GIS is built on decades of legacy formats. Knowing how to convert them is survival skill.

## See Also

- Week 00: Shapefile (the workhorse)
- Week 01: GeoJSON (modern web standard)
- Week 03: GeoPackage (modern database alternative)
- [GDAL Format List](https://gdal.org/drivers/vector/index.html) — Comprehensive format support
- [ogr2ogr](https://gdal.org/programs/ogr2ogr.html) — Universal conversion tool
- [File Geodatabase API](https://github.com/Esri/file-geodatabase-api) — Read .gdb programmatically
