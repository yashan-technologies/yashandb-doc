The BOX2D type in YashanDB is a database built-in UDT used to store and access the two-dimensional bounding box of ST_GEOMETRY.

## Storage Attributes

|Type |Byte Length |
|--------|------|
| BOX2D  | 32   |

Definition format:

|Type |Format |Rules |
|--------|-------| -------------------------- |
| BOX2D | BOX2D  | Custom object type, size is not required |

## BOX2D Attributes

|Attribute Name |Type |Length |
|----------|--------|------|
| XMIN           | DOUBLE | 8 bytes |
| XMAX           | DOUBLE | 8 bytes |
| YMIN           | DOUBLE | 8 bytes |
| YMAX           | DOUBLE | 8 bytes |

## Usage Rules

### Usage Restrictions

The usage restrictions for BOX2D type data are as follows:

- Only applicable for HEAP tables.

- Cannot be used as a partition key for partition tables.

- Cannot be used as a GROUP BY grouping key.

### Usage Format

BOX2D types can be generated using the OBJECT method or the ST_EXTENT function, and queried using the format `table_alias.column_name.attribute_name`.

***Example*** for Heap tables

```sql
-- 1. Create a table box_table that contains BOX2D type
CREATE TABLE box_table (id INT, box BOX2D);

-- 2. Insert BOX2D object into the table
INSERT INTO box_table VALUES(1, BOX2D(1,2,3,4));

-- 3. Query BOX2D data
SELECT TB.box.xmin XMIN from box_table TB;

XMIN
----------- 
1.0E+000
```
