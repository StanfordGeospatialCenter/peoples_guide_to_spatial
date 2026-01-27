# Spatial Data Format of the Week: XYZ Tiles & Web Services (WMS/WFS/IIIF)

## Web Mapping Formats

This week covers **four interconnected web mapping formats**:
1. **XYZ Tiles** — Pre-rendered map images in pyramid structure
2. **WMS (Web Map Service)** — On-demand map image rendering
3. **WFS (Web Feature Service)** — Vector feature streaming
4. **IIIF (International Image Interoperability Framework)** — Georeferenced historic cartography

All four enable **web mapping without downloading entire datasets**.

---

## Part 1: XYZ Tiles (Slippy Map Tiles)

**What it is**: Pre-rendered map images organized in a pyramid folder structure based on zoom level, row, and column.

### The Tile Pyramid Concept

**Zoom level 0**: Entire world = 1 tile (256×256 pixels)

```
Zoom 0:   [world.png]
```

**Zoom 1**: World = 2×2 = 4 tiles

```
Zoom 1:   [0,0] [1,0]
          [0,1] [1,1]
```

**Zoom 2**: World = 4×4 = 16 tiles

**Each zoom level quadruples**:
- Zoom 3: 64 tiles (8×8)
- Zoom 10: 1,048,576 tiles (1,024×1,024)
- Zoom 18: 68 billion tiles

### URL Structure

**Standard XYZ tile URL:**
```
https://tile.openstreetmap.org/{z}/{x}/{y}.png
```

**Example**: San Francisco at zoom 12
```
https://tile.openstreetmap.org/12/654/1582.png
```

**Where**:
- `{z}` = Zoom level (0-18 typically)
- `{x}` = Column (west to east)
- `{y}` = Row (north to south)

### Common Tile Sources

**OpenStreetMap:**
```
https://tile.openstreetmap.org/{z}/{x}/{y}.png
```

**CartoDB Dark:**
```
https://cartodb-basemaps-{s}.global.ssl.fastly.net/dark_all/{z}/{x}/{y}.png
```
*`{s}` = subdomain (a, b, c, d) for load balancing*

**Stamen Terrain:**
```
https://stamen-tiles.a.ssl.fastly.net/terrain/{z}/{x}/{y}.png
```

**Satellite (ESRI):**
```
https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}
```

### Using XYZ Tiles in QGIS

**Add Tile Layer:**
1. **Browser Panel** → XYZ Tiles → (right-click) **New Connection**
2. **Name**: Give it a descriptive name
3. **URL**: Paste tile URL template
4. Click **OK**
5. **Double-click** to add to map

**Example Configuration:**
- **Name**: OpenStreetMap
- **URL**: `https://tile.openstreetmap.org/{z}/{x}/{y}.png`

### XYZ Tile Advantages

**1. Speed**
- **Pre-rendered** = instant display
- No processing time
- Cached by browsers/CDN

**2. Simplicity**
- Just images (PNG/JPEG)
- Any web server can host
- Works with simple HTTP GET requests

**3. Scalability**
- **CDN-friendly** (CloudFlare, Akamai)
- Parallel tile fetching (load many at once)
- Handles millions of users

**4. Universal Support**
- Every web mapping library (Leaflet, Mapbox, OpenLayers)
- Mobile mapping apps
- QGIS, ArcGIS Online

### XYZ Tile Limitations

**1. Static Content**
- **Can't change** what's displayed (pre-rendered)
- **Can't query** attributes (just images)
- **Can't style** dynamically

**2. Storage Requirements**
- Full global tileset at zoom 18 = petabytes
- Typical country-scale tileset = gigabytes to terabytes

**3. Update Overhead**
- Modify one feature = **regenerate all affected tiles**
- Time-consuming for large areas

**4. Fixed Zoom Levels**
- **Only specific zoom levels** (discrete, not continuous)
- "Jumpy" zoom experience

### When to Use XYZ Tiles

