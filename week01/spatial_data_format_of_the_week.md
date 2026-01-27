# Spatial Data Format of the Week: GeoJSON

## The Web-Native Vector Format

**File extension**: `.geojson` or `.json`

**What it is**: A lightweight, human-readable format for encoding geographic data structures using JavaScript Object Notation (JSON). It's the modern standard for web mapping and API data exchange.

## The Single-File Simplicity

Unlike shapefiles (which require 3-15 files), GeoJSON is **one self-contained file**:

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [-122.375, 37.619]
      },
      "properties": {
        "name": "San Francisco International Airport",
        "iata_code": "SFO",
        "passengers_annual": 57800000
      }
    }
  ]
}
```

## Why Web Developers Love It

### Advantages

**1. Web-Friendly by Design**
- Native JavaScript format—no parsing libraries needed
- Direct integration with web mapping libraries (Leaflet, Mapbox, D3)
- Perfect for APIs and web services
- Readable by humans and machines

**2. No Artificial Limitations**
- Field names can be **any length**: `population_density_2020_census_tract_estimate`
- Unlimited fields per feature
- No 2GB component file limits
- Single file—no missing companions

**3. Flexible Data Structures**
- Supports **nested attributes**: `{"address": {"street": "123 Main", "city": "Palo Alto"}}`
- **Arrays**: `"languages": ["English", "Spanish", "Mandarin"]`
- **Boolean values**: `"is_active": true`
- **Null values**: `"elevation": null`
- Mixed data types within properties

**4. Modern Ecosystem**
- GitHub renders GeoJSON automatically
- Observable, Jupyter, and Colab support native display
- Extensive JavaScript library support
- Direct browser viewing without GIS software

## The Performance Trade-offs

### Limitations

**1. No Spatial Indexing**
- Every spatial query requires scanning the entire file
- Slow for large datasets (>50,000 features)
- Desktop GIS software may struggle with rendering

**2. File Size Bloat**
- Text format is larger than binary formats
- Coordinate precision stored as full decimal strings
- Redundant structure for repeated property names

**3. Coordinate System Ambiguity**
- **Assumed WGS 84 (EPSG:4326)** unless specified
- No embedded `.prj` file—CRS must be in metadata
- Reprojection requires explicit handling

**4. Not Optimized for Analysis**
- Designed for **display and transport**, not complex spatial operations
- QGIS/ArcGIS prefer shapefiles for heavy analysis
- Better suited for web visualization than desktop workflows

## When to Use GeoJSON

**Best for:**
- Web mapping applications
- API responses and data exchange
- GitHub/GitLab spatial data (auto-renders)
- Interactive web visualizations (D3, Leaflet, Mapbox)
- Simple geometries with rich attributes
- Data under ~50MB / ~50,000 features

**Avoid when:**
- Large datasets requiring spatial indexes
- Desktop GIS analysis workflows
- File sizes exceed 50MB
- Performance is critical (use tiles or GeoPackage)

## Creating GeoJSON in QGIS

1. **Right-click layer** → Export → Save Features As
2. **Format**: GeoJSON
3. **File name**: `my_data.geojson`
4. **CRS**: EPSG:4326 - WGS 84 (recommended for web)
5. **Encoding**: UTF-8
6. **Coordinate precision**: 6-8 decimal places (balance precision vs. file size)
7. Click **OK**

## GeoJSON vs. Shapefile: The Decision Matrix

| **Criterion**               | **GeoJSON**                          | **Shapefile**                         |
|-----------------------------|--------------------------------------|---------------------------------------|
| **Use case**                | Web mapping, APIs                    | Desktop GIS, analysis                 |
| **File structure**          | Single file                          | 3-15 files                            |
| **Field name length**       | Unlimited                            | 10 characters max                     |
| **File size limit**         | ~50MB practical                      | 2GB per component                     |
| **Spatial indexing**        | No                                   | Yes (.sbn/.sbx)                       |
| **Web compatibility**       | Native JavaScript                    | Requires conversion                   |
| **Human readable**          | Yes                                  | No (binary)                           |
| **Desktop GIS performance** | Slow on large files                  | Fast with indexes                     |

## Advanced: GeoJSON Variants

### GeoJSONL (GeoJSON Lines)
- One feature per line (newline-delimited)
- Streamable for large datasets
- Used by Tippecanoe, Mapbox, and big data pipelines

```json
{"type":"Feature","geometry":{"type":"Point","coordinates":[-122.375,37.619]},"properties":{"name":"SFO"}}
{"type":"Feature","geometry":{"type":"Point","coordinates":[-0.454,51.470]},"properties":{"name":"LHR"}}
```

### TopoJSON
- Optimized encoding eliminating redundant coordinates
- Shared borders (topology preservation)
- ~80% smaller file sizes
- Used for choropleth maps with shared boundaries

## Web Mapping with GeoJSON

**Leaflet example:**

```javascript
fetch('airports.geojson')
  .then(response => response.json())
  .then(data => {
    L.geoJSON(data, {
      onEachFeature: (feature, layer) => {
        layer.bindPopup(feature.properties.name);
      }
    }).addTo(map);
  });
```

**D3.js example:**

```javascript
d3.json('counties.geojson').then(data => {
  svg.selectAll('path')
    .data(data.features)
    .enter().append('path')
    .attr('d', d3.geoPath());
});
```

## The Bottom Line

GeoJSON is the **format for modern web mapping**. If your data will appear in a web browser, display in a JavaScript application, or pass through an API, choose GeoJSON. If you're doing heavy spatial analysis in desktop GIS, stick with shapefiles or GeoPackage.

**Key takeaway**: Shapefiles for analysis, GeoJSON for the web.

## See Also

- Week 00: Shapefile (the desktop standard)
- Week 07: XYZ Tiles & Web Services (for high-performance web mapping)
- [geojson.io](http://geojson.io) — Create and edit GeoJSON interactively
- [RFC 7946](https://tools.ietf.org/html/rfc7946) — GeoJSON specification
