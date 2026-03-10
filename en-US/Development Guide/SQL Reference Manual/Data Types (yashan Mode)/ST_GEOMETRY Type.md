The ST_GEOMETRY type in YashanDB is a built-in UDT (User-Defined Type) used for storing and accessing geometric objects that comply with the SFA SQL standards set by the Open Geospatial Consortium (OGC).

## Storage Properties

|**Type** |Byte Length |
|--------|---------|
| ST_GEOMETRY   | 4GB |

## Definition Format

The definition format is `ST_GEOMETRY(SPATIAL TYPE, SRID)` or `ST_GEOMETRY(SPATIAL TYPE)` or `ST_GEOMETRY`, where the meanings of SPATIAL TYPE and SRID are as follows:

*   SPATIAL TYPE: Represents the spatial type of ST_GEOMETRY, restricting the data in the column to a specific subtype and dimension. If the subtype is GEOMETRY, the data in the column can be any subtype. SPATIAL TYPE is specified by a suffix to indicate coordinate dimension restrictions; currently, only the suffix Z for three-dimensional coordinates is supported. If there is no suffix, the data dimension is restricted to two-dimensional.
*   SRID: Represents the spatial reference system identifier, used to restrict the SRID of the data in the column. It can be omitted, and the default is 0. The parameter value type is INTEGER, and the range of SRID is the same as that of INTEGER, but if the SRID value is less than 0, it is treated as 0.
    * Specifying SRID as 0 (or less than 0): Indicates that there is no restriction on the SRID of the data in the column.
    * Specifying SRID greater than 0: Indicates that the SRID of the data in the column is restricted to the specified value. If the SRID of the data being inserted is 0 or equal to the specified value, the inserted data's SRID will be uniformly saved as the specified SRID value; otherwise, an error will be raised, and the data insertion will fail.

***Example*** for Heap tables

```sql
--1. Create a table position containing ST_GEOMETRY type, pos1 does not restrict subtype, dimension, and SRID; pos2 restricts the subtype to POINT and only supports two-dimensional coordinates, with no restriction on SRID; pos3 does not restrict the subtype, restricts the dimension to three-dimensional, and SRID to 4326.
CREATE TABLE position (id INT, pos1 ST_GEOMETRY, pos2 ST_GEOMETRY(POINT), pos3 ST_GEOMETRY(GEOMETRYZ, 4326));

--2. Insert data with the subtype LINESTRING into pos2; since it does not meet the POINT subtype, a Geometry type (LINESTRING) does not match column type (POINT) error will occur.
INSERT INTO POSITION(pos2) VALUES(ST_GEOMFROMTEXT('LINESTRING(1 2, 2 2)'));
YAS-07202 plugin execution error, Geometry type (LINESTRING) does not match column type (POINT)

--3. Insert two-dimensional data with subtype POINT into pos3; since it does not contain Z coordinates, a Column has Z dimension but geometry does not error will occur.
INSERT INTO POSITION(pos3) VALUES(ST_GEOMFROMTEXT('POINT(1 1)'));
YAS-07202 plugin execution error, Column has Z dimension but geometry does not

--4. Insert three-dimensional data with SRID of 3857 into pos3; since it does not meet the specified SRID, a Geometry SRID (3857) does not match column SRID (4326) error will occur.
INSERT INTO POSITION(pos3) VALUES(ST_GEOMFROMTEXT('POINT(1 1 1)', 3857));
YAS-07202 plugin execution error, Geometry SRID (3857) does not match column SRID (4326)

--5. Insert three-dimensional data with SRID of 0 into pos3; since the column's SRID is specified as 4326, the data's SRID will be converted to 4326.
INSERT INTO POSITION(pos3) VALUES(ST_GEOMFROMTEXT('POINT(1 1 1)', 0));

SELECT ST_SRID(pos3) FROM position;
ST_SRID(POS3) 
------------- 
         4326
```

## ST\_GEOMETRY Attributes

|**Attribute Name** |Type |Length |
|--------|------| -------------------------- |
| HEAD   | RAW | 40 bytes |
| GEOM   | BLOB| < 4G |

## Supported Subtypes of ST\_GEOMETRY

- POINT

  A point represents a single coordinate in the coordinate space.

  ```sql
  POINT (1 2)
  POINT Z (1 2 3)
  ```

- LINESTRING

  A line is composed of connected line segments, each defined by two points, with points separated by a comma.

  ```sql
  LINESTRING (1 2,4 5)
  ```

- POLYGON

  A polygon consists of an outer ring (a closed LINESTRING) and 0 or more inner rings (closed LINESTRINGS), with commas separating the outer and inner rings.

  ```sql
  POLYGON ((1 0,1 1,2 2,1 0),(0 0,6 6,8 8,0 0))
  ```

