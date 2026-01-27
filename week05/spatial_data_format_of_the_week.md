# Spatial Data Format of the Week: Raster Image Formats for Spatial Analysis

## Understanding "Images" as Spatial Data

In GIS, an **image isn't just a picture**—it's a **georeferenced grid of measurements**. This week covers the core raster image formats you'll encounter in spatial analysis, from the universal GeoTIFF to legacy formats still hiding in old data archives.

**Core concept**: Every pixel in a spatial raster has a **location** (coordinates) and a **value** (measurement). The format determines how that information is stored and accessed.

---

## 1. GeoTIFF (.tif, .tiff) — The Universal Raster Standard

**What it is**: Tagged Image File Format (TIFF) with embedded spatial reference information. The most common format for **all** raster spatial data.

### Why GeoTIFF Dominates

A regular TIFF is just an image. A **GeoTIFF** adds:
- **Coordinate Reference System (CRS)**: What projection? What datum?
- **Geotransform**: Where in the world is this image? (pixel size, rotation, origin)
- **Bounding coordinates**: Geographic extent
- **NoData values**: Which pixels mean "no data here"?

**Result**: Self-contained spatial dataset—no separate metadata files needed.

### Multi-Band Structure

GeoTIFFs store **multiple bands** (layers) in one file:

**True color imagery (RGB):**
- Band 1: Red channel (0.63-0.69 µm)
- Band 2: Green channel (0.52-0.60 µm)
- Band 3: Blue channel (0.45-0.52 µm)

**Landsat 8 (multispectral):**
- Band 1: Coastal/Aerosol
- Band 2: Blue
- Band 3: Green
- Band 4: Red
- Band 5: NIR (near-infrared)
- Band 6-7: SWIR (shortwave infrared)
- Band 10-11: Thermal

**Digital Elevation Model (DEM):**
- Band 1: Elevation values (meters or feet)

### Common Sources

**Satellite imagery:**
- Landsat (30m): USGS EarthExplorer, Google Earth Engine
- Sentinel-2 (10m): Copernicus Hub, Google Earth Engine
- NAIP (1m): USGS, state GIS portals, Google Earth Engine

**Elevation data:**
- SRTM (30m global): USGS, CGIAR-CSI, Google Earth Engine
- USGS 3DEP (10m U.S.): National Map, Google Earth Engine
- LiDAR-derived DEMs: State/local portals

**Derived products:**
- Land cover classifications (NLCD, ESA WorldCover)
- Climate grids (PRISM, WorldClim)
- Vegetation indices (NDVI, EVI)

### Working with GeoTIFF in QGIS

**Load:**
1. **Drag and drop** `.tif` file into QGIS
2. Or: **Layer → Add Layer → Add Raster Layer**

**Inspect properties:**
- Right-click layer → **Properties**
- **Information tab**: CRS, extent, pixel size, band count, data type
- **Symbology tab**: Rendering (singleband gray, multiband color, paletted)
- **Histogram tab**: Data distribution

**Common operations:**
```
# Calculate NDVI (Raster Calculator)
("NIR@1" - "Red@1") / ("NIR@1" + "Red@1")

# Clip to study area
Raster → Extraction → Clip Raster by Extent

# Reproject
Raster → Projections → Warp (Reproject)

# Extract values at points
Processing → Raster analysis → Sample raster values
```

### Compression Options

**DEFLATE (lossless):**
```bash
gdal_translate -co COMPRESS=DEFLATE -co PREDICTOR=2 input.tif output.tif
```
- Best for: Elevation data, scientific measurements
- PREDICTOR=2: Better compression for continuous data

**LZW (lossless):**
```bash
gdal_translate -co COMPRESS=LZW input.tif output.tif
```
- Best for: Classified data (land cover, categorical rasters)

**JPEG (lossy):**
```bash
gdal_translate -co COMPRESS=JPEG -co JPEG_QUALITY=85 input.tif output.tif
```
- Best for: Imagery where visual quality matters more than exact values
- Quality 85-95 balances size and fidelity

### GeoTIFF Advantages

