# Spatial Data Format of the Week: NetCDF (Network Common Data Form)

## The Scientific Multi-Dimensional Array Format

**File extension**: `.nc` or `.nc4`

**What it is**: A self-describing, machine-independent format for storing **array-oriented scientific data**—particularly climate, oceanographic, atmospheric, and Earth system model outputs. If you work with time-series gridded data (temperature over time, sea surface height, precipitation), you work with NetCDF.

## Why NetCDF Exists

### The Multi-Dimensional Problem

Scientific data often has **3+ dimensions**:

**Climate model output:**
- **Longitude** (x): 1440 points
- **Latitude** (y): 720 points
- **Time**: 365 days × 30 years = 10,950 time steps
- **Variables**: Temperature, precipitation, humidity, wind

**Total size**: 1440 × 720 × 10,950 × 4 variables = ~45 billion data points

**GeoTIFF can't handle this** (designed for 2D grids + bands).  
**NetCDF can**: Designed for **n-dimensional arrays with metadata**.

### Example Data Structure

```
dimensions:
    lon = 1440 (0.25° resolution)
    lat = 720  (0.25° resolution)
    time = 10950 (daily, 1990-2020)

variables:
    float temperature(time, lat, lon)
        units: "degrees_celsius"
        long_name: "2-meter air temperature"
        _FillValue: -9999.0
    
    float precipitation(time, lat, lon)
        units: "mm/day"
        long_name: "daily precipitation"
        _FillValue: -9999.0
```

## NetCDF Structure

### 1. Dimensions
Define array axes (lon, lat, time, depth, etc.)

### 2. Variables
Multi-dimensional arrays with metadata:
- **Coordinate variables**: lon, lat, time (dimension metadata)
- **Data variables**: temperature, precipitation, wind_speed

### 3. Attributes
Metadata about variables and the dataset:
- **Global attributes**: Dataset title, institution, creation date
- **Variable attributes**: Units, missing value codes, scale factors

### 4. Self-Describing
**Everything needed to understand the data is embedded**:
- Coordinate system (lat/lon, projected, vertical)
- Units (Celsius, mm, m/s)
- Time encoding (days since 1900-01-01)
- Variable descriptions

## Common NetCDF Sources

### Climate and Weather Data

**ERA5 Climate Reanalysis** (ECMWF)
- Global climate data (1940-present)
- Hourly resolution
- Temperature, precipitation, wind, humidity
- **Google Earth Engine**: Analysis-ready

**PRISM Climate Data** (Oregon State)
- High-resolution U.S. climate grids
- Monthly temperature and precipitation
- 4km resolution

**CMIP6 Climate Models** (World Climate Research)
- Future climate projections
- Multiple models and scenarios
- Hundreds of variables

### Oceanographic Data

**Global Ocean Data Assimilation System (GODAS)**
- Sea surface temperature, salinity, currents
- Ocean heat content
- 3D ocean state (lon, lat, depth, time)

**Copernicus Marine Service**
- Global ocean monitoring
- Satellite + in-situ observations

### Satellite Products

**MODIS/VIIRS** (NASA)
- Global vegetation indices
- Sea surface temperature
- Aerosol optical depth

**GPM (Global Precipitation Measurement)**
- Satellite precipitation estimates
- 30-minute to monthly accumulations

## Working with NetCDF in QGIS

### Loading NetCDF

**Drag and drop** `.nc` file into QGIS, or:

1. **Layer → Add Layer → Add Raster Layer**
2. Select `.nc` file
3. **Choose variable** from list
4. **Choose time step** (if temporal)

**QGIS extracts one 2D slice** (lon × lat) at a time.

### Temporal NetCDF (Time-Series)

**NetCDF with time dimension** opens as **multi-band raster**:
- Band 1 = Time step 1 (e.g., Jan 2020)
- Band 2 = Time step 2 (e.g., Feb 2020)
- Band 365 = Time step 365 (e.g., Dec 2020)

**View time-series animation:**
1. **Layer Properties → Temporal**
2. Enable **Dynamic Temporal Control**
3. Use **Temporal Controller** panel to animate

### NetCDF Browser Plugin

**Better NetCDF handling:**
1. **Plugins → Manage and Install Plugins**
2. Search: **NetCDF Browser**
3. Provides variable explorer and metadata viewer

## Working with NetCDF in Python

### xarray: The NetCDF Swiss Army Knife

```python
import xarray as xr

# Open NetCDF file
ds = xr.open_dataset('temperature_2020.nc')

# View structure
print(ds)
```

**Output:**
```
<xarray.Dataset>
Dimensions:  (lon: 1440, lat: 720, time: 365)
Coordinates:
  * lon      (lon) float32 -180.0 -179.75 ... 179.75
  * lat      (lat) float32 -90.0 -89.75 ... 89.75
  * time     (time) datetime64[ns] 2020-01-01 ... 2020-12-31
Data variables:
    temp     (time, lat, lon) float32 ...
```

### Extract Specific Location

```python
# San Francisco time-series
sf = ds.sel(lon=-122.4, lat=37.8, method='nearest')
sf_temp = sf['temp'].values
```

