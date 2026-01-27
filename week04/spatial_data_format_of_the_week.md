# Spatial Data Format of the Week: Vector Datasets for Network Analysis with Topology

## The Hidden Structure in Transportation Networks

Network analysis—routing, connectivity, flow analysis—requires special attention to **topology**: how features connect. This week covers **topologically-enabled vector datasets** designed for networks, with emphasis on the foundational DIME (Dual Independent Map Encoding) format.

---

## Network Topology Fundamentals

### What is Network Topology?

In GIS, **topology describes how features relate spatially**:

- **Nodes**: Connection points (intersections, endpoints)
- **Edges**: Line segments connecting nodes (road segments, utility pipes)
- **Turns**: Rules about how you can traverse from one edge to another
- **Connectivity**: Which nodes are connected to which edges

**Non-topological approach** (traditional shapefile):
```
Lines layer:
[Road A: coordinates...]
[Road B: coordinates...]
# But are they connected? Maybe. Maybe not.
# Software doesn't "know"—must compute on the fly.
```

**Topological approach** (network dataset):
```
Edges: [ID: 1, From-Node: 10, To-Node: 11]
       [ID: 2, From-Node: 11, To-Node: 12]
Nodes: [ID: 10, Geometry...]
       [ID: 11, Geometry...]
# Connectivity is explicit. Flow analysis works correctly.
```

### Why Topology Matters for Networks

**Problem**: Finding shortest route from A to B

**Shapefile approach**:
1. Load road shapefile
2. Software calculates line intersection on-the-fly
3. Detects connected segments
4. Computes route

**Problem**: Computationally expensive, prone to errors (lines nearly touching but not exactly).

**Network dataset approach**:
1. Load pre-built network
2. Nodes and edges already defined
3. Connectivity already computed
4. Route calculation instant

**Result**: Network datasets are 100-1000× faster for routing and connectivity analysis.

---

## DIME (Dual Independent Map Encoding) Format

### Historical Context: The Census Bureau's Innovation (1967)

**The Problem**: 1960 U.S. Census mapping was analog—literally paper maps. Geographic statistics (census blocks) couldn't be easily aligned with census tracts or analyzed spatially.

**The Solution**: Don Cooke and colleagues at the **U.S. Census Bureau** developed **DIME** (Dual Independent Map Encoding) to solve the 1970 Census data release.

**Key innovation**: Represent **every street segment** as a pair of nodes with **attributes linking to census data**.

### DIME Structure

DIME records streets as **arcs** (edges) with bidirectional encoding:

```
ARC Record: [Sequence#] [From-Node] [To-Node] [Left-Block] [Right-Block] [Attributes...]
```

**Example: New Haven street segment**

```
ARC 0001
  From-Node: 10001
  To-Node:   10002
  Left-Block:  5001  ← Census block on left side
  Right-Block: 5002  ← Census block on right side
  Block-Length: 150 meters
  One-Way: N
  Type: Primary Street
```

**Key feature**: Each arc stores **census blocks on both sides**—enabling direct linkage of geographic features to census attributes.

### The New Haven Study (1967)

**Don Cooke's team** conducted the foundational test using **New Haven, Connecticut**:

- **Study area**: Entire city of New Haven (~130,000 people)
- **Network coverage**: All streets, alleys, paths
- **Census linkage**: Each street segment linked to surrounding census blocks
- **Attributes encoded**: Street type, direction restrictions, block identifiers

