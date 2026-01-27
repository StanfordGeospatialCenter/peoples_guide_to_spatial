# Spatial Data Format of the Week: Cloud Optimized GeoTIFF (COG) & JPEG2000 (JP2)

## Modern Raster Formats for Web and Cloud Workflows

This week covers **two modern raster formats** optimized for efficiency: **Cloud Optimized GeoTIFF (COG)** for streaming access and **JPEG2000 (JP2)** for high-quality compression. Both solve problems traditional raster formats can't handle.

---

## Part 1: Cloud Optimized GeoTIFF (COG)

### The Problem COG Solves

**Scenario**: You need elevation data for a 1km² study area, but the DEM covers 10,000km².

**Old way** (traditional GeoTIFF):
1. **Download** entire 2GB GeoTIFF
2. Wait 20 minutes
3. Open in QGIS
4. Clip to study area (1km²)
5. Use **<0.01%** of the data you downloaded

**Wasted**: 99.99% of bandwidth and time.

**New way** (Cloud Optimized GeoTIFF):
1. **Request** only pixels in your study area via HTTP
2. Download **~2MB** (just what you need)
3. Process immediately
4. **Result**: 1000× faster, 1000× less data transfer

### What is COG?

**COG is not a new format**—it's **GeoTIFF with specific internal structure** optimized for **partial data reading** over HTTP.

**Key concept**: Traditional GeoTIFF requires full download. COG enables **streaming**.

### What Makes a COG "Cloud Optimized"?

#### 1. Internal Tiling

**Traditional GeoTIFF**: Pixels stored row by row (scanline-based)
```
Row 1: [pixel, pixel, pixel, ... 10,000 pixels ...]
Row 2: [pixel, pixel, pixel, ... 10,000 pixels ...]
Row 10,000: [...]
```

**Problem**: Reading a small area requires scanning through many rows → slow remote access.

**COG**: Pixels organized into **tiles** (typically 512×512 or 256×256)
```
┌─────────┬─────────┬─────────┐
│ Tile    │ Tile    │ Tile    │
│ [0,0]   │ [0,1]   │ [0,2]   │
├─────────┼─────────┼─────────┤
│ Tile    │ Tile    │ Tile    │
│ [1,0]   │ [1,1]   │ [1,2]   │
├─────────┼─────────┼─────────┤
│ Tile    │ Tile    │ Tile    │
│ [2,0]   │ [2,1]   │ [2,2]   │
└─────────┴─────────┴─────────┘
```

**Result**: Request only tiles that intersect your area → fast.

#### 2. Overviews (Pyramids)

COG embeds **multiple resolution versions** in one file:

```
Full resolution:    10m pixels  (10,000 × 10,000 grid)
Overview level 1:   20m pixels  (5,000 × 5,000)
Overview level 2:   40m pixels  (2,500 × 2,500)
Overview level 3:   80m pixels  (1,250 × 1,250)
Overview level 4:   160m pixels (625 × 625)
```