✅ **Universal support** — Every GIS software reads GeoTIFF  
✅ **Self-contained** — Embedded CRS, no separate `.prj` file  
✅ **Multi-band** — Store all spectral bands in one file  
✅ **Flexible compression** — Choose lossless or lossy  
✅ **BigTIFF support** — No 4GB limit  

### GeoTIFF Limitations

❌ **Large file sizes** — High-resolution imagery = multi-GB files  
❌ **Not web-optimized** — Full download required (see COG next week)  
❌ **Single CRS** — One projection per file  

### When to Use

**Use GeoTIFF for:**
- Desktop GIS analysis
- Satellite imagery processing
- Elevation models (DEMs)
- Archival storage
- Multi-band scientific data

---

## 2. JPEG with World File (.jpg + .jgw) — The Georeferenced Photo

**What it is**: Standard JPEG image + separate world file that provides spatial reference.

### The World File System

A **world file** is a tiny text file with 6 lines defining the geotransform:

**Example: `aerial_photo.jgw`**
```
0.5                  ← pixel width in map units (0.5 meters)
0.0                  ← rotation term (usually 0)
0.0                  ← rotation term (usually 0)
-0.5                 ← pixel height (negative = north-up)
123456.789           ← X coordinate of upper-left pixel center
987654.321           ← Y coordinate of upper-left pixel center
```

**File naming**:
- `image.jpg` → `image.jgw` (common)
- `image.jpg` → `image.jpgw` (alternative)

### Why JPEG + World File?

**Advantages:**
- **Small file sizes** — JPEG compression excellent for photos
- **Universal viewing** — Any image viewer opens JPEGs (non-GIS users)
- **Legacy standard** — Old aerial photos often distributed this way

**Disadvantages:**
- **Lossy compression** — Not suitable for analysis requiring exact values
- **No CRS embedded** — Requires separate `.prj` file for projection
- **No band metadata** — Typically 3-band RGB only

### Common Sources

- **Historical aerial photography** (pre-digital era scans)
- **Orthophotos** (georectified aerial photos)
- **Field photos** with GPS cameras
- **Exported web map images**

### Using JPEG+World in QGIS

**Load:**
1. Ensure **three files** present:
   - `photo.jpg` (image)
   - `photo.jgw` (world file)
   - `photo.prj` (projection, optional but recommended)
2. **Drag `photo.jpg`** into QGIS
3. QGIS automatically reads `.jgw` and `.prj`

**If CRS missing:**
- Right-click layer → **Set CRS**
- Choose appropriate coordinate system

**Export with world file:**
1. **Project → Import/Export → Export Map to Image**
2. Check **"Save world file"**
3. QGIS creates `.jpgw` automatically

### When to Use

**Use JPEG+World for:**
- Sharing georeferenced photos with non-GIS users
- Reducing file sizes for visual display (not analysis)
- Historical aerial photos (legacy format)
- Quick web map exports

**Avoid for:**
- Quantitative analysis (lossy compression)
- Multispectral imagery (limited to RGB)
- Scientific measurements

---

## 3. PNG with World File (.png + .pgw) — The Lossless Web Image

**What it is**: Portable Network Graphics image + world file. Like JPEG+World, but **lossless compression**.

### PNG vs. JPEG

| **Aspect**          | **JPEG**                  | **PNG**                       |
|---------------------|---------------------------|-------------------------------|
| **Compression**     | Lossy (visual quality)    | Lossless (exact pixels)       |
| **File size**       | Smaller                   | Larger                        |
| **Best for**        | Photos, natural imagery   | Graphics, screenshots, text   |
| **Transparency**    | No                        | Yes (alpha channel)           |
| **Color depth**     | 24-bit (16.7M colors)     | Up to 48-bit                  |

### World File Format

Same as JPEG world files:

**Example: `screenshot.pgw`**
```
1.0                  ← 1 meter per pixel
0.0
0.0
-1.0                 ← negative = north-up
500000.0             ← X coordinate (easting)
4000000.0            ← Y coordinate (northing)
```

