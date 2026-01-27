# Spatial Data Format of the Week: Database Formats with SQL Support

## GeoPackage, Spatialite, and PostGIS: Queryable Spatial Databases

This week covers **database formats** that support SQL queries—moving beyond simple file formats to structured databases. We'll explore **GeoPackage** (modern file-based), **Spatialite** (legacy file-based), and **PostGIS** (server-based), all accessible through **QGIS DB Manager**.

---

## Why Database Formats?

### The Shapefile Limitation

**Shapefiles** are great for sharing data, but they're just files—no built-in querying, no relationships between layers, no complex analysis without loading everything into memory.

**Database formats** provide:
- **SQL queries**: Filter and analyze without loading entire dataset
- **Multiple layers**: Store related data in one container
- **Relationships**: Link tables without manual joins
- **Spatial indexing**: Fast spatial queries on large datasets
- **Transactions**: Data integrity (all-or-nothing changes)

### Example: Simple Attribute Query

```sql
SELECT name, population, geometry
FROM counties
WHERE population > 1000000;
```

### Example: Spatial Join

```sql
SELECT 
  c.county_name,
  COUNT(s.school_id) as num_schools
FROM counties c
LEFT JOIN schools s ON ST_Contains(c.geometry, s.geometry)
GROUP BY c.county_name;
```

**These queries work identically in GeoPackage, Spatialite, and PostGIS through QGIS DB Manager.**  

---

## QGIS DB Manager: Universal Database Interface

### What is DB Manager?

**DB Manager** is QGIS's built-in tool for working with all database formats:
- **GeoPackage** (.gpkg files)
- **Spatialite** (.sqlite, .db files)
- **PostGIS** (PostgreSQL servers)
- **Virtual Layers** (query loaded QGIS layers without database)

**Access**: **Database → DB Manager** (menu) or **Ctrl+Shift+D**

### DB Manager Interface

**Left panel**: Tree view of database providers and their contents  
**Right panel**: SQL query window, table preview, and schema information  
**Bottom**: Execute button and "Load as layer" option

### Basic Workflow

1. **Open DB Manager**: Database → DB Manager
2. **Navigate**: Expand provider (GeoPackage, Spatialite, PostGIS)
3. **Select database**: Click on your database/file
4. **Inspect**: View table structure, preview data
5. **Query**: Click SQL Window button, write query, execute
6. **Load results**: Click "Load as layer" to add to map

### SQL Query Results

**Two ways to view results:**
- **Table view**: See query results in DB Manager (no map layer)
- **Load as layer**: Add results to QGIS map canvas

**Loading as layer** is essential for spatial queries—results appear as temporary or saved layers you can style and analyze.

---

## GeoPackage: File-Based SQL Database

### What is GeoPackage?

**GeoPackage** (.gpkg) is a **SQLite database** with spatial extensions:
- Single file (portable)
- Multiple layers in one file
- Full SQL support
- Spatial indexing
- QGIS native format

### Creating GeoPackage in QGIS

**New GeoPackage:**
1. Right-click layer → **Export → Save Features As**
2. Format: **GeoPackage**
3. File name: `mydata.gpkg`
4. Layer name: `counties`
5. Click **OK**

**Add more layers to same GeoPackage:**
1. Export another layer
2. Choose **same .gpkg file**
3. New layer name: `schools`
4. Choose **"Add to existing GeoPackage"**

### Querying GeoPackage with DB Manager

**Open in DB Manager:**
1. **Database → DB Manager**
2. Expand **GeoPackage**
3. Find your `.gpkg` file
4. Expand to see all layers (tables)
5. Click **SQL Window**

**Query multiple layers in same GeoPackage:**
```sql
-- Schools per county with demographics
SELECT 
  c.county_name,
  c.population,
  c.median_income,
  COUNT(s.school_id) as num_schools,
  AVG(s.enrollment) as avg_enrollment,
  c.geometry
FROM counties c
LEFT JOIN schools s ON ST_Contains(c.geometry, s.geometry)
GROUP BY c.county_name, c.population, c.median_income, c.geometry
HAVING num_schools > 5;
```

### Spatial Indexes in GeoPackage

**GeoPackage automatically creates spatial indexes**—but you can rebuild if needed:

**In DB Manager:**
```sql
-- Check if spatial index exists
SELECT * FROM gpkg_spatial_index WHERE table_name = 'counties';

-- Rebuild spatial index (if corrupted)
SELECT DisableSpatialIndex('counties', 'geometry');
SELECT CreateSpatialIndex('counties', 'geometry');
```

### GeoPackage Advantages

✅ **Single file**: Easy to share, backup, move  
✅ **Multiple layers**: One database for entire project  
✅ **Full SQL**: All SQLite features  
✅ **Fast**: Spatial indexing for quick queries  
✅ **Open standard**: OGC specification  
✅ **QGIS native**: Best support in QGIS  

### GeoPackage Limitations

