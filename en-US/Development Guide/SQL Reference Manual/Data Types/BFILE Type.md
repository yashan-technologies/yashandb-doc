The BFILE type in YashanDB is a variable-length data type that stores special types of binary large objects, primarily used for handling binary files stored outside the database. 

## Storage Attributes

|Type |Byte Length |
|--------|------------|
| BFILE  | Row Storage: Maximum 328 bytes |

The BFILE type in YashanDB stores a reference to a physical file in the operating system rather than the content itself. The actual files are stored in a file system outside the database, and the database only records the file path and name. 

Definition format:

|Type |Format |Rules |
|--------|-------|-----------------|
| BFILE  | BFILE | Variable-length binary string, no size specification required |

## Usage Rules

### Usage Restrictions

- Applicable only to HEAP tables.

- When using the BFILE type, a file path alias must be created; specifications can refer to [CREATE DIRECTORY](../SQL Statements/CREATE DIRECTORY).

- BFILE type usage restrictions can be referred to in [LOB Usage Restrictions](Large Object Types.md#lob_use_restrictions).

- The BFILE field supports only read operations on operating system files and cannot modify their content through the database. File maintenance must be done externally to the database.

- The BFILE type does not participate in database transactions, and file modifications will not be rolled back, so manual data consistency must be ensured.

### Parameter Specifications

The number of file handles opened by BFILE is affected by the session-level parameter SESSION_MAX_OPEN_FILES, with a default value of 50.

### Usage Method

The BFILE type defines a BFILE locator through the built-in function [BFILENAME](../Built-in Functions/BFILENAME) for inserting operations, and file data operations can be performed using related advanced package functionality [DBMS_LOB](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_LOB.md#dbms_lob_bfile).

***Example*** for Heap tables

```sql
-- 1. Create tbl_bfile table with BFILE field under sales user
CREATE TABLE tbl_bfile (col1 bfile); 

-- 2. Create corresponding directory and prepare test.txt data
CREATE OR REPLACE DIRECTORY my_dir AS '/data/yashan';
!echo "this is test bfile file here" >> /data/yashan/test.txt       

-- 3. Add data to tbl_bfile table
INSERT INTO tbl_bfile VALUES(bfilename('MY_DIR', 'test.txt'));

-- 4. View data in tbl_bfile table
SELECT * FROM tbl_bfile;

-- 5. View corresponding file data for tbl_bfile table
SELECT cast(col1 as varchar(1000)) FROM tbl_bfile;
```