**Zoomed out?** Fetch low-resolution overview (tiny file).  
**Zoomed in?** Fetch full-resolution tiles (only what's visible).

#### 3. HTTP Range Requests

COG is organized so web servers can send **byte ranges**:

```http
GET /elevation.tif
Range: bytes=2048000-2097151
```

**Result**: Download only tiles 100-103, not the whole file.

**Requirements**:
- HTTP server supporting **range requests** (most do)
- Client software that reads COG structure (GDAL 3.1+, QGIS 3.14+)

### Creating COGs

#### Method 1: GDAL Command Line (Recommended)

**Dedicated COG driver (GDAL 3.1+):**
```bash
gdal_translate input.tif output_cog.tif -of COG
```

**Best practice with options:**
```bash
gdal_translate input.tif output_cog.tif \
  -of COG \
  -co COMPRESS=DEFLATE \
  -co PREDICTOR=2 \
  -co NUM_THREADS=ALL_CPUS \
  -co BIGTIFF=IF_SAFER
```

**For imagery (lossy compression):**
```bash
gdal_translate input.tif output_cog.tif \
  -of COG \
  -co COMPRESS=JPEG \
  -co JPEG_QUALITY=85
```

#### Method 2: Python (rio-cogeo)

```python
from rio_cogeo.cogeo import cog_translate
from rio_cogeo.profiles import cog_profiles

cog_translate(
    'input.tif',
    'output_cog.tif',
    cog_profiles.get('deflate'),
    in_memory=False
)
```

#### Method 3: QGIS Processing Toolbox

1. **Processing Toolbox** → GDAL → Raster conversion → **Translate**
2. **Input**: Your raster layer
3. **Advanced Parameters**:
   - Additional creation options: `TILED=YES|COMPRESS=DEFLATE|COPY_SRC_OVERVIEWS=YES`
4. **Run**

#### Verifying COG Validity

```bash
rio cogeo validate output_cog.tif
```

**Output (valid COG):**
```
output_cog.tif is a valid cloud optimized GeoTIFF
```

**Common validation failures:**
- ❌ Missing overviews
- ❌ Non-tiled structure
- ❌ Overviews not tiled

### Using COGs

#### Remote Access (No Download)

**Python (rasterio):**
```python
import rasterio

# Direct cloud access
url = 'https://example.com/elevation_cog.tif'

with rasterio.open(url) as src:
    # Read only the window you need
    window = rasterio.windows.Window(1000, 1000, 512, 512)
    data = src.read(1, window=window)
```

**QGIS:**
1. **Layer → Add Layer → Add Raster Layer**
2. **Source**: Paste cloud URL (e.g., `https://storage.example.com/data.tif`)
3. QGIS streams only visible tiles

**R (terra package):**
```r
library(terra)

# Stream COG from cloud
r <- rast("/vsicurl/https://example.com/elevation_cog.tif")
```

#### Local COG Files

COGs work **exactly like regular GeoTIFFs** for local files—no special handling. The optimization benefits apply when:
- Files are remote (cloud storage, HTTP server)
- You need partial data extraction
- Zooming/panning in web viewers

### COG Advantages

✅ **Cloud-native** — No full download required  
✅ **Bandwidth savings** — 90-99% reduction in typical use cases  
✅ **Faster performance** — Parallel tile requests, instant access  
✅ **Backward compatible** — Works everywhere GeoTIFF works  
✅ **Open standard** — No proprietary lock-in  

### COG Use Cases

**1. Web mapping with raster basemaps:**
```html
<!-- Leaflet with GeotiffLayer plugin -->
<script>
  const cog_url = 'https://example.com/satellite_cog.tif';
  const layer = L.leafletGeotiff(cog_url).addTo(map);
</script>
```

**2. Cloud-based analysis (no download):**
```python
# Process satellite imagery on AWS S3
with rasterio.open('s3://landsat-pds/.../LC08_cog.tif') as src:
    red = src.read(4, window=my_window)
    nir = src.read(5, window=my_window)
    ndvi = (nir - red) / (nir + red)
```

**3. Scientific data distribution:**
- **Landsat Collection 2** (USGS): COG on AWS
- **Sentinel-2** (ESA): COG on AWS S3
- **USGS 3DEP**: Elevation as COG

**4. Time-series stacks:**
```python
# Access 2023 data from 30-year temperature stack
with rasterio.open('temp_1990_2024_cog.tif') as src:
    band_2023 = src.read(33)  # Year 2023 = band 33
```

### COG vs. XYZ Tiles

| **Aspect**              | **COG**                             | **XYZ Tiles**                        |
|-------------------------|-------------------------------------|--------------------------------------|
| **File structure**      | Single file per dataset             | Thousands of small files (pyramid)   |
| **Best for**            | Analysis, dynamic rendering         | Static basemaps, fast web display    |
| **Data access**         | On-demand, any extent               | Pre-rendered tiles at fixed zooms    |
| **Storage**             | One file, easier management         | Complex folder structure             |
| **Updates**             | Easy (replace one file)             | Hard (regenerate entire pyramid)     |
| **Client complexity**   | Requires COG-aware software         | Simple (just fetch PNG/JPEG)         |
| **Server requirements** | HTTP range requests                 | Simple file hosting                  |

**Use COG when**: Data changes frequently, analysis needed, dynamic rendering  
**Use Tiles when**: Static basemaps, maximum display speed, simple clients

### COG Best Practices

**1. Choose appropriate tile size:**
```bash
-co BLOCKSIZE=512  # Most common (512×512 pixels)
```
- **256×256**: Better for web (smaller requests)
- **512×512**: Good balance (default)
- **1024×1024**: Better for analysis (fewer fetches)

**2. Use compression:**
```bash
-co COMPRESS=DEFLATE -co PREDICTOR=2  # Lossless, continuous data
-co COMPRESS=LZW                       # Lossless, categorical data
-co COMPRESS=JPEG -co JPEG_QUALITY=85  # Lossy, imagery
```

**3. Generate overviews:**
```bash
gdaladdo -r average output_cog.tif 2 4 8 16
```
- **Resampling**: `average` (continuous), `nearest` (categorical)
- **Levels**: Powers of 2 (2, 4, 8, 16, 32)

**4. Validate:**
```bash
rio cogeo validate output_cog.tif
```

---

## Part 2: JPEG2000 (JP2)

### What is JPEG2000?

**JP2** is a **wavelet-based image compression standard** (ISO/IEC 15444-1) offering better compression than JPEG with more features.

**Not to be confused with JPEG:**
- **JPEG** (1992): DCT-based, lossy, 8-bit, limited features
- **JPEG2000** (2000): Wavelet-based, lossy OR lossless, up to 16-bit, advanced features

### JPEG2000 Features

#### 1. Lossless OR Lossy Compression

**Your choice:**
```bash
# Lossless (perfect reconstruction)
gdal_translate input.tif output.jp2 -co QUALITY=100

# Lossy (visual quality, smaller file)
gdal_translate input.tif output.jp2 -co QUALITY=20
```

**Quality scale**: 0 (maximum compression) to 100 (lossless)

#### 2. Progressive Resolution

JP2 supports **progressive decoding**:
- Decode low-resolution version first (fast preview)
- Refine to full resolution as more data loads
- Similar to COG overviews, but built into compression

#### 3. Higher Bit Depth

- **JPEG**: 8-bit per channel (0-255)
- **JPEG2000**: Up to **16-bit per channel** (0-65,535)

**Critical for**:
- Scientific imagery (preserve dynamic range)
- Elevation data (sub-meter precision)
- Multispectral satellite data

#### 4. Better Compression Ratios

**Same visual quality:**
- JPEG: 100 KB
- JPEG2000: 60 KB (40% smaller)

**Same file size:**
- JPEG: Artifacts, blocking
- JPEG2000: Smoother, fewer artifacts

### JPEG2000 in Remote Sensing

**Sentinel-2 (ESA) uses JP2:**
- Distributed as `.jp2` files
- 10m-60m resolution bands
- 12-bit radiometric resolution
- Lossy compression (typical 20:1 ratio)

**Why Sentinel-2 chose JP2:**
- **High bit depth** (12-bit sensor data)
- **Better compression** than JPEG (smaller downloads)
- **Progressive decoding** (preview before full load)

### Working with JPEG2000 in QGIS

#### Loading JP2

**Drag and drop** into QGIS, or:
1. **Layer → Add Layer → Add Raster Layer**
2. Select `.jp2` file
3. QGIS uses GDAL JP2 drivers

**Note**: Requires GDAL compiled with JP2 support (OpenJPEG, Kakadu, or ECW drivers).

#### Check GDAL JP2 Support

```bash
gdalinfo --formats | grep -i jp2
```

**Expected output:**
```
JP2OpenJPEG -raster,vector- (rw+vs): JPEG-2000 driver based on OpenJPEG library
JP2KAK -raster- (rw+vs): JPEG-2000 (based on Kakadu)
```

If missing, install OpenJPEG:
```bash
# macOS
brew install openjpeg

# Ubuntu
sudo apt install libopenjp2-7
```

#### Convert JP2 to GeoTIFF

**Why convert?**
- GeoTIFF has better QGIS/Python ecosystem support
- Avoid JP2 driver compatibility issues
- Faster processing (no decompression overhead)

```bash
gdal_translate input.jp2 output.tif -co COMPRESS=DEFLATE
```

#### Create JP2 from GeoTIFF

**Lossless:**
```bash
gdal_translate input.tif output.jp2 -co QUALITY=100
```

**Lossy (smaller files):**
```bash
gdal_translate input.tif output.jp2 \
  -co QUALITY=20 \
  -co REVERSIBLE=NO \
  -co YCBCR420=NO
```

### JPEG2000 Advantages

✅ **Better compression** than JPEG (40-60% smaller)  
✅ **Lossless option** (unlike JPEG)  
✅ **High bit depth** (up to 16-bit)  
✅ **Progressive decoding** (fast previews)  
✅ **Fewer artifacts** (wavelet vs. DCT)  

### JPEG2000 Disadvantages

❌ **Limited software support** (needs specific drivers)  
❌ **Slower decode** than JPEG (wavelet computation)  
❌ **Patent issues** (historically—mostly resolved now)  
❌ **Not web-native** (browsers don't support natively)  
❌ **Complex standard** (multiple profiles, extensions)  

### When to Use JP2

**Use JPEG2000 for:**
- **Sentinel-2 data** (native format)
- **High bit-depth imagery** (>8-bit per channel)
- **Scientific datasets** requiring lossless compression
- **Archival storage** (better than lossy JPEG, smaller than lossless TIFF)

**Avoid when:**
- **Web mapping** (browsers don't support—use COG instead)
- **Simple workflows** (GeoTIFF simpler, more compatible)
- **Limited GDAL support** (driver issues)

### JP2 vs. Other Formats

| **Format**     | **Compression** | **Lossless?** | **Bit Depth** | **Web Support** | **GIS Support** |
|----------------|-----------------|---------------|---------------|-----------------|-----------------|
| **JPEG**       | Lossy           | ❌ No         | 8-bit         | ✅ Excellent    | Limited         |
| **JPEG2000**   | Lossy OR Lossless | ✅ Yes      | Up to 16-bit  | ❌ No           | ⚠️ Moderate     |
| **GeoTIFF**    | Multiple options | ✅ Yes       | Up to 32-bit  | ⚠️ Partial      | ✅ Excellent    |
| **COG**        | Multiple options | ✅ Yes       | Up to 32-bit  | ✅ Yes (with client) | ✅ Excellent |

---

## COG vs. JP2: Which to Choose?

### Use COG for:
- **Web mapping** applications
- **Cloud-based analysis** workflows
- **Large datasets** accessed remotely
- **Desktop GIS** (universal support)

### Use JP2 for:
- **Sentinel-2 data** (already in JP2)
- **High bit-depth scientific data** (>8-bit)
- **Archival storage** (better than lossy JPEG)
- **Specific compression needs** (wavelet-based)

### Modern Recommendation

**For new projects**: **Use COG** (Cloud Optimized GeoTIFF).

**Why?**
- Better software support (GDAL, QGIS, Python, R, web clients)
- Native web streaming (HTTP range requests)
- Backward compatible with GeoTIFF ecosystem
- No driver complexity issues
- Works in browsers with appropriate clients

**Use JP2 only when**:
- Working with Sentinel-2 (native format)
- Specific compression requirements demand wavelet encoding
- Legacy systems require JP2 format

---

## Practical Workflows

### Sentinel-2 Processing

**Scenario**: Download Sentinel-2 data (JP2 format), process in QGIS.

**Option 1: Keep as JP2**
```python
# Python (rasterio)
import rasterio
with rasterio.open('T10SEG_20230601_B04_10m.jp2') as src:
    red = src.read(1)
```

**Option 2: Convert to GeoTIFF**
```bash
# Batch convert all bands
for file in *.jp2; do
    gdal_translate "$file" "${file%.jp2}.tif" -co COMPRESS=DEFLATE
done
```

**Recommendation**: Convert to GeoTIFF (or COG) for analysis, keep JP2 as archive.

### Large Raster Distribution

**Scenario**: Distribute 10GB aerial imagery online.

**Best approach:**
1. **Create COG** from source imagery:
   ```bash
   gdal_translate aerial_10gb.tif aerial_cog.tif -of COG -co COMPRESS=JPEG -co JPEG_QUALITY=85
   ```
2. **Upload to cloud storage** (AWS S3, Google Cloud Storage)
3. **Share URL** — users stream only what they need

**Result**: Users download 10-50 MB for typical use case, not 10 GB.

---

## The Bottom Line

**Cloud Optimized GeoTIFF (COG)** is the **modern raster standard** for web and cloud workflows. It's GeoTIFF with optimization—same compatibility, but unlocks streaming access.

**JPEG2000 (JP2)** is a **specialized format** with excellent compression and high bit-depth support. Use it for Sentinel-2 data, scientific archives, or when wavelet compression is specifically needed. For most GIS work, COG is simpler and better supported.

**Key takeaway**: Traditional GeoTIFF says "download everything." COG says "stream what you need." JP2 says "compress efficiently with wavelets."

**Modern best practice**: Create COGs by default. Convert JP2 to GeoTIFF/COG for processing.

## See Also

- Week 05: Raster Image Formats — GeoTIFF, JPEG, PNG, ASCII, ESRI GRID
- Week 07: XYZ Tiles — Pre-rendered web map tiles
- Week 09: GDAL — Command-line raster processing tools
- [COG Specification](https://www.cogeo.org/) — Cloud optimization standard
- [JPEG2000 Overview](https://jpeg.org/jpeg2000/) — Official JPEG2000 site
- [OpenJPEG](https://www.openjpeg.org/) — Open-source JP2 codec
- [Sentinel-2 User Guide](https://sentinels.copernicus.eu/web/sentinel/user-guides/sentinel-2-msi) — JP2 usage in satellite data
