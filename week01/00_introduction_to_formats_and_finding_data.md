# Introduction to GIS Data Formats & Finding Spatial Data

## Overview

Understanding spatial data formats and knowing where to find quality data are fundamental skills in GIS. This guide covers the most common file formats you'll encounter and provides a comprehensive directory of data sources for your spatial analysis projects.

## Spatial Data Formats Overview

Detailed coverage of spatial data formats is provided in weekly "Spatial Data Format of the Week" (SDFotW) guides throughout the course. Each format is explained in the context of its typical use case and weekly theme:

### Vector Data Formats

- [**Week 0: Shapefile (.shp)**](../week00/spatial_data_format_of_the_week.md) — The universal vector standard: multi-file structure, spatial indexing, universal compatibility
- [**Week 1: GeoJSON (.geojson)**](../week01/spatial_data_format_of_the_week.md) — Web-native vector format: single file, lightweight, direct JavaScript integration
- [**Week 2: GPX (.gpx)**](../week02/spatial_data_format_of_the_week.md) — GPS/fitness tracking format: waypoints, tracks, routes with timestamps
- [**Week 4: Network Datasets for Topology**](../week04/spatial_data_format_of_the_week.md) — Vector topology for analysis: DIME format, network datasets, connectivity rules
- [**Week 10: Esoteric Vector Formats**](../week10/spatial_data_format_of_the_week.md) — Personal Geodatabase, MapInfo TAB, E00, KML/KMZ, DXF/DWG, GML

### SQL & Database Solutions for Spatial Data

- [**Week 3: SQL Solutions (DB Manager, GeoPackage, PostGIS)**](../week03/spatial_data_format_of_the_week.md) — Querying spatial data: QGIS DB Manager, Virtual Layers, GeoPackage, Spatialite, PostGIS

### Raster Data Formats

- [**Week 5: Raster Image Formats**](../week05/spatial_data_format_of_the_week.md) — GeoTIFF (universal standard), JPEG+world files, PNG+world files, ESRI ASCII Grid, ESRI GRID
- [**Week 6: Cloud Optimized GeoTIFF (COG) & JPEG2000**](../week06/spatial_data_format_of_the_week.md) — Modern web-optimized formats: partial data streaming, high bit-depth compression
- [**Week 7: XYZ Tiles & Web Services**](../week07/spatial_data_format_of_the_week.md) — Pre-rendered tiles, WMS (Web Map Service), WFS (Web Feature Service)
- [**Week 8: NetCDF**](../week08/spatial_data_format_of_the_week.md) — Multi-dimensional scientific arrays for climate and oceanographic data
- [**Week 9: HDF5**](../week09/spatial_data_format_of_the_week.md) — Hierarchical scientific format for large-scale satellite and sensor data

### Key Format Selection Principles

- **Vector analysis**: Use Shapefile or GeoPackage (not GeoJSON)
- **Web mapping**: Use GeoJSON or XYZ Tiles (not Shapefile)
- **Raster analysis**: Use GeoTIFF (not imagery with world files)
- **Cloud/web rasters**: Use Cloud Optimized GeoTIFF (COG)
- **Scientific data**: Use NetCDF (multi-dimensional) or HDF5 (hierarchical)
- **Public sharing (non-GIS)**: Use GeoJSON (web) or KML (Google Earth)

**Full format details, workflows, and creation instructions are available in the weekly SDFotW guides linked above.**

## Finding Spatial Data: A Comprehensive Guide

### Academic and Research Sources

#### Stanford EarthWorks