### Temporal Operations

```python
# Calculate annual mean
annual_mean = ds['temp'].mean(dim='time')

# Seasonal means
winter = ds.sel(time=ds['time.season'] == 'DJF')
winter_mean = winter['temp'].mean(dim='time')
```

### Spatial Subsetting

```python
# Extract California
california = ds.sel(lon=slice(-125, -114), lat=slice(32, 42))
```

### Export to GeoTIFF

```python
import rioxarray

# Open with spatial reference
ds = xr.open_dataset('temp.nc')
ds = ds.rio.write_crs("EPSG:4326")

# Export time step to GeoTIFF
ds['temp'].isel(time=0).rio.to_raster('temp_jan2020.tif')
```

## NetCDF Variants

### NetCDF-3 (Classic)
- Original format
- 2GB file size limit
- Limited data types

### NetCDF-4 (HDF5-based)
- **Modern standard** (use this)
- No file size limits
- Compression support
- Better performance

### NetCDF-4 Classic Model
- NetCDF-4 features, NetCDF-3 structure
- Backward compatibility

## NetCDF Advantages

### 1. Multi-Dimensional Native
- Time-series, 3D, 4D+ data
- Not "hacked" like multi-band GeoTIFF

### 2. Self-Describing
- **CF Conventions** (Climate and Forecast)
- Embedded metadata
- No separate documentation needed

### 3. Efficient Storage
- **Chunking**: Optimize read patterns
- **Compression**: LZW, DEFLATE (NetCDF-4)
- **Packed data**: Store as int16, scale to float64

### 4. Scientific Ecosystem
- **Standard in climate science**
- Extensive software support (Python, R, MATLAB, IDL)
- Analysis-ready formats

### 5. Parallel I/O (NetCDF-4)
- HDF5 parallel access
- High-performance computing workflows

## NetCDF Limitations for GIS

### 1. Not GIS-Native

**Issue**: NetCDF predates GIS conventions
- No embedded CRS (like GeoTIFF `.prj`)
- Coordinate system in CF conventions metadata (not always clear)
- QGIS struggles with complex NetCDF structures

### 2. Projection Ambiguity

**Common issue**: Lat/lon assumed WGS 84, but:
- Some use different datums
- Projected data (polar stereographic, Lambert) requires careful handling
- No `.prj` file to reference

### 3. Raster-Only (Mostly)

NetCDF is **grid-based**:
- No native vector support
- Point clouds, trajectories possible but uncommon
- Use shapefiles/GeoJSON for vector data

### 4. Learning Curve

- **Complex structure** for GIS users
- **xarray/nco tools** required for advanced use
- Not as simple as "drag into QGIS"

## When to Use NetCDF

**Best for:**
- **Time-series gridded data** (climate, weather, oceanography)
- **Multi-dimensional arrays** (3D, 4D+)
- **Scientific analysis** (climate models, forecasts, reanalysis)
- **Large datasets** with compression
- **CF Convention-compliant** data exchange

**Avoid when:**
- **2D static data** (use GeoTIFF)
- **Simple web mapping** (use COG or tiles)
- **Vector data** (use shapefile/GeoJSON)
- **Non-temporal rasters** (GeoTIFF is simpler)

## NetCDF Tools

### Command-Line (NCO - NetCDF Operators)

**Concatenate files:**
```bash
ncrcat file1.nc file2.nc output.nc
```

**Calculate mean:**
```bash
ncwa -a time input.nc output_mean.nc
```

**Extract variable:**
```bash
ncks -v temperature input.nc output_temp.nc
```

### Python Libraries

**xarray**: Multi-dimensional arrays (NumPy for NetCDF)  
**netCDF4**: Low-level NetCDF access  
**rioxarray**: Spatial extensions (CRS, GeoTIFF export)  
**dask**: Parallel, out-of-core computation

### GUI Tools

**Panoply** (NASA): NetCDF viewer and plotter  
**ncview**: Quick visualization  
**ToolsUI** (Unidata): NetCDF structure browser

## The Bottom Line

NetCDF is the **standard format for scientific gridded data**—especially climate, weather, and ocean models. If your data has a **time dimension**, use NetCDF. If it's a single snapshot, GeoTIFF is simpler.

GIS tools handle NetCDF, but it's **optimized for scientific workflows**, not cartography. **Best practice**: Use NetCDF for storage/analysis, export time slices to GeoTIFF for mapping.

**Key takeaway**: NetCDF is GeoTIFF's scientific cousin—same family (gridded data), different priorities (multi-dimensional science vs. 2D maps).

## See Also

- Week 05: GeoTIFF (2D raster standard)
- Week 06: COG (cloud-optimized raster)
- Week 09: HDF5 (scientific array storage)
- Week 09: Python/xarray (NetCDF processing)
- [Unidata NetCDF](https://www.unidata.ucar.edu/software/netcdf/) — Official documentation
- [CF Conventions](http://cfconventions.org/) — Climate and Forecast metadata standard
- [xarray Documentation](https://xarray.pydata.org/) — Python NetCDF toolkit