**File naming**:
- `image.png` → `image.pgw` (common)
- `image.png` → `image.pngw` (alternative)

### Common Uses

**Web map exports:**
- Export styled QGIS map as PNG
- Maintain georeferencing with `.pgw`
- Lossless for text labels, sharp boundaries

**Screenshots of GIS software:**
- Capture analysis results
- Document workflows
- Share exact visual representation

**Graphics with spatial reference:**
- Logos on maps
- Legends with coordinates
- Transparent overlays

### Using PNG+World in QGIS

**Load:**
1. Ensure files present: `map.png`, `map.pgw`, `map.prj`
2. Drag `map.png` into QGIS
3. QGIS reads world file automatically

**Export with world file:**
1. **Project → Import/Export → Export Map to Image**
2. Choose **PNG** format
3. Check **"Save world file"**
4. Result: `output.png` + `output.pgw` + `output.prj`

### Transparency Support

PNG supports **alpha channel** (transparency):
- Useful for overlay graphics
- Logos on web maps
- Transparent NoData areas

**Create transparent NoData in QGIS:**
1. Layer Properties → **Transparency**
2. Set **NoData value** or **transparent color**
3. Export as PNG

### When to Use

**Use PNG+World for:**
- Lossless map exports
- Graphics with text/labels
- Transparent overlays
- Screenshots with georeferencing
- Web map images

**Avoid for:**
- Large imagery datasets (use GeoTIFF)
- Multispectral analysis (limited bands)
- High-color-depth scientific data

---

## 4. ESRI ASCII Grid (.asc, .txt) — The Human-Readable Raster

**What it is**: Plain text raster format where **every pixel value is written as text**. Simple, readable, inefficient.

### File Structure

**Example: Small 5×5 elevation grid**

```
ncols         5
nrows         5
xllcorner     123456.0
yllcorner     987654.0
cellsize      10.0
NODATA_value  -9999
45.2 46.1 44.8 47.3 48.9
43.7 45.3 46.0 46.8 47.2
42.1 43.9 44.5 45.1 46.3
40.8 42.2 43.0 43.8 45.0
39.5 40.7 41.4 42.1 43.5
```

**Header metadata:**
- `ncols` / `nrows`: Grid dimensions
- `xllcorner` / `yllcorner`: Lower-left corner coordinates
- `cellsize`: Pixel size (same in X and Y)
- `NODATA_value`: Code for missing data

### Why ASCII Grid Exists

**Advantages:**
- **Human-readable** — Open in text editor, inspect values
- **Universal** — Any software can parse text
- **Simple** — No binary format complexity
- **Debugging** — Easy to verify data correctness

**Disadvantages:**
- **Massive file sizes** — 10-100× larger than binary formats
- **Slow I/O** — Text parsing much slower than binary reads
- **Limited metadata** — No CRS embedded (requires `.prj` file)
- **No compression** — Every digit written explicitly

### Common Sources

**Modeling outputs:**
- Hydrological models (flow direction, accumulation)
- Terrain analysis (slope, aspect)
- Species distribution models
- Academic research software (legacy exports)

**Data interchange:**
- Sharing data between incompatible software
- Educational datasets (easy to inspect)
- Manual data creation (small grids)

### Using ASCII Grid in QGIS

**Load:**
1. **Drag `.asc` file** into QGIS
2. Or: **Layer → Add Layer → Add Raster Layer**
3. QGIS converts to internal format for display

**Assign CRS (if missing):**
1. Right-click layer → **Set Layer CRS**
2. Choose projection matching coordinate values

**Export to ASCII:**
1. Right-click layer → **Export → Save As**
2. Format: **Arc/Info ASCII Grid**
3. QGIS writes header + values

### When to Use

**Use ASCII Grid for:**
- Quick inspection of raster values
- Data interchange between incompatible tools
- Educational examples (show actual values)
- Manual small-grid creation
- Debugging spatial analysis

**Avoid for:**
- Large datasets (file size explosion)
- Production workflows (too slow)
- Web delivery (inefficient)
- Archival storage (GeoTIFF better)