- **URL**: [earthworks.stanford.edu](https://earthworks.stanford.edu/)
- **Strengths**: High-quality academic datasets, historical maps, global coverage
- **Best for**: Research projects, historical analysis, academic work
- **Highlights**: Curated collections, metadata standards, direct download access

#### Google Earth Engine Data Catalog

- **URL**: [developers.google.com/earth-engine/datasets](https://developers.google.com/earth-engine/datasets)
- **Strengths**: Massive archive of **analysis-ready** satellite imagery and environmental datasets
- **Best for**: Time-series analysis, environmental monitoring, global-scale research, cloud computing
- **Key Collections**:
  - **Landsat Collection 2** (1972-present) - Complete archive, surface reflectance
  - **Sentinel-2** (2015-present) - 10m resolution, 5-day revisit
  - **MODIS** (2000-present) - Daily global coverage, 16+ years time-series
  - **Climate data** - ERA5, CHIRPS precipitation, temperature records
  - **Land cover** - Dynamic World, NLCD, ESA WorldCover
- **Advantages**: **Pre-processed for analysis**, cloud masking, atmospheric correction
- **Access**: Free for research and education, requires Google account

### Government Data Sources

#### Data.gov

- **URL**: [data.gov](https://www.data.gov/)
- **Strengths**: Comprehensive U.S. government data portal
- **Best for**: Federal datasets, demographic data, policy analysis
- **Highlights**: Open data initiative, standardized metadata, API access

#### U.S. National Map

- **URL**: [viewer.nationalmap.gov/basic/](https://viewer.nationalmap.gov/basic/)
- **Strengths**: Authoritative U.S. topographic and geographic data
- **Best for**: Base mapping, elevation data, infrastructure analysis
- **Highlights**: USGS topographic maps, elevation data, hydrography

#### USGS EarthExplorer

- **URL**: [earthexplorer.usgs.gov](https://earthexplorer.usgs.gov/)
- **Strengths**: Satellite imagery, aerial photography, elevation data
- **Best for**: Remote sensing projects, change detection, terrain analysis
- **Highlights**: Landsat archive, NAIP imagery, digital elevation models
- **🌍 Google Earth Engine**: **Analysis-ready** - Landsat archive available in GEE
  - [Landsat Collection 2 in GEE](https://developers.google.com/earth-engine/datasets/catalog/landsat)
  - [USGS 3DEP in GEE](https://developers.google.com/earth-engine/datasets/catalog/USGS_3DEP_10m)

#### USGS TopoViewer

- **URL**: [ngmdb.usgs.gov/topoview/viewer](https://ngmdb.usgs.gov/topoview/viewer/#4/40.01/-100.06)
- **Strengths**: Historical topographic maps of the United States
- **Best for**: Historical analysis, change detection, reference mapping
- **Highlights**: Complete USGS topo map archive, georeferenced historical maps

### Specialized Environmental Datasets

#### National Aerial Imagery Program (NAIP)

- **Access**: Through EarthExplorer or state portals
- **Strengths**: High-resolution aerial imagery of the United States
- **Best for**: Land use analysis, agricultural monitoring, urban planning
- **Highlights**: 1-meter resolution, annual coverage, true color and infrared
- **🌍 Google Earth Engine**: **Analysis-ready** in GEE with cloud masking
  - [NAIP in GEE](https://developers.google.com/earth-engine/datasets/catalog/USDA_NAIP_DOQQ)

#### National Land Cover Database (NLCD)

- **Access**: Through EarthExplorer or USGS websites
- **Strengths**: Standardized land cover classification for the U.S.
- **Best for**: Land use change analysis, environmental assessment
- **Highlights**: Consistent methodology, regular updates, multiple time periods
- **🌍 Google Earth Engine**: **Analysis-ready** with time-series support
  - [NLCD in GEE](https://developers.google.com/earth-engine/datasets/catalog/USGS_NLCD_RELEASES)

#### NASS Crop Data Layer (CDL)

- **URL**: [nassgeodata.gmu.edu/CropScape/](https://nassgeodata.gmu.edu/CropScape/)
- **Strengths**: Detailed agricultural land use data
- **Best for**: Agricultural analysis, crop monitoring, rural land use studies
- **Highlights**: Annual updates, detailed crop classifications, county-level statistics
- **🌍 Google Earth Engine**: **Analysis-ready** with annual time-series
  - [NASS CDL in GEE](https://developers.google.com/earth-engine/datasets/catalog/USDA_NASS_CDL)

#### National Wetlands Inventory

- **URL**: [fws.gov/wetlands/data/Mapper.html](https://www.fws.gov/wetlands/data/Mapper.html)
- **Strengths**: Comprehensive wetlands mapping and classification
- **Best for**: Environmental assessment, habitat analysis, regulatory compliance
- **Highlights**: Standardized wetland classification, regular updates, regulatory applications

#### Digital Elevation Models

- **SRTM**: [srtm.csi.cgiar.org/srtmdata/](http://srtm.csi.cgiar.org/srtmdata/)
- **Strengths**: Global elevation data at 30-meter resolution
- **Best for**: Terrain analysis, watershed delineation, global studies
- **Highlights**: Global coverage, consistent resolution, free access
- **🌍 Google Earth Engine**: **Analysis-ready** with multiple DEM products
  - [SRTM 30m in GEE](https://developers.google.com/earth-engine/datasets/catalog/USGS_SRTMGL1_003)
  - [SRTM 90m in GEE](https://developers.google.com/earth-engine/datasets/catalog/CGIAR_SRTM90_V4)
  - [ASTER DEM in GEE](https://developers.google.com/earth-engine/datasets/catalog/ASTER_GED_AG100_003)
  - [NASA DEM in GEE](https://developers.google.com/earth-engine/datasets/catalog/NASA_NASADEM_HGT_001)

#### National Hydrography Dataset

- **Access**: Through USGS National Map or EarthExplorer
- **Strengths**: Comprehensive surface water features for the U.S.
- **Best for**: Watershed analysis, flood modeling, water resource studies
- **Highlights**: Standardized attribution, network topology, regular updates

#### WWF HydroSHEDS

- **URL**: [hydrosheds.org](https://www.hydrosheds.org/)
- **Strengths**: Global hydrographic data based on SRTM elevation
- **Best for**: Global watershed analysis, international water studies
- **Highlights**: Global coverage, multiple resolutions, consistent methodology
- **🌍 Google Earth Engine**: **Analysis-ready** with complete global coverage
  - [HydroSHEDS in GEE](https://developers.google.com/earth-engine/datasets/catalog/WWF_HydroSHEDS_03VFDEM)
  - [Flow Direction in GEE](https://developers.google.com/earth-engine/datasets/catalog/WWF_HydroSHEDS_15DIR)
  - [Flow Accumulation in GEE](https://developers.google.com/earth-engine/datasets/catalog/WWF_HydroSHEDS_15ACC)

#### USDA Soils Data

- **URL**: [websoilsurvey.sc.egov.usda.gov](https://websoilsurvey.sc.egov.usda.gov/App/WebSoilSurvey.aspx)
- **Strengths**: Comprehensive soil surveys and properties
- **Best for**: Agricultural planning, environmental assessment, engineering applications
- **Highlights**: Detailed soil properties, interpretive maps, query capabilities
- **🌍 Google Earth Engine**: **Analysis-ready** soil property grids available
  - [SoilGrids in GEE](https://developers.google.com/earth-engine/datasets/catalog/ISRIC_SoilGrids250m_v2_0) - Global soil properties
  - [STATSGO/SSURGO in GEE](https://developers.google.com/earth-engine/datasets/catalog/USDA_NRCS_STATSGO2) - US soil surveys

#### Digital Floodplain Data

- **URL**: [msc.fema.gov/portal/advanceSearch](https://msc.fema.gov/portal/advanceSearch)
- **Strengths**: Official flood hazard mapping for the United States
- **Best for**: Risk assessment, insurance applications, regulatory compliance
- **Highlights**: Regulatory authority, detailed flood studies, insurance rate maps

#### Geological Data

- **URL**: [mrdata.usgs.gov/general/map-us.html](https://mrdata.usgs.gov/general/map-us.html)
- **Strengths**: Comprehensive geological mapping and mineral resource data
- **Best for**: Geological studies, mineral exploration, hazard assessment
- **Highlights**: Authoritative geological maps, mineral resource databases, hazard information

### Satellite Imagery Collections (Primarily Google Earth Engine)

#### Sentinel Satellites (Copernicus Program)

- **Strengths**: Free, open European satellite constellation with frequent revisit
- **Best for**: Environmental monitoring, agriculture, land change detection
- **🌍 Google Earth Engine**: **Primary access method** - analysis-ready with cloud masking
  - [Sentinel-2 MSI in GEE](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_SR) - 10m optical imagery
  - [Sentinel-1 SAR in GEE](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S1_GRD) - Radar imagery, weather-independent
  - [Sentinel-3 in GEE](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S3_OLCI) - Ocean and land monitoring

#### MODIS (NASA Terra/Aqua Satellites)

- **Strengths**: Long time-series (2000-present), global daily coverage, multiple spectral bands
- **Best for**: Climate research, phenology, large-scale environmental monitoring
- **🌍 Google Earth Engine**: **Analysis-ready** with extensive processing options
  - [MODIS Surface Reflectance in GEE](https://developers.google.com/earth-engine/datasets/catalog/MODIS_006_MOD09A1)
  - [MODIS NDVI in GEE](https://developers.google.com/earth-engine/datasets/catalog/MODIS_006_MOD13A1)
  - [MODIS Land Surface Temperature in GEE](https://developers.google.com/earth-engine/datasets/catalog/MODIS_006_MOD11A1)

#### Planet Labs (Commercial High-Resolution)

- **Strengths**: Daily global coverage, very high resolution (3-5m), rapid revisit
- **Best for**: Change detection, precision agriculture, disaster response
- **🌍 Google Earth Engine**: **Available through Planet-GEE partnership**
  - [Planet in GEE](https://developers.google.com/earth-engine/datasets/catalog/planet) - Requires Planet account
  - Daily imagery for detailed monitoring applications

#### Climate and Weather Data

- **🌍 Google Earth Engine**: **Extensive climate datasets** analysis-ready
  - [ERA5 Climate Reanalysis in GEE](https://developers.google.com/earth-engine/datasets/catalog/ECMWF_ERA5_DAILY)
  - [CHIRPS Precipitation in GEE](https://developers.google.com/earth-engine/datasets/catalog/UCSB-CHG_CHIRPS_DAILY)
  - [GRIDMET Weather in GEE](https://developers.google.com/earth-engine/datasets/catalog/IDAHO_EPSCOR_GRIDMET)

### Demographic and Social Data

#### IPUMS NHGIS

- **URL**: [nhgis.org](https://www.nhgis.org/)
- **Strengths**: Historical and contemporary demographic data with geographic boundaries
- **Best for**: Demographic analysis, historical studies, social research
- **Highlights**: Time-series demographic data, consistent geographic boundaries, research-quality datasets

#### SimplyAnalytics

- **URL**: [simplyanalytics.com](https://simplyanalytics.com)
- **Strengths**: Business and demographic data with mapping capabilities
- **Best for**: Market analysis, business planning, demographic research
- **Highlights**: Current demographic estimates, business data, custom mapping tools

#### U.S. Census Bureau

- **URL**: [census.gov/geographies/mapping-files](https://www.census.gov/geographies/mapping-files/time-series/geo/kml-cartographic-boundary-files.html)
- **Strengths**: Official demographic data and administrative boundaries
- **Best for**: Official statistics, policy analysis, administrative research
- **Highlights**: Decennial census, American Community Survey, official boundaries

### Global and International Sources

#### Natural Earth

- **URL**: [naturalearthdata.com](https://www.naturalearthdata.com/)
- **Strengths**: High-quality global vector and raster map data
- **Best for**: Global mapping projects, reference cartography, educational materials
- **Highlights**: Multiple scales, cartographically designed, public domain

#### OpenStreetMap

- **URL**: [openstreetmap.org](https://www.openstreetmap.org/#map=3/10.17/20.40)
- **Strengths**: Crowd-sourced global mapping data
- **Best for**: Urban mapping, routing applications, community-driven projects
- **Highlights**: Global coverage, regular updates, open license, detailed urban data

#### Overpass Turbo

- **URL**: [overpass-turbo.eu](https://overpass-turbo.eu/)
- **Strengths**: Query interface for OpenStreetMap data
- **Best for**: Custom data extraction from OpenStreetMap
- **Highlights**: Powerful query language, real-time data access, export capabilities

#### Humanitarian OpenStreetMap

- **URL**: [tasks.hotosm.org/explore](https://tasks.hotosm.org/explore)
- **Strengths**: Crisis mapping and humanitarian data projects
- **Best for**: Disaster response, humanitarian projects, community mapping
- **Highlights**: Crisis-focused mapping, volunteer coordination, rapid response

#### Humanitarian Data Exchange

- **URL**: [data.humdata.org](https://data.humdata.org/)
- **Strengths**: Humanitarian and crisis-related datasets
- **Best for**: Emergency response, humanitarian analysis, crisis mapping
- **Highlights**: Crisis-specific data, standardized formats, rapid deployment

### Commercial and Specialized Sources

#### Planet.com

- **URL**: [planet.com](https://www.planet.com/)
- **Strengths**: High-resolution, frequently updated satellite imagery
- **Best for**: Change detection, precision agriculture, monitoring applications
- **Highlights**: Daily global imaging, very high resolution, analytics-ready data
- **🌍 Google Earth Engine**: **Available through partnership** (requires Planet account)
  - [Planet in GEE](https://developers.google.com/earth-engine/datasets/catalog/planet) - 3-5m daily imagery
  - Educational access available through Planet's education program

#### ArcGIS Hub

- **URL**: [hub.arcgis.com](https://hub.arcgis.com/)
- **Strengths**: Government and organization data sharing platform
- **Best for**: Local government data, organizational datasets, community resources
- **Highlights**: Local government focus, data sharing tools, community engagement

### Historical and Cultural Sources

#### Old Maps Online

- **URL**: [oldmapsonline.org](https://www.oldmapsonline.org/)
- **Strengths**: Georeferenced historical maps from multiple collections
- **Best for**: Historical analysis, change detection, cultural studies
- **Highlights**: Multiple map collections, georeferenced overlays, time-series analysis

#### Allmaps.org

- **URL**: [allmaps.org](https://allmaps.org/)
- **Strengths**: Crowdsourced georeferencing of historical maps with modern visualization and web integration
- **Best for**: Historical cartography, time-series historical analysis, web mapping historical data
- **Key Features**:
  - **Interactive georeferencing**: Community-driven map alignment to modern coordinates
  - **XYZ tile services**: Each georeferenced map available as web-accessible tiles
  - **Web mapping integration**: Direct integration with Leaflet, Maplibre, and other modern web libraries
  - **Searchable collections**: Browse by collection, institution, or time period
  - **Stable APIs**: Access georeferenced maps programmatically
- **Collections**: Includes maps from Stanford Digital Repository, Library of Congress, UCLA, and many other institutions
- **When to use**: Overlaying historical maps on modern basemaps, time-series historical analysis, public web mapping projects
- **Example workflow**: 
  1. Search for historical maps in Allmaps (e.g., "San Francisco 1906")
  2. View/contribute georeferencing if needed
  3. Copy XYZ tile URL
  4. Add as basemap layer in Leaflet/Maplibre web map
  5. Overlay modern data on historical context

#### David Rumsey Map Collection

- **URL**: [DavidRumsey.com](https://www.davidrumsey.com/)
- **Strengths**: Extensive collection of historical maps and cartographic materials
- **Best for**: Historical cartography, cultural analysis, educational resources
- **Highlights**: High-resolution scans, detailed metadata, cartographic history
- **Integration**: Many historical maps indexed in Allmaps.org for modern web access

## Search Strategies

### Google Earth Engine for Raster Data Analysis

**Why use Google Earth Engine for raster data?**

- **Analysis-ready data**: Pre-processed with atmospheric correction, cloud masking
- **Massive compute power**: Handle continental-scale analysis in the cloud
- **Time-series ready**: Decades of satellite data organized for temporal analysis
- **No download required**: Process petabytes of data without local storage
- **Reproducible research**: Code-based analysis with version control

**Key advantages marked with 🌍 throughout this guide:**

- Look for the 🌍 symbol next to datasets available in Google Earth Engine
- These datasets are optimized for analysis and ready to use in GEE
- Direct links provided to specific Earth Engine data catalog pages

### General Search Tips

- Use specific file format terms in your searches: "shapefile", "geotiff", "geojson", "geodatabase"
- Include geographic terms: city names, administrative boundaries, geographic features
- Combine data type with location: "California counties shapefile", "elevation data Colorado geotiff"
- **For raster analysis**: Consider Google Earth Engine first for satellite imagery and environmental data

### Data Quality Considerations

- Check metadata for coordinate systems, accuracy, and currency
  - **Interactive georeferencing**: Community-driven map alignment to modern coordinates
  - **XYZ tile services**: Each georeferenced map available as web-accessible tiles
  - **Web mapping plugins**: Direct integration with Leaflet, Maplibre, and other modern web libraries
  - **Searchable collections**: Browse by collection, institution, or time period
  - **Stable APIs**: Access georeferenced maps programmatically
- **Collections**: Includes maps from Stanford Digital Repository, Library of Congress, UCLA, and many other institutions
- **When to use**: Overlaying historical maps on modern basemaps, time-series historical analysis, public web mapping projects
- **Example workflow**: 
  1. Search for historical maps in Allmaps (e.g., "San Francisco 1906")
  2. View/contribute georeferencing if needed
  3. Copy XYZ tile URL
  4. Add as basemap layer in Leaflet/Maplibre web map
  5. Overlay modern data on historical context

#### David Rumsey Map Collection

- **URL**: [DavidRumsey.com](https://www.davidrumsey.com/)
- **Strengths**: Extensive collection of historical maps and cartographic materials
- **Best for**: Historical cartography, cultural analysis, educational resources
- **Highlights**: High-resolution scans, detailed metadata, cartographic history
- **Integration**: Many historical maps indexed in Allmaps.org for modern web access