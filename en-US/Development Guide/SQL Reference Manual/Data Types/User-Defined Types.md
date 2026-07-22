YashanDB supports the creation of a [UDT](../../PL Reference Manual/PL Objects/User-Defined Types) (UDT) object through PL syntax. A successfully created UDT object can be declared as a data type for column fields.

User-defined UDTs can only be used on HEAP tables and do not participate in conversions and operations between standard scalar data types. The following restrictions apply:

- It is not allowed to change the data type of a column field to UDT, or to change UDT to another type.
- UDT fields cannot be used as partition columns, index columns, or foreign key columns.
- UDT fields cannot be used as a GROUP BY grouping key.
- Queries should not use direct field name specifications, but should follow the methods described in this document.
- When a non-partition Nested Table column is defined for a partition table, dropping/truncating that table's partition is not allowed.
- The recycle bin for tables containing Nested Table columns is invalid, meaning flashback is not possible.
- Bulk insert operations cannot be performed on tables with UDT types.

## Object Type

Object is an abstract data type that embodies the concept of object-oriented programming, containing attributes and methods.

YashanDB supports storing object-structured data represented by an Object as column fields in physical tables.

When defining an Object type that does not include Nested Table type attributes:

- The declaration method for Object types is the same as for ordinary scalar data types, and data can be inserted or updated using the object initialization method.
- Object type data can be queried using the format: table alias.column name.attribute name.

When defining an Object type that includes Nested Table type attributes:

- Declare and use it according to the methods described in [Nested Table type](#nestedtable).

***Example*** for Heap tables

```sql
DROP TABLE IF EXISTS city;
-- Create an Object UDT, containing population and province as attributes
CREATE OR REPLACE TYPE obj_city AS OBJECT(population INT, province VARCHAR(20));
/

-- Create the city table and insert data
CREATE TABLE city (id INT, info obj_city);
INSERT INTO city VALUES(1, obj_city(2000, 'guangdong'));
INSERT INTO city VALUES(2, obj_city(2500, 'guangdong'));

-- Update data
UPDATE city SET info = obj_city(2500, 'shanghai') WHERE id=2;
COMMIT;

-- Query data using table alias.column name.attribute name
SELECT id, c.info.population, c.info.province FROM city c;
          ID INFO.POPULATION INFO.PROVINCE         
------------ --------------- --------------------- 
           1            2000 guangdong            
           2            2500 shanghai   
```

## Varray Type

Varray is a collection of ordered elements in an array.

YashanDB supports storing array-structured data represented by Varray as column fields in physical tables. However, when defining Varray type column fields or fields containing Varray type attributes, the elements of Varray type arrays cannot be of LOB, BFILE, JSON, or XMLTYPE types.

The declaration method for Varray types is the same as for ordinary scalar data types, and data can be inserted or updated using the object initialization method.

Varray type data can be queried in the following ways:

- Using array functions, such as [ARRAY_TO_STRING](../Built-in Functions/ARRAY_TO_STRING).
- Table function table(), for detailed syntax please refer to [table_collection_expression](../SQL Statements/SELECT.md#tablecollectionexpression) in SELECT statement.

***Example*** for Heap tables

```sql
DROP TABLE IF EXISTS city;
-- Create a Varray UDT
CREATE OR REPLACE TYPE arr_type AS VARRAY(5) OF CHAR(10);
/

-- Create the city table and insert data
CREATE TABLE city (id INT, info arr_type);
INSERT INTO city VALUES(1, arr_type('2000', 'guangdong'));
INSERT INTO city VALUES(2, arr_type('2500', 'guangdong'));

-- Update data
UPDATE city SET info = arr_type('2500', 'shanghai') WHERE id=2;
COMMIT;

-- Access data through an array function
SELECT id, ARRAY_TO_STRING(info,',') infos FROM city;
          ID INFOS                                                            
------------ ---------------------------------------------------------------- 
           1 2000,guangdong                                                  
           2 2500,shanghai 
           
-- Access data through the table() method
SELECT /*+ leading(c,arr) */ id, arr.*
FROM city c, table(c.info) arr;
          ID COLUMN_VALUE  
------------ ------------- 
           1 2000         
           1 guangdong    
           2 2500         
           2 shanghai 
```

<span id="nestedtable" name="nestedtable"></span>

## Nested Table Type

Nested Table is a collection containing multiple rows, similar to a table.

YashanDB supports storing table-structured data represented by a Nested Table as column fields in physical tables. However, the data of the Nested Table is not stored directly in the declared table; instead, a new nested table must be created to store it. Therefore, when defining a column field of Nested Table type, or a column field containing Nested Table type attributes, the information for storing the Nested Table data must also be specified for creation.

The newly created nested table is associated with the main table where the column field resides and cannot be separately subject to DDL/DML operations. It will be dropped/truncated along with the main table (or the main table partition) when dropped/truncated.

Information related to nested tables can be queried through DBA_NESTED_TABLES/USER_NESTED_TABLES/ALL_NESTED_TABLES.

The declaration method for Nested Table types can be found in the nested_table_clause description in [CREATE TABLE](../SQL Statements/CREATE TABLE).

Nested Table data can be inserted or updated using the object initialization method.

Queries for Nested Table data must use the table() method, for syntax details see the table_collection_expression description in the SELECT statement.

***Example*** for Heap tables

```sql
DROP TABLE IF EXISTS city;
-- Create a Nested Table UDT
CREATE OR REPLACE TYPE user_table_type IS TABLE OF CHAR(10);
/

-- Create the main table containing a nested table
CREATE TABLE city (id INT, info user_table_type) NESTED TABLE info STORE AS nt_city;

-- Insert data
INSERT INTO city VALUES (1, user_table_type('2000','2500'));
INSERT INTO city VALUES (2, user_table_type('guangdong','guangdong'));

-- Modify data
UPDATE city SET info = user_table_type('guangdong','shanghai')
WHERE id = 2;
COMMIT;

-- Query data
SELECT /*+ leading(c) */ id, tab.*
FROM city c, TABLE(c.info) tab;
          ID COLUMN_VALUE  
------------ ------------- 
           1 2000         
           1 2500         
           2 guangdong    
           2 shanghai 
           
DROP TABLE IF EXISTS provs;
-- Create an Object UDT containing Nested Table attributes
CREATE OR REPLACE TYPE obj_type_nested IS OBJECT(name CHAR(10), citys user_table_type);
/
-- Create a table containing Object UDT fields
CREATE TABLE provs(id INT, province obj_type_nested)
NESTED TABLE province.citys STORE AS nt_province;

-- Insert data
INSERT INTO provs VALUES(1,
                       obj_type_nested('guangdong', user_table_type('shenzhen','guangzhou')));
                       
-- Query data
SELECT /*+ leading(p) */ p.id, p.province.name, c2.*
FROM provs p, 
TABLE(p.province.citys) c2;
          ID PROVINCE.NAME COLUMN_VALUE  
------------ ------------- ------------- 
           1 guangdong     shenzhen     
           1 guangdong     guangzhou   
```