**Best for:**
- **Basemaps** (OpenStreetMap, satellite imagery, terrain)
- **High-traffic web maps** (millions of users)
- **Static data** (doesn't change frequently)
- **Fast display** (pre-rendered performance)

**Avoid when:**
- **Dynamic data** (real-time, frequently updated)
- **User-styled layers** (custom symbology per user)
- **Vector analysis** needed (tiles are images, not features)
- **Attribute queries** required

---

## Part 2: WMS (Web Map Service)

**What it is**: OGC standard protocol for serving **georeferenced map images** dynamically rendered from spatial databases.

### How WMS Works

**Client request:**
```http
https://example.com/geoserver/wms?
  service=WMS&
  version=1.3.0&
  request=GetMap&
  layers=counties&
  bbox=-124,32,-114,42&
  width=800&
  height=600&
  crs=EPSG:4326&
  format=image/png
```

**Server response**: Rendered PNG image of counties within bounding box.

### WMS vs. XYZ Tiles

| **Aspect**            | **WMS**                              | **XYZ Tiles**                        |
|-----------------------|--------------------------------------|--------------------------------------|
| **Rendering**         | On-demand (dynamic)                  | Pre-rendered (static)                |
| **Speed**             | Slower (render time)                 | Faster (cached)                      |
| **Flexibility**       | Any extent, projection, style        | Fixed zoom levels, style             |
| **Data updates**      | Instant (reflects database changes)  | Requires tile regeneration           |
| **Storage**           | Minimal (source data only)           | Large (millions of tiles)            |
| **Query support**     | Yes (GetFeatureInfo)                 | No (just images)                     |

### Using WMS in QGIS

**Add WMS Layer:**
1. **Layer → Add Layer → Add WMS/WMTS Layer**
2. Click **New** connection
3. **Name**: Service name
4. **URL**: Base WMS endpoint (without parameters)
5. Click **OK** → **Connect**
6. Select layers → **Add**

**Example WMS Services:**

**USGS National Map:**
```
https://basemap.nationalmap.gov/arcgis/services/USGSTopo/MapServer/WMSServer
```

**OpenStreetMap WMS (terrestris):**
```
https://ows.terrestris.de/osm/service?
```

**California Data Geoportal:**
```
https://gis.data.ca.gov/arcgis/services/...
```

### WMS Capabilities

**GetCapabilities**: Query available layers
```http
https://example.com/wms?service=WMS&request=GetCapabilities
```

**GetMap**: Fetch map image (see example above)

**GetFeatureInfo**: Query attributes at clicked point
```http
https://example.com/wms?
  service=WMS&
  request=GetFeatureInfo&
  query_layers=counties&
  x=250&
  y=150&
  ...
```

### When to Use WMS

**Best for:**
- **Dynamic data** (real-time, frequently updated)
- **Custom rendering** (user-defined styles, filters)
- **Analysis/query** (GetFeatureInfo)
- **Multiple projections** (server-side reprojection)

**Avoid when:**
- **High traffic** (slow rendering under load)
- **Static basemaps** (tiles are faster)
- **Offline access** needed (requires server connection)

---

## Part 3: WFS (Web Feature Service)

**What it is**: OGC standard for serving **vector features** (geometry + attributes) over the web, unlike WMS which serves images.

### WFS vs. WMS

- **WMS**: Serves **images** of maps (raster)
- **WFS**: Serves **features** (vector data—points, lines, polygons with attributes)

**Key difference**: WFS returns **actual geometry and attributes**, enabling client-side styling and analysis.

### How WFS Works

**Request features:**
```http
https://example.com/geoserver/wfs?
  service=WFS&
  version=2.0.0&
  request=GetFeature&
  typeNames=counties&
  count=100&
  outputFormat=application/json
```

**Response**: GeoJSON with features:
```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {...},
      "properties": {
        "name": "Santa Clara County",
        "population": 1936259
      }
    }
  ]
}
```

### Using WFS in QGIS

**Add WFS Layer:**
1. **Layer → Add Layer → Add WFS Layer**
2. Click **New** connection
3. **URL**: Base WFS endpoint
4. Click **OK** → **Connect**
5. Select layer → **Add**

**Result**: **Editable vector layer** (not image) loaded into QGIS.

### WFS Capabilities

**GetCapabilities**: List available feature types
```http
https://example.com/wfs?service=WFS&request=GetCapabilities
```

**GetFeature**: Fetch features (with filters, bbox, attribute queries)

**Transaction** (WFS-T): **Insert, Update, Delete** features remotely
- Collaborative editing
- Mobile data collection apps
- Real-time feature updates

### Common WFS Sources

**National GI Portal (many countries)**
**USGS WFS Services** (hydrography, boundaries)
**City/County GIS Portals** (parcels, zoning, infrastructure)

### When to Use WFS

**Best for:**
- **Vector analysis** (need geometry, not images)
- **Attribute queries** (filter by properties)
- **Editable data** (collaborative mapping)
- **Client-side styling** (Leaflet, Mapbox custom styles)
- **Data integration** (merge remote vectors with local data)

**Avoid when:**
- **Large datasets** (>100,000 features—too slow)
- **Raster data** (use WMS or COG)
- **Display-only** (WMS/tiles are faster)

---
Part 4: IIIF (International Image Interoperability Framework)

**What it is**: A set of open standards for **delivering high-resolution images** over the web, increasingly used for **georeferenced historic maps** from libraries and archives.

### IIIF + Allmaps.org: Historic Cartography Pipeline

**IIIF** enables libraries to serve historic maps at web scale.  
**Allmaps.org** provides georeferencing tools that create XYZ tile layers from IIIF images.

### How IIIF Works

**IIIF Image API** delivers image tiles similar to XYZ tiles, but with flexible parameters:

**Standard IIIF URL structure:**
```
https://example.org/iiif/2/{identifier}/{region}/{size}/{rotation}/{quality}.{format}
```

**Example:**
```
https://dlcs.io/iiif-img/3/2/abc123/full/800,/0/default.jpg
```

**Where**:
- `{identifier}`: Unique image ID
- IIIF + Allmaps** is the **library archives**—historic treasures made accessible, georeferenced for modern analysis.

**Key takeaway**: Choose tiles for speed, WMS for flexibility, WFS for analysis, IIIF for historic cartography. Often you'll use all four in one project—tiles as basemap, WFS for analysis layers, WMS for dynamic overlays, IIIF for historical context.

## See Also

- Week 00: Georeferencing (georeferencing workflow using QGIS Georeferencer and Allmaps.org)
- Week 01: GeoJSON (client-side vector format)
- Week 05: GeoTIFF (raster data)
- Week 06: Cloud Optimized GeoTIFF (remote raster access)
- [Allmaps.org](https://allmaps.org/) — Georeference IIIF maps, create tile layers
- [IIIF Consortium](https://iiif.io/) — Standards documentation
- [Library of Congress Maps](https://www.loc.gov/maps/) — IIIF-enabled historic map collection
- [David Rumsey Map Collection](https://www.davidrumsey.com/) — Massive georeferenced historic map archive

**Major map collections with IIIF support:**

**Library of Congress:**
```
https://www.loc.gov/collections/panoramic-maps/
```

**David Rumsey Map Collection:**
```
https://www.davidrumsey.com/
```

**British Library:**
```
https://www.bl.uk/maps
```

**Stanford Libraries:**
```
https://searchworks.stanford.edu/view/...
```

### Allmaps.org: Georeferencing IIIF Images

**Allmaps** turns IIIF historic maps into **georeferenced XYZ tile layers** you can use in QGIS, Leaflet, or any web map.

**Workflow:**
1. Find historic map with IIIF manifest (Library of Congress, David Rumsey, etc.)
2. Open in **Allmaps Editor**: https://editor.allmaps.org/
3. Add control points (match historic map features to modern coordinates)
4. Allmaps generates **XYZ tile URL**
5. Use tile URL in QGIS or web maps

### Using Allmaps Tiles in QGIS

**After georeferencing in Allmaps Editor**, you get a tile URL like:
```
https://allmaps.xyz/maps/{map-id}/{z}/{x}/{y}.png
```

**Add to QGIS:**
1. **Browser Panel** → **XYZ Tiles** → (right-click) **New Connection**
2. **Name**: Historic Map Name (e.g., "Sanborn 1895 SF")
3. **URL**: Paste Allmaps tile URL
4. Click **OK**
5. **Double-click** to add to map

**Result**: Historic map overlays modern basemap, georeferenced and tiled.

### Allmaps Features

**1. Cloud-based georeferencing**
- No software installation
- Works directly with IIIF URLs
- Results stored in W3C Web Annotation format

**2. Mask tool**
- Remove borders, legends, decorative elements
- Clean overlay on modern maps

**3. Multiple transformation algorithms**
- **Polynomial**: Best for most historic maps
- **Thin Plate Spline**: Handles warped/distorted maps
- **Projective**: For maps with perspective distortion

**4. Shareable URLs**
- Each georeferenced map gets permanent URL
- Embed in web pages or load in QGIS
- Collaborative georeferencing

### Example: Using Library of Congress Maps

**1. Find map at LOC:**
- Browse: https://www.loc.gov/maps/
- Look for **IIIF Manifest** link or icon

**2. Copy IIIF Manifest URL**, e.g.:
```
https://www.loc.gov/item/2013593069/manifest.json
```

**3. Open Allmaps Editor:**
- Go to: https://editor.allmaps.org/
- Paste manifest URL
- Click **Load**

**4. Georeference:**
- Click historic map location
- Click corresponding modern map location
- Repeat 3-6 times (minimum 3 control points)
- Click **Results** tab

**5. Copy tile URL:**
```
https://allmaps.xyz/maps/abc123def456/{z}/{x}/{y}.png
```

**6. Add to QGIS** (see workflow above)

### IIIF + Allmaps Advantages

✅ **No downloads**: Stream historic maps from library servers  
✅ **High resolution**: IIIF delivers full detail at all zoom levels  
✅ **Permanent URLs**: Libraries maintain collections long-term  
✅ **Free access**: Most institutions provide open IIIF endpoints  
✅ **Collaborative**: Share georeferenced maps via URL  
✅ **Standards-based**: IIIF + W3C Web Annotations  

### IIIF + Allmaps Limitations

❌ **Requires IIIF support**: Not all archives have IIIF  
❌ **Manual georeferencing**: Must add control points yourself  
❌ **Internet required**: Cannot work offline  
❌ **Variable quality**: Some historic maps poorly scanned  

### When to Use IIIF + Allmaps

**Best for:**
- **Historic map overlays** (Sanborn fire insurance, USGS historical topos)
- **Change detection** (urban growth, coastline change)
- **Historic spatial analysis** (past land use, transportation networks)
- **Teaching/research** (visualizing historical geography)

**Example use cases:**
- Overlay 1906 San Francisco Sanborn map over modern streets
- Compare 1850 London map with current building footprints
- Trace historical rail networks from USGS topos
- Study urban expansion with sequential historic maps

### Beyond Allmaps: Other IIIF Tools

**IIIF Georeferencer** (Klokan Technologies):
- https://georeferencer.com/
- More advanced georeferencing features
- Export to GeoTIFF, tiles, WMS

**Georeferencer QGIS Plugin**:
- Connects to Georeferencer.com
- Load georeferenced maps directly in QGIS

**OpenSeadragon**:
- JavaScript viewer for IIIF images
- Embed zoomable historic maps in web pages

---

## Comparing Web Formats

| **Format**   | **Data Type** | **Use Case**                        | **Speed** | **Flexibility** |
|--------------|---------------|-------------------------------------|-----------|-----------------|
| **XYZ Tiles**| Image         | Basemaps, static display            | Fast      | Low             |
| **WMS**      | Image         | Dynamic maps, custom styles         | Medium    | High            |
| **WFS**      | Vector        | Analysis, editing, queries          | Slow      | Highest         |
| **IIIF**     | Image         | Historic maps, high-res manuscripts | Medium    | Medium          |
| **COG**      | Raster        | Remote raster analysis              | Medium    | Medium          |

**Decision matrix:**
- **Need basemap?** → XYZ Tiles
- **Need dynamic map rendering?** → WMS
- **Need vector features for analysis?** → WFS
- **Need historic maps?** → IIIF + Allmaps
- **Need vector features for analysis?** → WFS
- **Need remote raster processing?** → COG

---

## The Bottom Line

**XYZ Tiles** are the **fast food of web mapping**—quick, cheap, ubiquitous, but not nutritious for analysis.

**WMS** is the **restaurant menu**—custom orders, made to order, but takes longer.

**WFS** is the **farmer's market**—raw ingredients (features) for you to cook (analyze) yourself.

**Key takeaway**: Choose tiles for speed, WMS for flexibility, WFS for analysis. Often you'll use all three in one project—tiles as basemap, WFS for analysis layers, WMS for dynamic overlays.

## See Also

- Week 01: GeoJSON (client-side vector format)
- Week 05: GeoTIFF (raster data)
- Week 06: Cloud Optimized GeoTIFF (remote raster access)
- [Leaflet](https://leafletjs.com) — Popular web mapping library
- [QGIS QuickMapServices Plugin](https://plugins.qgis.org/plugins/quick_map_services/) — Easy basemap access
- [GeoServer](http://geoserver.org/) — Open-source WMS/WFS server