**Why New Haven?**
- Large enough to test completeness
- Small enough to manually verify accuracy
- Dense urban area showcasing routing complexity
- Yale University proximity (Cooke's location)

**Result**: Proof-of-concept that **topological street networks could be digitized and linked to demographic data**.

### DIME's Legacy

**DIME became the template for:**
- **TIGER (Topologically Integrated Geographic Encoding and Referencing)** — Census Bureau's nationwide digital map database
- **Network datasets in ArcGIS** — Modern network analysis
- **OpenStreetMap topology** — Contemporary crowdsourced networks

**DIME format:**
- Dominated 1970s-1990s transportation GIS
- Textual/ASCII format (human-readable but verbose)
- No longer actively used, but historical importance remains

---

## Modern Network Dataset Structures

### ESRI Network Dataset (.nd)

**What it is**: Esri's native format for network analysis, replacing DIME in ArcGIS workflows.

**Structure** (hierarchical, binary):

```
Network Dataset
├── Edges (line features)
│   ├── Street segments with impedance (length, time)
│   ├── Turn restrictions
│   └── One-way indicators
├── Nodes (point features)
│   ├── Intersections
│   └── Junctions
├── Turns (connectivity rules)
│   ├── U-turn restrictions
│   ├── Left-turn prohibitions
│   └── Turn cost (delay at intersection)
└── Attributes (linked data)
    ├── Speed limits
    ├── Toll costs
    ├── Surface type
    └── Restriction zones
```

**Key advantage over DIME**: **Turns** are explicit objects, not just edge-to-edge connections.

**Why turns matter:**
```
Street network:
    ↑
    | Main St
    |
←---+---→ Oak Ave
    |

At intersection:
- Straight: Main → Main (allowed)
- Left: Main → Oak (allowed)
- U-turn: Main → opposite Main (RESTRICTED)
- Right: Main → Oak (allowed)
```

Traditional DIME treats all four as "connected". Modern network datasets encode **turn restrictions** explicitly.

### OpenStreetMap Network Model

**OSM approach**: No formal "network dataset"—instead, **topological relationships encoded in tags**:

```
Way (edge):
  <way id="123">
    <nd ref="1001"/>  ← From-Node
    <nd ref="1002"/>  ← To-Node
    <nd ref="1003"/>
    <tag k="highway" v="residential"/>
    <tag k="oneway" v="no"/>
    <tag k="maxspeed" v="25"/>
  </way>

Node (vertex):
  <node id="1001" lat="41.309..." lon="-72.923..."/>
```

**Topology implicit**: Node references in ways define connectivity.

**Turns (from tags)**:
```
<relation type="restriction">
  <member type="way" ref="123" role="from"/>
  <member type="node" ref="1001" role="via"/>
  <member type="way" ref="456" role="to"/>
  <tag k="restriction" v="no_left_turn"/>
</relation>
```

---

## Attribute Structures for Network Analysis

### Essential Edge Attributes

**Impedance** (cost of traversing):
- **Length**: Meters (distance impedance)
- **Time**: Minutes based on speed (temporal impedance)
- **Toll**: Dollar cost (economic impedance)
- **Environmental**: CO₂ emissions (carbon impedance)

**Directionality**:
- **One-way**: Direction of travel (YES/NO/BOTH)
- **Reverse impedance**: Different cost to traverse backward (e.g., uphill vs. downhill)

**Restrictions**:
- **Height restrictions**: Tunnels (meters)
- **Weight restrictions**: Bridges (tons)
- **HOV**: High-occupancy vehicle lanes (required passengers)
- **Time-based**: Truck restrictions during rush hour

**Functional class**:
- Interstate / US Highway / State Road / Local Street
- Used for routing preferences (fastest vs. most scenic)

### Essential Turn Attributes

**Turn costs**:
- **U-turn penalty**: Delay for U-turns (seconds)
- **Turn time**: Time to execute turn (seconds)
- **Intersection delay**: General intersection cost (seconds)

**Turn restrictions**:
- **Prohibited**: No_Left_Turn, No_Right_Turn, No_U_Turn, No_Straight
- **Conditional**: Only_Right_Turn, Only_Straight
- **Time-based**: Allowed during certain hours only

**Turn types**:
- Angle (0°=U-turn, 90°=right angle, 180°=u-turn)
- Named (sharp left, gentle right, straight)

### Node Attributes

**Stop signs / signals**:
- Traffic signal presence
- Signal cycle time
- Stop sign vs. yield

**Special locations**:
- Toll booths
- Weigh stations
- Border crossings
- Rest areas

**Connectivity**:
- Number of connected edges
- Is it an intersection or endpoint?

---

## Real-World Example: Census Tract Routing

**Problem**: Find all streets bordering Census Tract 5001

**DIME solution** (1970s):
```
SELECT all arcs WHERE Left-Block = 5001 OR Right-Block = 5001
```

Instant—no spatial computation needed.

**Shapefile solution** (1990s):
```
1. Load census tracts shapefile
2. Load roads shapefile
3. Compute spatial intersection (slow)
4. Check which roads touch tract boundary
5. Result after 30+ seconds
```

**Network dataset solution** (2000s):
```
1. Load network dataset
2. Query edges where Left-Block = 5001
3. Result instant (attribute lookup, no geometry)
```

---

## Using Network Datasets in QGIS

### Creating Networks in QGIS

**QGIS has limited native network support**. Alternatives:

**Option 1: Use pgRouting (PostGIS)**
```sql
-- Create network from roads shapefile
SELECT pgr_createTopology('roads_table', 0.00001, 'geom', 'id');

-- Query connectivity
SELECT * FROM pgr_dijkstra(
  'SELECT id, source, target, ST_Length(geom) as cost FROM roads',
  1,    -- Start node
  10,   -- End node
  false -- Directed?
);
```

**Option 2: Use Processing → Network Analysis**
1. **Vector → Analysis Tools → Network Analysis**
2. Input: Line layer (roads)
3. Output: Network topology computed on-the-fly
4. QGIS creates nodes at intersections

**Option 3: Import from OpenStreetMap**
1. **Processing → Download OSM**
2. Download road network as GeoJSON
3. Use for routing/connectivity analysis

### Loading ESRI Network Datasets

**QGIS cannot directly read ESRI .nd files** (proprietary binary format).

**Workaround**:
1. In ArcGIS: Export network dataset to shapefile + attribute table
2. Load shapefile in QGIS
3. Manually encode turn restrictions in attributes
4. Use PostGIS for routing

---

## Network Dataset Comparison

| **Format**              | **Topology Explicit** | **Turns Encoded** | **Spatial Indexing** | **Routing Speed** | **Best For**                     |
|------------------------|----------------------|-------------------|----------------------|-------------------|---------------------------------|
| **Shapefile**           | ❌ No (implicit)      | ❌ No             | ✅ Yes               | Slow              | Simple visualization            |
| **DIME (ASCII)**        | ✅ Yes               | ⚠️ Implicit       | ❌ No                | Medium            | Census linkage, historical      |
| **ESRI Network Dataset**| ✅ Yes               | ✅ Yes            | ✅ Yes               | Very Fast         | Professional routing (ArcGIS)   |
| **OpenStreetMap**       | ✅ Yes               | ✅ Yes (via tags) | ✅ Yes               | Fast              | Public routing, open-source     |
| **PostGIS + pgRouting** | ✅ Yes               | ✅ Yes            | ✅ Yes               | Very Fast         | Custom routing, open-source     |

---

## When to Use Network Datasets

**Use explicit network datasets for:**
- **Routing analysis** (shortest path, vehicle routing)
- **Accessibility analysis** (service coverage, travel time)
- **Turn restrictions** (complex traffic rules)
- **Network connectivity** (outage analysis, flow)
- **Census linkage** (demographics on streets)

**Don't need explicit topology for:**
- **Simple visualization** (roads as lines)
- **Intersection counting** (how many roads meet here?)
- **Street naming** (what is this road called?)
- **One-time analysis** (convert on-the-fly)

---

## The Lasting Legacy of DIME

**DIME was revolutionary** because it:

1. **Solved the census problem**: Linked street networks to demographic data
2. **Pioneered topology**: Established that geometry + topology + attributes = power
3. **Proved feasibility**: Showed city-scale networks could be digitized accurately
4. **Inspired TIGER**: Led to nationwide digital base map

**Modern lessons from DIME**:
- **Topology matters**: Explicit connectivity is faster and more reliable than computed
- **Attributes drive analysis**: Nodes and edges without attributes are just pictures
- **Block encoding works**: Don Cooke's left-block/right-block model still dominates (e.g., TIGER, OpenStreetMap)

---

## The Bottom Line

**Network analysis requires topology**—explicit connections between nodes and edges. The DIME format (1967) pioneered this approach for the census, and modern network datasets (ESRI, OSM, pgRouting) still follow its fundamental structure.

**Key insight**: A road network is not just "a bunch of lines." It's a **graph of connected nodes with attributes**, designed for specific analysis tasks (routing, accessibility, flow).

**Modern practice**: Use PostGIS + pgRouting (open-source) or ESRI Network Datasets (professional GIS). For one-off analysis, convert shapefile to temporary network on-the-fly.

---

## See Also

- Week 3: GeoPackage & Spatial CSV — Storing network attributes
- Week 5: Relational Databases (PostGIS, SQLite) — Storing topology
- Week 7: XYZ Tiles — Visualizing networks on web
- Week 9: Python/GDAL — Processing network data
- [DIME Format Documentation](https://www.census.gov/geo/reference/archive/tiger/tigerarchive.html) — Original Census documentation
- [pgRouting Documentation](https://pgrouting.org/) — Open-source network analysis
- [OpenStreetMap Tagging for Routing](https://wiki.openstreetmap.org/wiki/Key:restriction) — Modern network encoding