- MULTIPOINT

  Represents a collection of points, with points separated by commas.

  ```sql
  MULTIPOINT ((1 1),(2 2))
  ```

- MULTILINESTRING

  Represents a collection of lines, with lines separated by commas.

  ```sql
  MULTILINESTRING ((1 2,4 5),(2 3,5 6))
  ```

- MULTIPOLYGON

  Represents a collection of polygons, with polygons separated by commas.

  ```sql
  MULTIPOLYGON (((1 5, 4 3, 6 6, 2 6, 1 5)), ((6 5, 8 8, 6 9, 6 5)))
  ```

- GEOMETRYCOLLECTION

  A GEOMETRYCOLLECTION can consist of different ST_GEOMETRY subtypes, with subtypes separated by commas.

  ```sql
  GEOMETRYCOLLECTION (POINT (1 0),LINESTRING (1 2,4 5))
  ```

## Usage Rules

### Usage Restrictions

The usage restrictions for ST_GEOMETRY type data are as follows:

- Cannot be used as the partition key of a partition table.
- Only applicable to HEAP tables.
- Only RTREE indexes can be created on ST_GEOMETRY types; other types of indexes cannot be created.

### Usage Format

ST_GEOMETRY type can be input through OGC-compliant exchange formats (e.g., WKT, WKB) and can also be converted to exchange formats like WKT and WKB.

#### WKT

WKT (Well-Known Text) format is a standard format for describing spatial data. Sample formats include:

```sql
POINT(2 5)
LINESTRING((1 3),(4 5))
POLYGON ((1 0,1 1,2 2,1 0),(0 0,6 6,8 8,0 0))
```

#### WKB

WKB (Well-Known Binary) format is a binary format for describing spatial data. The format corresponds to the WKT format as follows:

```sql
WKT:POINT(5 0)
WKB:010100000000000000000014400000000000000000

WKT:LINESTRING (1 2,4 5)
WKB:010200000002000000000000000000F03F000000000000004000000000000010400000000000001440
```

***Example*** for Heap tables

```sql
--1. Create a table position_wkb containing ST_GEOMETRY type
CREATE TABLE position_wkb (id INT, pos ST_GEOMETRY);

--2. Insert ST_GEOMETRY object via WKT format
INSERT INTO position_wkb 
VALUES (1, ST_GEOMFROMTEXT('POINT(0 0)'));
COMMIT;

--3. Insert ST_GEOMETRY object via WKB format
INSERT INTO position_wkb
VALUES (2, ST_GEOMFROMWKB('0101000000000000000000F03F000000000000F03F'));
COMMIT;

--4. Query ST_GEOMETRY object in WKT format
SELECT id, ST_ASTEXT(pos) FROM position_wkb;

ID           ST_ASTEXT(POS)
------------ ----------------------------------------------------------------
           1 POINT (0.000000000000000 0.000000000000000)
           2 POINT (1.000000000000000 1.000000000000000)
```

## Features of ST\_GEOMETRY

**Coordinates**

Each coordinate has an X and a Y value to determine its position in the plane. A shape is constructed from points or line segments, where a point is specified by a single coordinate, and a line segment is defined by two coordinates.

Coordinates can include optional Z and M values. Z is commonly used to represent height. M is a measure and can represent time or distance. If Z or M values appear in the ST_GEOMETRY value, they must be defined for every point in the ST_GEOMETRY value. If the ST_GEOMETRY type data has Z or M coordinates, the coordinate dimension is 3D; if it has both Z and M, the coordinate dimension is 4D.

**Coordinate Reference System**

All spatial operations on ST_GEOMETRY type data use the units of the coordinate reference system in which the data resides. The coordinate reference system is identified by the SRID number. The units for the X and Y axes are determined by the coordinate reference system. In a planar reference system, the X and Y coordinates typically represent east and north, while in geodetic systems, they represent longitude and latitude. SRID=0 indicates an infinite Cartesian plane with no units on its axes.

**Dimension**

Dimension is an attribute of ST_GEOMETRY type. The dimension of point types is 0, linear types is 1, and polygon types is 2. The dimension of a collection is equal to the maximum dimension of its elements.

YashanDB currently supports up to three-dimensional coordinates; if four-dimensional coordinates are input, the fourth coordinate will be ignored.

**Bounding Box**

A bounding box containing ST_GEOMETRY coordinates in two or three dimensions represents the range of the ST_GEOMETRY type data in the coordinate space and serves as an effective method to check if two ST_GEOMETRY type data interact.