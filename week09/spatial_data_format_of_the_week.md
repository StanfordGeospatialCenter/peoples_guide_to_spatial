# Spatial Data Format of the Week: HDF5 & Advanced Scientific Formats

## The Big Data Science Storage Format

**File extension**: `.hdf`, `.hdf5`, `.h5`, `.he5`

**What it is**: Hierarchical Data Format version 5—a **container format** for storing massive, complex scientific datasets with hierarchical organization, compression, and efficient partial I/O. Think of it as a **file system inside a file**.

## Beyond Simple Arrays: The Container Paradigm

While GeoTIFF stores one raster and NetCDF stores multi-dimensional grids, **HDF5 stores entire datasets** with complex structure:

```
satellite_scene.h5
├── Metadata/
│   ├── Acquisition_DateTime
│   ├── Solar_Zenith_Angle
│   └── Cloud_Cover_Percent
├── Science_Data/
│   ├── Temperature_2m (1000 × 1000 array)
│   ├── Precipitation (1000 × 1000 array)
│   ├── Cloud_Mask (1000 × 1000 array)
│   └── Quality_Flags (1000 × 1000 array)
├── Geolocation/
│   ├── Latitude (1000 × 1000 array)
│   └── Longitude (1000 × 1000 array)
└── Calibration/
    ├── Gain_Coefficients
    └── Offset_Coefficients
```

**One HDF5 file** = entire self-contained dataset with internal organization.

## HDF5 Features

### 1. Hierarchical Structure

**Groups** (like folders) organize **datasets** (arrays):
```python
import h5py

with h5py.File('data.h5', 'r') as f:
    # Navigate hierarchy
    temp = f['Science_Data']['Temperature_2m'][:]
    metadata = f['Metadata'].attrs['acquisition_time']
```

### 2. Flexible Data Types
- **Arrays**: N-dimensional (like NumPy)
- **Scalars**: Single values
- **Compound types**: Structured data (like database records)
- **Variable-length types**: Strings, ragged arrays

### 3. Compression
- **Chunked storage**: Divide arrays into blocks
- **Gzip, LZO, Blosc**: Multiple compression algorithms
- **Transparency**: Automatic decompression on read

### 4. Partial I/O
**Read only what you need** (like COG):
```python
# Read 100×100 subset from 10,000×10,000 array
data = f['large_array'][1000:1100, 2000:2100]
```

### 5. Parallel Access
- **MPI-enabled I/O**: Multiple processors read/write simultaneously
- Critical for supercomputing workflows

## Common HDF5 Sources

### NASA Earth Observing System (EOS)

**MODIS HDF-EOS:**
- Moderate Resolution Imaging Spectroradiometer
- Daily global coverage
- `.hdf` or `.he5` files
- Example: `MOD09A1.A2020001.h09v04.061.hdf`

**VIIRS:**
- Visible Infrared Imaging Radiometer Suite
- Higher resolution than MODIS
- HDF5-EOS format

**SRTM (some versions):**
- Shuttle Radar Topography Mission elevation
- HDF format for scientific distribution

**GPM (Global Precipitation Measurement):**
- Satellite precipitation estimates
- HDF5 format

### Sentinel-5P (ESA)

- Atmospheric composition (NO₂, O₃, CO, CH₄)
- NetCDF variant built on HDF5

### Climate Model Outputs

**CMIP6 (some models):**
- Climate Model Intercomparison Project
- HDF5-based NetCDF-4

## Working with HDF5 in QGIS

### Challenge: HDF5 is Not GIS-Native

QGIS **can** open HDF5, but:
1. **Complex structure** confuses layer selection
2. **Geolocation** not always standard (separate lat/lon arrays)
3. **Subdataset navigation** required

### Loading HDF5

**Method 1: Subdataset Selection**
1. **Layer → Add Raster Layer**
2. Select `.hdf` or `.h5` file
3. QGIS lists **subdatasets** (internal arrays)
4. Choose specific variable (e.g., `Temperature_2m`)

**Method 2: GDAL HDF5 Driver**
```bash
# List subdatasets
gdalinfo MOD09A1.hdf
```

**Output:**
```
SUBDATASET_1_NAME=HDF4_EOS:EOS_GRID:"MOD09A1.hdf":MOD_Grid_500m_Surface_Reflectance:sur_refl_b01
SUBDATASET_2_NAME=HDF4_EOS:EOS_GRID:"MOD09A1.hdf":MOD_Grid_500m_Surface_Reflectance:sur_refl_b02
```

**Load specific subdataset:**
```bash
gdal_translate HDF4_EOS:EOS_GRID:"MOD09A1.hdf":MOD_Grid_500m_Surface_Reflectance:sur_refl_b01 band1.tif
```

### MODIS Reprojection Tool (MRT)

NASA provides **MODIS Reprojection Tool**:
- Converts HDF-EOS to GeoTIFF
- Handles MODIS sinusoidal projection
- Batch processing

**Better option**: Use **Google Earth Engine**—MODIS data already processed and analysis-ready.

## Working with HDF5 in Python

### h5py: Low-Level HDF5 Access

```python
import h5py
import numpy as np

# Open HDF5 file
with h5py.File('satellite_data.h5', 'r') as f:
    # List contents
    print(list(f.keys()))
    # Output: ['Science_Data', 'Geolocation', 'Metadata']
    
    # Read dataset
    temp = f['Science_Data']['Temperature'][:]
    
    # Read attributes
    units = f['Science_Data']['Temperature'].attrs['units']
    print(units)  # "degrees_celsius"
    
    # Partial read (1000×1000 from larger array)
    subset = f['Science_Data']['Temperature'][0:1000, 0:1000]
```