❌ **Single-user**: File locking prevents concurrent writes  
❌ **No server**: Must copy file for remote access  
❌ **Size**: Slower than PostGIS for >1M features  

---

## Spatialite (.sqlite, .db): Legacy File-Based Database

### What is Spatialite?

**Spatialite** is the **predecessor to GeoPackage**:
- **SQLite database** with spatial extensions
- **Older format** (.sqlite or .db extension)
- **Different SQL syntax** than GeoPackage/PostGIS
- **Still encountered** in legacy projects

### Structure

Similar to GeoPackage (single file, multiple tables), but older standard and different internal organization.

### Using Spatialite in QGIS

**Create new Spatialite database:**
1. **Database → DB Manager**
2. Expand **Spatialite** provider
3. Right-click → **New Database**
4. Name: `mydata.sqlite`

**Load existing Spatialite:**
1. **Layer → Add Layer → Add Spatialite Layer**
2. Browse to `.sqlite` or `.db` file
3. Select table

**Query in DB Manager:**
- Same workflow as GeoPackage
- Expand Spatialite provider
- Navigate to database
- Use SQL Window

### Key Differences from GeoPackage

**Syntax differences** (Spatialite uses different function names):
- `ST_Contains()` vs. `Contains()` 
- Boolean returns `= 1` instead of `TRUE`
- Some functions not available

**Manual spatial indexes:**
- GeoPackage: automatic
- Spatialite: must create manually

**Legacy format:**
- Spatialite: older, less support
- GeoPackage: modern OGC standard

### GeoPackage vs. Spatialite

| **Aspect**          | **GeoPackage**        | **Spatialite**          |
|---------------------|-----------------------|-------------------------|
| **Standard**        | OGC (modern)          | Legacy                  |
| **File extension**  | .gpkg                 | .sqlite, .db            |
| **QGIS support**    | Native, excellent     | Good                    |
| **Spatial indexes** | Automatic             | Manual creation         |
| **SQL syntax**      | PostGIS-like (ST_*)   | Different (no ST_ prefix)|
| **Recommendation**  | **Use for new work**  | Legacy/compatibility    |

### When to Use Spatialite

**Use Spatialite for:**
- Opening legacy databases from older projects
- Compatibility with specific software requiring Spatialite
- Converting old Spatialite to GeoPackage

**Don't use for new projects**: GeoPackage is the modern replacement with better support and standardized SQL

**Add more layers:**
1. Export another layer
2. Choose **Server-Based Spatial Database

### What is PostGIS?

**PostGIS** = **PostgreSQL** (enterprise database server) + **spatial extensions**

**Key difference from GeoPackage/Spatialite:**
- **Server-based** (not a file—runs as continuous service)
- **Multi-user** (many people query simultaneously)
- **Networked** (access from anywhere with credentials)
- **Enterprise-scale** (millions of features, terabytes of data)

### Architecture

```
PostgreSQL Server (remote or local)
├── Database: myproject
│   ├── counties (spatial table)
│   ├── schools (spatial table)
│   └── roads (spatial table)
├── Database: otherproject
└── Users, permissions, backups
```

**Not a file you copy**—it's a server you connect to.

### Connecting QGIS to PostGIS

**One-time setup:**
1. **Database → DB Manager**
2. Expand **PostGIS** provider
3. Right-click → **New Connection**
4. Enter connection details:
   - **Name**: MyServer (your label)
   - **Host**: localhost or server address
   - **Port**: 5432 (default)
   - **Database**: myproject
   - **Username/Password**: provided by admin
5. **Test Connection** → **OK**

**Using connection:**
1. Expand **PostGIS** in DB Manager
2. Select your connection (e.g., "MyServer")
3. Expand database to see tables
4. Query with SQL Window (same as GeoPackage)

**Loading layers:**
- **Layer → Add Layer → Add PostGIS Layers**
- Select connection, choose tables, click **Add**
- Layers remain on server (not copied to local file)

### PostGIS in DB Manager

**Same SQL interface as GeoPackage**, but:
- Queries run on remote server
- Results can be very large (database handles it)
- Can create views, indexes, triggers (advanced features)
- Multiple users can query simultaneously

**Workflow identical to GeoPackage:**
1. Open DB Manager
2. Expand PostGIS → your connection → database
3. Click SQL Window
4. Write query
5. Execute and load as layer

### PostGIS Advantages

✅ **Multi-user**: Team collaboration (concurrent access)  
✅ **Scalable**: Millions of features, no performance loss  
✅ **Server-based**: Access from anywhere (network)  
✅ **Advanced features**: Materialized views, triggers, procedures  
✅ **Enterprise-grade**: Backup, replication, security  
✅ **Open-source**: Free (PostgreSQL + PostGIS)  

### PostGIS Limitations

❌ **Server setup**: Requires IT infrastructure  
❌ **Network dependency**: Must connect to server  
❌ **Learning curve**: Server management + SQL  
❌ **Overkill for solo work**: GeoPackage simpler  

### When to Use PostGIS

