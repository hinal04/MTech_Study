# Session 14: Temporal and Spatial Databases

> BITS Pilani — **SSZG507: Modern Database Systems**

---

## Table of Contents

- [14.1 Temporal Databases Overview](#141-temporal-databases-overview)
- [14.2 Valid Time and Transaction Time](#142-valid-time-and-transaction-time)
- [14.3 Temporal Data Models](#143-temporal-data-models)
- [14.4 Time-Series Databases](#144-time-series-databases)
- [14.5 Spatial Databases Fundamentals](#145-spatial-databases-fundamentals)
- [14.6 Spatial Data Types and Relationships](#146-spatial-data-types-and-relationships)
- [14.7 Spatial Indexing and Queries](#147-spatial-indexing-and-queries)
- [14.8 PostGIS and GIS Applications](#148-postgis-and-gis-applications)

---

## 14.1 Temporal Databases Overview

### What is a Temporal Database?

A **temporal database** automatically tracks when data changes and allows reconstruction of past states.

**Key capability:** "What was the data at time X?"

### Need for Temporal Data

**Real-world example - Insurance:**

```
Policy #123:
- 2020-01-01: Premium $500/month
- 2021-06-15: Premium $550/month (increase)
- 2022-03-01: Premium $500/month (promo)
- 2024-01-01: Premium $600/month (current)

Questions:
- What was premium on 2021-12-01? → $550
- When did premium last change? → 2024-01-01
- History of policy? → All versions
- Corrections needed? → Update history
```

### Types of Temporal Data

| Type | Use Case | Example |
|---|---|---|
| **Snapshot** | Only current state | Current customer address |
| **Valid-time only** | Business effective dates | When policy applies |
| **Transaction-time only** | Audit trail | When recorded in DB |
| **Bi-temporal** | Both dimensions | Full audit + business time |

---

## 14.2 Valid Time and Transaction Time

### Valid Time

**Valid time** = when fact applies to business reality

```
Policy premium of $500 is "valid" from 2020-01-01 to 2021-06-14
During this period, premium amount is $500

Valid Time Interval: [2020-01-01, 2021-06-14]
```

**Characteristics:**
- When business truth applies
- Defined by business domain
- May extend to future (predictions)
- Multiple periods possible (non-consecutive)

### Transaction Time

**Transaction time** = when database recorded the fact

```
Recorded in DB: 2024-01-15 10:30:00
Fact: "Premium was $500 from 2020-01-01"

Transaction Time Interval: [2024-01-15 10:30:00, ∞] or [start, end]
```

**Characteristics:**
- When stored in database
- Always recorded (automatically)
- Monotonically increasing
- Once recorded, doesn't change (append-only)

### Bi-temporal Data

**Tracking both clocks simultaneously:**

```
Policy Premium:
ValidFrom: 2021-01-01, ValidTo: 2021-12-31, Amount: $500
RecordedFrom: 2020-12-28, RecordedTo: ∞

Scenario 1: Recorded before effective date
- Premium set today for future effective date
- ValidTime future, TransactionTime current

Scenario 2: Historical correction
- Recorded today, but applies to past period
- ValidTime past, TransactionTime current

Scenario 3: Correction to past recording
- Original recorded: 2021-06-15
- Correction recorded: 2024-01-15
- Same ValidTime, different TransactionTime versions
```

---

## 14.3 Temporal Data Models

### Snapshot Model

**Only store current state:**

```
Customer_Current
├─ customer_id: 1
├─ name: "Alice"
├─ address: "123 Main St"
├─ phone: "555-1234"
└─ updated_at: 2024-01-15
```

**Characteristics:**
- Simple schema
- No history
- Cannot answer "what was?"
- Only answer "what is?"

**When to use:**
- History not needed
- Storage constraints
- Real-time only

### Valid-Time Model

**Store business-effective history:**

```
Customer
├─ customer_id: 1
├─ valid_from: 2020-01-01
├─ valid_to: 2023-12-31
├─ name: "Alice"
├─ address: "123 Main St"

Customer
├─ customer_id: 1
├─ valid_from: 2024-01-01
├─ valid_to: NULL (current)
├─ name: "Alice"
├─ address: "456 Oak Ave"
```

**Characteristics:**
- Multiple rows for valid periods
- Answer "what was effective on date X?"
- Business history preserved
- May have overlapping or gap periods

### Transaction-Time Model

**Store database change audit trail:**

```
Customer_Audit
├─ customer_id: 1
├─ recorded_from: 2020-01-15 10:30:00
├─ recorded_to: 2024-01-10 14:22:00
├─ name: "Alice"
├─ address: "123 Main St"

Customer_Audit
├─ customer_id: 1
├─ recorded_from: 2024-01-10 14:22:00
├─ recorded_to: NULL (current)
├─ name: "Alice"
├─ address: "456 Oak Ave"
```

**Characteristics:**
- Append-only (never update)
- Complete audit trail
- Answer "what was recorded at database time X?"
- Answers "when did we know?"

### Bi-temporal Model

**Combine both dimensions:**

```
Policy
├─ policy_id: 1
├─ valid_from: 2021-01-01
├─ valid_to: 2022-12-31
├─ recorded_from: 2020-12-28
├─ recorded_to: 2024-01-15
├─ premium: 500

Policy
├─ policy_id: 1
├─ valid_from: 2021-01-01
├─ valid_to: 2022-12-31
├─ recorded_from: 2024-01-15
├─ recorded_to: NULL
├─ premium: 525  (correction)
```

**Enables questions:**
- "What premium was we charging on 2021-07-01?" (valid-time)
- "What did we have recorded on 2020-12-29?" (transaction-time)
- "When did we correct the 2021 premium?" (both)

---

## 14.4 Time-Series Databases

### Time-Series Characteristics

**Distinct from other data types:**

```
Time-series: [t1, v1], [t2, v2], [t3, v3], ...
- Ordered by timestamp
- Append-only (new measurements, no updates)
- Regularly spaced (often)
- Measurements decline in importance over time
- Compression possible
```

### Time-Series Use Cases

| Use Case | Measurements | Rate |
|---|---|---|
| **IoT Sensors** | Temperature, pressure, humidity | 1-100 Hz |
| **Financial** | Stock price, volume | 1 Hz - 1 KHz |
| **Monitoring** | CPU, memory, disk, network | 1 per second |
| **Healthcare** | Heart rate, blood pressure, O2 | 1-100 Hz |
| **Weather** | Temperature, precipitation | 15 min intervals |

### Time-Series Data Model

**Typical structure:**

```
Measurement: "cpu_usage"
├─ timestamp: 2024-01-15 10:00:00
├─ server_id: "server-01"
├─ value: 42.5
├─ unit: "percent"
└─ tags: ["production", "us-east-1"]

Measurement: "cpu_usage"
├─ timestamp: 2024-01-15 10:01:00
├─ server_id: "server-01"
├─ value: 45.2
├─ unit: "percent"
└─ tags: ["production", "us-east-1"]
```

### Time-Series Optimization

**Time Partitioning:**
- Divide by time (hourly, daily)
- Partition pruning on queries
- Easier archival

```
/metrics/cpu_usage/2024/01/15/hour_10/data.parquet
/metrics/cpu_usage/2024/01/15/hour_11/data.parquet
/metrics/cpu_usage/2024/01/15/hour_12/data.parquet
```

**Downsampling/Aggregation:**
```
Raw: Every 1 second → 86,400 points/day
1-min average: → 1,440 points/day
1-hour average: → 24 points/day

Keep raw recent, aggregate older data
```

**Compression:**
- Delta encoding (store differences)
- Dictionary encoding (repeated values)
- Gorilla compression (time-series specific)

### Time-Series Databases

**Examples:**
- InfluxDB
- Prometheus
- TimescaleDB (PostgreSQL extension)
- Graphite
- Datadog

---

## 14.5 Spatial Databases Fundamentals

### What is Spatial Data?

**Spatial data** represents objects in space with location information.

```
Point: Single location
├─ Latitude: 37.7749°N
└─ Longitude: -122.4194°W
  (San Francisco)

Line: Path between points
├─ [37.7749, -122.4194]
├─ [37.7850, -122.4000]
├─ [37.7900, -122.3850]
  (Route)

Polygon: Closed area
├─ [37.7700, -122.4300]
├─ [37.7800, -122.4300]
├─ [37.7800, -122.4100]
├─ [37.7700, -122.4100]
├─ [37.7700, -122.4300]
  (Service area)
```

### Coordinate Systems

**Geographic coordinate system (WGS84 / EPSG:4326):**
- Latitude: -90 to +90 degrees
- Longitude: -180 to +180 degrees
- Uses: GPS, mapping, long-distance

```
Example: San Francisco
Latitude: 37.7749°N
Longitude: 122.4194°W
Or: (37.7749, -122.4194)
```

**Projected coordinate system:**
- Meters from origin
- Lower distortion at specific area
- Uses: Local surveying, urban planning

### Distance Calculation

**Great-circle distance (Haversine formula):**

```
Calculates shortest distance between two points on sphere

Distance = 2 * R * arcsin(√(sin²(Δφ/2) + cos(φ1) * cos(φ2) * sin²(Δλ/2)))

Where:
- R = Earth radius (6371 km)
- φ = latitude
- λ = longitude
```

---

## 14.6 Spatial Data Types and Relationships

### Spatial Data Types

**OGC (Open Geospatial Consortium) types:**

| Type | Representation | Example |
|---|---|---|
| **Point** | Single coordinate | Store location |
| **LineString** | Sequence of points | Road, path |
| **Polygon** | Closed ring | Building, territory |
| **MultiPoint** | Multiple points | Multiple stores |
| **MultiLineString** | Multiple lines | Road network |
| **MultiPolygon** | Multiple polygons | Archipelago |

### WKT (Well-Known Text)

**Human-readable spatial format:**

```
POINT (37.7749 -122.4194)

LINESTRING (0 0, 1 1, 2 2)

POLYGON ((0 0, 10 0, 10 10, 0 10, 0 0))

MULTIPOINT ((0 0), (1 1), (2 2))
```

### Spatial Relationships

| Relationship | Definition | Example |
|---|---|---|
| **Contains** | A fully contains B | Building contains room |
| **Within** | A fully inside B | Room within building |
| **Intersects** | A and B overlap | Roads intersect |
| **Touches** | A and B touch at boundary | Neighborhoods border |
| **Crosses** | A and B cross | Road crosses river |
| **Disjoint** | A and B don't overlap | Separate locations |
| **Equals** | A and B same geometry | Duplicate shapes |

**Spatial predicates (PostGIS functions):**

```sql
-- Contains: Does polygon contain point?
SELECT * FROM buildings 
WHERE ST_Contains(geometry, ST_Point(37.7749, -122.4194));

-- Within: Is point within distance?
SELECT * FROM stores 
WHERE ST_DWithin(geometry, search_point, 1000);  -- 1km

-- Intersects: Do geometries overlap?
SELECT * FROM routes 
WHERE ST_Intersects(geometry, search_area);
```

---

## 14.7 Spatial Indexing and Queries

### Spatial Indexing Problem

**Problem:** Finding nearby objects

```
Database has 1M locations
Query: "Find restaurants within 1km of point (37.7749, -122.4194)"

Brute force: Check all 1M locations (slow)
Indexed: Use spatial index (fast)
```

### R-tree Index

**Hierarchical rectangle-based index:**

```
Root
├─ BBox1 (covering West region)
│  ├─ BBox1.1 (covering part of West)
│  │  └─ Leaf: Point1, Point2, Point3
│  └─ BBox1.2 (covering other part)
│     └─ Leaf: Point4, Point5
│
└─ BBox2 (covering East region)
   ├─ BBox2.1
   │  └─ Leaf: Point6, Point7
   └─ BBox2.2
      └─ Leaf: Point8, Point9, Point10
```

**Query: Find points within search area:**
1. Check if search area intersects root's BBox
2. Recursively check sub-boxes that intersect
3. Return points in intersecting leaves

**Advantages:**
- Efficient spatial range queries
- Handles overlapping boxes well
- Scales to millions of objects

### GiST (Generalized Search Tree)

**PostgreSQL's generalized indexing structure:**

```
Can index any data type with:
- Key representation
- Consistency check
- Compression
```

Used for spatial data in PostGIS.

### Spatial Query Examples

**Find nearby restaurants:**
```sql
SELECT name, ST_Distance(geometry, search_point) as distance
FROM restaurants
WHERE ST_DWithin(geometry, search_point, 1000)  -- 1 km
ORDER BY distance
LIMIT 10;
```

**Find customers in service area:**
```sql
SELECT customer_id, customer_name
FROM customers
WHERE ST_Contains(
  (SELECT geometry FROM service_areas WHERE area_id = 123),
  customer_location
);
```

**Find intersecting geometries:**
```sql
SELECT r1.id, r2.id
FROM routes r1, routes r2
WHERE r1.id < r2.id
  AND ST_Intersects(r1.geometry, r2.geometry);
```

---

## 14.8 PostGIS and GIS Applications

### PostGIS Extension

**PostGIS** adds spatial data types and functions to PostgreSQL:

```sql
-- Enable PostGIS
CREATE EXTENSION postgis;

-- Create table with geometry column
CREATE TABLE restaurants (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  geometry GEOMETRY(Point, 4326),  -- Point with WGS84
  rating INT
);

-- Add spatial index
CREATE INDEX idx_restaurant_location ON restaurants USING GIST(geometry);

-- Insert location
INSERT INTO restaurants (name, geometry) 
VALUES ('Pizza Place', ST_Point(-122.4194, 37.7749));
```

### GIS Applications

**GIS (Geographic Information System):**

| Application | Use Case |
|---|---|
| **Mapping/Navigation** | Google Maps, Waze |
| **Delivery Services** | Route optimization |
| **Real Estate** | Property search, neighborhood analysis |
| **Urban Planning** | City development, zoning |
| **Environmental** | Conservation, habitat monitoring |
| **Retail** | Store locator, site selection |
| **Utilities** | Infrastructure management |
| **Transportation** | Traffic analysis, public transit |

### Real-World Example: EV Charging Station Locator

```sql
-- Find nearest EV chargers
WITH user_location AS (
  SELECT ST_Point(-122.4194, 37.7749) as location
)
SELECT 
  charger_id,
  station_name,
  ST_Distance(geometry, user_location.location) / 1000 as distance_km,
  available_ports
FROM ev_chargers, user_location
WHERE ST_DWithin(geometry, user_location.location, 5000)  -- 5 km
ORDER BY distance_km
LIMIT 5;

-- Find cities in service area
SELECT city_name, state
FROM cities
WHERE ST_Contains(
  ST_Buffer(service_center_location, 50000),  -- 50 km radius
  city_center_location
);
```

### Spatial Analysis Operations

**Buffer (create zone around geometry):**
```sql
-- Create 1km zone around store
SELECT ST_Buffer(store_location, 1000) as service_zone
FROM stores WHERE store_id = 1;
```

**Union (combine geometries):**
```sql
-- Combine all service zones
SELECT ST_Union(geometry)
FROM service_zones
WHERE area_manager = 'John';
```

**Intersection (find overlap):**
```sql
-- Find customers in overlapping areas
SELECT c.id
FROM customers c
WHERE ST_Intersects(c.location, (
  SELECT ST_Intersection(a.geometry, b.geometry)
  FROM areas a, areas b
  WHERE a.id = 1 AND b.id = 2
));
```

---

## Summary

Temporal and spatial databases handle specialized data types:

**Temporal Databases:**
1. **Valid time vs. transaction time** - Business-effective dates vs. database recording dates
2. **Data models** - Snapshot, valid-time, transaction-time, bi-temporal
3. **Time-series databases** - Optimized for append-only measurements
4. **Optimization** - Partitioning, downsampling, compression

**Spatial Databases:**
1. **Spatial data types** - Points, lines, polygons, collections
2. **Coordinate systems** - Geographic (lat/long) and projected
3. **Spatial relationships** - Contains, within, intersects, touches
4. **Spatial indexing** - R-tree and GiST for efficient queries
5. **GIS applications** - Mapping, delivery, real estate, urban planning

Modern applications increasingly leverage both temporal and spatial dimensions for richer data insights and real-world applicability.