### pyhdf: HDF4 (older MODIS format)

```python
from pyhdf.SD import SD, SDC

# Open HDF4 file
hdf = SD('MOD09A1.hdf', SDC.READ)

# List datasets
print(hdf.datasets())

# Read band
band1 = hdf.select('sur_refl_b01')
data = band1.get()
```

### Extract to GeoTIFF

```python
import rioxarray as rxr

# Open HDF5 with xarray
ds = rxr.open_rasterio('data.h5', group='Science_Data')

# Export to GeoTIFF
ds['Temperature'].rio.to_raster('temperature.tif')
```

## HDF5 vs. NetCDF-4

**Surprise**: **NetCDF-4 IS HDF5!**

NetCDF-4 is **HDF5 with CF conventions** (Climate and Forecast metadata):
- Same underlying format
- NetCDF-4 files are HDF5 files
- HDF5 libraries can read NetCDF-4

**Difference**:
- **NetCDF-4**: CF-compliant metadata, simpler API
- **HDF5**: More flexible, complex hierarchies, wider scientific use

**When to use which**:
- **Climate/ocean data**: NetCDF-4 (CF conventions)
- **Satellite/sensor data**: HDF5 (NASA/ESA standard)
- **Generic scientific arrays**: Either works

## HDF5 Advantages

### 1. Massive Data Scalability
- **Petabyte-scale** files supported
- **Trillions of objects** in single file
- NASA uses HDF5 for satellite archives

### 2. Efficient Partial I/O
- **Chunked storage**: Read only needed blocks
- **No full-file download** required
- Critical for large remote datasets

### 3. Complex Data Organization
- **Hierarchical groups**: Organize related data
- **Multiple data types**: Arrays, tables, metadata
- **Self-describing**: All metadata embedded

### 4. High Performance
- **Parallel I/O**: HPC workflows
- **Compression**: Reduce storage/transfer
- **Optimized libraries**: Decades of development

### 5. Industry Standard
- **NASA, NOAA, ESA**: Earth observation
- **Neuroscience**: Brain imaging data
- **Physics**: Particle accelerators, simulations

## HDF5 Limitations for GIS

### 1. Not GIS-Native

**Issues**:
- No standard georeferencing (like GeoTIFF)
- Geolocation often in **separate arrays** (not embedded)
- Projection information varies by dataset

**Workaround**: Convert to GeoTIFF for GIS work.

### 2. Complex Structure

**For GIS users**:
- Navigating hierarchies is unintuitive
- Multiple coordinate arrays confusing
- Requires domain knowledge (which group has temperature?)

### 3. Limited QGIS Support

- Subdataset navigation clunky
- No temporal controller for time-series HDF5
- Better to convert to NetCDF or GeoTIFF

### 4. Specialized Tools Required

**Not universally readable**:
- Need h5py, pyhdf, HDFView, or specialized tools
- Can't open in text editor (binary format)
- Learning curve steeper than GeoTIFF

## When to Use HDF5

**Best for:**
- **Massive satellite datasets** (MODIS, VIIRS, Sentinel)
- **Complex hierarchical data** (multiple related arrays)
- **High-performance computing** workflows
- **Compressed scientific archives**
- **Partial data access** (cloud-based, remote sensing)

**Avoid when:**
- **Simple 2D rasters** (use GeoTIFF)
- **Standard GIS workflows** (convert to GeoTIFF first)
- **Web mapping** (use COG or tiles)
- **Non-technical users** (too complex)

## HDF5 Tools

### GUI Viewers

**HDFView** (HDF Group)
- Official HDF5 viewer
- Browse structure, view datasets
- Cross-platform

**Panoply** (NASA)
- Scientific data viewer
- Supports HDF, NetCDF
- Plots time-series

### Command-Line

**h5dump**
```bash
h5dump -H data.h5  # Header only
h5dump -d /Science_Data/Temperature data.h5  # Specific dataset
```

**h5ls**
```bash
h5ls -r data.h5  # List all contents recursively
```

### Python Libraries

**h5py**: Pythonic HDF5 access  
**pyhdf**: HDF4 (older format)  
**xarray**: High-level multi-dimensional arrays  
**rioxarray**: Spatial extensions for xarray

## The Bottom Line

HDF5 is the **file system for scientific big data**. NASA uses it for **petabytes of satellite imagery**. Climate scientists use it (via NetCDF-4) for **model outputs**. If you're doing serious remote sensing or working with NASA/NOAA data products, you'll encounter HDF5.

For **GIS workflows**, HDF5 is typically an **intermediate format**—you download it, extract the variables you need, convert to GeoTIFF, then work in QGIS. Or better yet, use **Google Earth Engine** where NASA/ESA data is already processed into analysis-ready formats.

**Key takeaway**: HDF5 is the **industrial-strength shipping container** for scientific data. Powerful, flexible, and ubiquitous in science—but overkill for simple GIS tasks.

## See Also

- Week 05: GeoTIFF (simple raster standard)
- Week 06: COG (web-optimized raster)
- Week 08: NetCDF (climate/ocean data)
- Week 09: Python/GDAL (HDF5 processing)
- [HDF5 Documentation](https://www.hdfgroup.org/solutions/hdf5/) — Official specification
- [NASA EarthData](https://earthdata.nasa.gov/) — HDF5 satellite data portal
- [h5py Documentation](https://docs.h5py.org/) — Python HDF5 library