**Use PostGIS for:**
- Team projects (multiple people editing/querying)
- Large datasets (millions of features)
- Server deployment (web services, APIs)
- Enterprise workflows (backup, security requirements)

**Don't use for:**
- Solo projects (GeoPackage easier)
- Field work without network (GeoPackage portable)
- Learning SQL (start with GeoPackage)
✅ **Advanced features**: Triggers, stored procedures, materialized views  
✅ **Enterprise-grade**: ACID compliance, replication, backup  
✅ **Open-source**: Free, community support  

### PostGIS Limitations

❌ **Server setup**: Requires installation, configuration  
❌ **Network dependency**: Need connection to server  
❌ **Learning curve**: SQL + server management  
❌ **Overkill for small projects**: GeoPackage simpler  

---

## Comparison: Which SQL Solution to Use?

| **Solution**         | **Best For**                              | **Server?** | **Multi-User** | **File Size** | **Setup Effort** |
|----------------------|-------------------------------------------|-------------|----------------|---------------|------------------|
| **Virtual Layers**   | Quick one-off queries                     | No          | No             | N/A           | None (built-in)  |
| **GeoPackage**       | Single-user projects, field work          | No          | Limited        | <1GB optimal  | Very easy        |
| **Spatialite**       | Legacy compatibility                      | No          | No             | <500MB        | Easy             |
| **PostGIS**          | Team collaboration, large datasets        | Yes         | Yes            | Unlimited     | Moderate-High    |

---

## Real-World Workflow Examples

### Example 1: Point-in-Polygon (Schools in Counties)
Database Format to Use?

| **Format**           | **Type**      | **Best For**                              | **Multi-User** | **Typical Size**  | **Setup** |
|----------------------|---------------|-------------------------------------------|----------------|-------------------|-----------|
| **GeoPackage**       | File          | Single-user projects, field work, learning| No             | <1GB              | None      |
| **Spatialite**       | File          | Legacy databases only                     | No             | <500MB            | None      |
| **PostGIS**          | Server        | Team collaboration, large datasets        | Yes            | Unlimited         | Medium    |

---

## Virtual Layers: Query Without Database

### What are Virtual Layers?

**Virtual Layers** let you run SQL on **currently loaded QGIS layers** without creating a database file. Useful for one-off queries.

**Create Virtual Layer:**
1. **Layer → Add Layer → Add/Edit Virtual Layer**
2. Check **"Embedded layers"** (uses loaded layers)
3. Reference layers by name in SQL
4. Click **Add**

**Limitations:**
- Temporary (not saved when QGIS closes)
- No spatial indexes (slower than database)
- Only works on loaded layers

**When to use**: Quick spatial joins, testing queries before committing to database format.QL. **Graduate to GeoPackage** for projects. **Use PostGIS** when team collaboration or scale demands it.

---

## See Also

- Week 4: Network Datasets — Topology queries with SQL
- Week 5: Raster Analysis — Focus on vector SQL before raster work
- Week 7: Web Services — Serving PostGIS data as WFS
- Week 9: Python/GDAL — Programmatic SQL queries
- [PostGIS Documentation](https://postgis.net/docs/) — Complete SQL spatial function reference
- [SQLite Spatial Functions](https://www.gaia-gis.it/gaia-sins/spatialite-sql-latest.html) — Spatialite/GeoPackage reference
Common SQL Spatial Functions

**These work in GeoPackage, Spatialite (without ST_ prefix), and PostGIS:**

```sql
ST_Contains(polygon, point)        -- Point inside polygon?
ST_Intersects(geom1, geom2)        -- Geometries overlap?
ST_Distance(geom1, geom2)          -- Distance between features
ST_DWithin(geom1, geom2, distance) -- Within specified distance?
ST_Buffer(geometry, distance)      -- Create buffer zone
ST_Area(polygon)                   -- Calculate area
ST_Length(line)                    -- Calculate length
```

**Full reference**: [PostGIS Function Documentation](https://postgis.net/docs/reference.html)

---

## The Bottom Line

**Database formats enable SQL queries on spatial data:**

- **GeoPackage**: Modern file-based database—**use this for most projects**
- **Spatialite**: Legacy format—only for opening old databases
- **PostGIS**: Server-based—for team collaboration and large datasets
- **QGIS DB Manager**: Universal interface for all three formats

**Recommendation:**
1. **Start with GeoPackage** (easy, portable, modern)
2. **Learn SQL in DB Manager** (same interface for all formats)
3. **Migrate to PostGIS** only when you need multi-user access or have millions of features

---

## See Also

- Week 4: Network Datasets — Network topology in databases
- Week 5: Raster Analysis — Moving from database queries to raster operations
- Week 7: Web Services — Serving database layers as WMS/WFS
- Week 9: Python/GDAL — Scripting database queries
- [PostGIS Documentation](https://postgis.net/docs/) — SQL spatial function reference
- [GeoPackage Specification](https://www.geopackage.org/) — OGC standard documentation