---

## 5. ESRI GRID — The Legacy Directory Format

**What it is**: Binary raster format from ESRI Arc/INFO (1980s-1990s). **Directory-based**, not a single file.

### Structure

**Directory with multiple files:**
```
elevation/
├── dblbnd.adf       ← Bounding coordinates
├── hdr.adf          ← Header (dimensions, cell size)
├── sta.adf          ← Statistics
├── vat.adf          ← Value Attribute Table (for integer grids)
├── w001001.adf      ← Data tiles
├── w001001x.adf     ← Index files
└── info/            ← Subdirectory with metadata
    ├── arc.dir
    └── arc0000.dat
```

### Why ESRI GRID Exists

**Historical context:**
- **Pre-GeoTIFF era** (1980s-1990s)
- ESRI's native raster format for Arc/INFO
- Designed for computational efficiency (tiled storage)
- Legacy format still encountered in old datasets

### Limitations

**Problems:**
- **Complex structure** — Dozens of files in directory
- **Platform-specific** — Naming restrictions (8.3 DOS filenames)
- **No compression** — Wastes disk space
- **Proprietary** — ESRI format, limited non-ESRI support
- **Deprecated** — ESRI recommends GeoTIFF now

### When You'll Encounter It

- **Old projects** (pre-2000 vintage)
- **Legacy archives** (university/government data)
- **ArcGIS outputs** (older workflows still use it)
- **Historical datasets** (elevation, land cover from 1990s)

### Working with ESRI GRID in QGIS

**Load:**
1. **Add Raster Layer** → Navigate to GRID directory
2. Select directory (not individual files)
3. QGIS uses GDAL driver to read

**Convert to GeoTIFF:**
1. Right-click layer → **Export → Save As**
2. Format: **GeoTIFF**
3. Choose compression (DEFLATE recommended)

**Command-line conversion:**
```bash
gdal_translate -of GTiff -co COMPRESS=DEFLATE elevation/ elevation.tif
```

### Recommendation

**Always convert to GeoTIFF** when encountering ESRI GRID:
- Single file (easier management)
- Compression (smaller size)
- Better cross-platform support
- Modern standard

---

## Format Comparison Table

| **Format**           | **Compression** | **Multi-Band** | **Embedded CRS** | **File Type**     | **Best For**                     |
|---------------------|----------------|---------------|-----------------|-------------------|----------------------------------|
| **GeoTIFF**          | ✅ Yes (various)| ✅ Yes         | ✅ Yes          | Single file       | Desktop analysis, archival       |
| **JPEG + World**     | ✅ Lossy       | ❌ RGB only    | ⚠️ Separate .prj| Two files         | Visual sharing, historical photos|
| **PNG + World**      | ✅ Lossless    | ❌ Limited     | ⚠️ Separate .prj| Two files         | Web exports, graphics            |
| **ASCII Grid**       | ❌ None        | ❌ Single band | ⚠️ Separate .prj| Single text file  | Debugging, data interchange      |
| **ESRI GRID**        | ❌ None        | ❌ Single band | ✅ Yes          | Directory         | Legacy only (convert to GeoTIFF) |

---

## The Bottom Line

**GeoTIFF is the standard** for raster spatial analysis:
- Self-contained (embedded CRS, no separate files)
- Flexible compression
- Multi-band support
- Universal compatibility

**World files (JPEG, PNG)** are useful for:
- Sharing visual maps (non-GIS users)
- Web graphics
- Historical aerial photos

**ASCII Grid** is for:
- Quick inspection
- Debugging
- Data interchange (when nothing else works)

**ESRI GRID** is legacy—convert to GeoTIFF immediately.

---

## See Also

- Week 6: Cloud Optimized GeoTIFF (COG) & JPEG2000 — Modern web-optimized rasters
- Week 8: NetCDF — Multi-dimensional scientific arrays
- Week 9: HDF5 — Hierarchical scientific data
- [GDAL Raster Formats](https://gdal.org/drivers/raster/index.html) — Comprehensive format documentation
