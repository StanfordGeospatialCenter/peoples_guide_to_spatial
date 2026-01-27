# Spatial Data Format of the Week: Shapefile

## The Universal Vector Format

**File extension**: `.shp` (plus companion files)

**What it is**: The most common vector data format in GIS, developed by Esri in the 1990s. Despite its limitations, it remains the de facto standard for vector data exchange.

## The Multi-File Reality

A shapefile is **never just one file**—it's actually a collection of 3-15+ files working together as a single dataset:

### Required Files (The Essential Three)
- **`.shp`** — The geometry (points, lines, polygons)
- **`.shx`** — The spatial index (links features to attribute records)
- **`.dbf`** — The attribute table (dBASE database format)

### Common Optional Files
- **`.prj`** — Projection information (coordinate reference system)
- **`.cpg`** — Character encoding (UTF-8, Windows-1252, etc.)
- **`.sbn`/`.sbx`** — Spatial index for faster queries
- **`.shp.xml`** — Metadata (ISO 19115 format)

**Critical rule**: All files must have the **same base filename** and be in the **same directory**.

Example: `california_counties.shp`, `california_counties.shx`, `california_counties.dbf`, `california_counties.prj`

## Why It's Still the Standard

### Advantages

**1. Universal Compatibility**
- Supported by every GIS software (QGIS, ArcGIS, Google Earth Engine, R, Python)
- Safe choice for data sharing across platforms
- Legacy system compatibility

**2. Spatial Indexing**
- Fast spatial queries on large datasets
- Efficient rendering and analysis
- Critical for professional GIS workflows

**3. Proven Reliability**
- 30+ years of battle-tested performance
- Stable format specification
- Extensive documentation and support

**4. Topology Support**
- Advanced geometry operations
- Spatial relationship preservation
- Complex spatial analysis capabilities

## The Infamous Limitations

### 1. The 2GB File Size Limit
- Each component file capped at 2GB
- Problem for high-resolution datasets or detailed geometries
- Workaround: Split into multiple files or use geodatabase

### 2. Field Name Restrictions
- Maximum **10 characters** for attribute field names
- No spaces or special characters allowed
- Leads to cryptic abbreviations: `pop_den_20` instead of `population_density_2020`

### 3. The 255 Field Limit
- Maximum 255 attribute columns
- Problematic for wide datasets
- Alternative: Use geodatabase or GeoPackage

### 4. No Mixed Geometry Types
- Points, lines, and polygons must be in separate files
- No single file with multiple geometry types
- Use multi-layer formats (GeoPackage, geodatabase) if needed

### 5. Limited Data Types
- Basic types only: Integer, Float, String, Date
- No support for arrays, JSON, or nested structures
- Maximum string length: 254 characters

## When to Use Shapefiles

**Best for:**
- Data sharing with unknown recipients
- Archival storage (stable, widely supported)
- Working with legacy systems
- Complex spatial analysis requiring topology
- Large datasets needing spatial indexing

**Avoid when:**
- Field names exceed 10 characters (use GeoPackage)
- File sizes approach 2GB (use geodatabase or GeoPackage)
- You need multiple geometry types in one file
- Web mapping applications (use GeoJSON instead)

## Creating Shapefiles in QGIS

1. **Right-click in Layers Panel** → Export → Save Features As
2. **Format**: ESRI Shapefile
3. **File name**: Choose base name (`.shp` added automatically)
4. **CRS**: Select appropriate coordinate system
5. **Encoding**: UTF-8 (recommended)
6. Click **OK**

**Result**: QGIS creates all required files automatically.

## Sharing Shapefiles

**Always zip all component files together:**

```bash
zip california_counties.zip california_counties.*
```

**Never share just the `.shp` file**—it's useless without its companions!

## Fun Shapefile Facts

- **Age**: Created in 1990s, older than most GIS students
- **Origin**: Esri proprietary format, later opened for public use
- **Ubiquity**: Billions of shapefiles exist worldwide
- **Persistence**: Despite alternatives, it refuses to die
- **File limit**: Windows MAX_PATH limitation (260 characters) often breaks before the 2GB limit

## The Bottom Line

Shapefiles are like the **USB-A connector of GIS**—clunky, outdated, with arbitrary limitations, but you'll encounter them everywhere. Learn to love their quirks, because they're not going away anytime soon.

**Key takeaway**: When in doubt, shapefile. It's the format that will work when nothing else does.

## See Also

- Week 01: GeoJSON (the modern alternative)
- Week 03: GeoPackage (the shapefile killer that still hasn't killed it)
- Week 10: Geodatabase (Esri's professional replacement)
