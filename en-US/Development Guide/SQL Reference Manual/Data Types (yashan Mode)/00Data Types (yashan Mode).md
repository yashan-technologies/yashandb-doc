All data in a database belongs to a specific data type (Data Type), which identifies the storage-related attributes of the data and the restrictions on operations for that type of data.

When a user inputs data, the system identifies its data type according to certain rules, which can be referenced in the description of [literal](../Basic SQL Elements/Literals).

When creating a table object, the declaration of the column fields must specify a data type. Based on this data type, all data in that column must meet its storage attribute requirements, such as length and precision, in order to be successfully inserted into the table. YashanDB also provides implicit data type conversion functionality. When the user input data does not align with the defined data type of the field, the system performs data type conversion first, for example, converting the character '100' to the numeric 100. In this case, the inserted data can meet the storage requirements, leading to a successful operation. For data types that cannot be converted, such as failing to convert the character 'aaa' to numeric data, the insertion will fail.

When data is used for expression calculations, its type also imposes restrictions on the calculation operations. Only data types that meet the restriction conditions can successfully execute calculations. For example, mathematical calculations require all parameters to be numeric, while date calculations require all parameters to be of date-time type. However, if the user explicitly specifies data conversion, or if the conversion can be successfully executed according to YashanDB's implicit conversion rules, calculations across different data types can be successfully performed.

In other scenarios, such as when data is used as parameters for functions and its type does not match the parameter types defined by the function, the system will also perform implicit data type conversion to ensure maximum compatibility and computational capability.

Based on commonalities in the attributes and operational restrictions of certain data types, YashanDB's data types can be classified into the following major categories:

- [Numeric Types](Numeric Types): Data related to numbers (Numeric) falls into this category, including TINYINT, SMALLINT, INT/INTEGER, BIGINT, NUMBER, FLOAT, DOUBLE, BIT, etc.
- [Character Types](Character Types): Data related to text characters (Character) falls into this category, including CHAR, VARCHAR, etc.
- [Date-Time Types](Date-Time Types): Data related to date and time (Datetime) falls into this category, including DATE, TIME, TIMESTAMP, TIMESTAMP WITH LOCAL TIMEZONE, TIMESTAMP WITH TIME ZONE, INTERVAL YEAR TO MONTH, INTERVAL DAY TO SECOND, etc.
- [Boolean Type](Boolean Type): Data related to Boolean logic (Boolean) falls into this category, specifically the Boolean data type.
- [Large Object Types](Large Object Types): Data related to large binary and large text objects (Large Object) falls into this category, including CLOB, BLOB, etc.
- [ROWID/UROWID](ROWID and UROWID Types): Data used to represent the physical address of a row record falls into this category.
- [RAW Type](RAW Type): A variable-length data type similar to VARCHAR, often used to store binary objects.
- [JSON Type](JSON Type): A variable-length data type related to JSON, often used to store JSON formatted binary objects.
- [ST_GEOMETRY Type](ST_GEOMETRY Type): Spatial data types related to GIS, including POINT, LINESTRING, POLYGON, MULTIPOINT, MULTILINESTRING, MULTIPOLYGON, etc.
- [XMLTYPE Type](XMLTYPE Type): A database built-in UDT related to XML.
- [BOX2D Type](BOX2D Type): A spatial data type related to ST_GEOMETRY, used to represent the two-dimensional bounding box of ST_GEOMETRY.
- [BFILE Type](BFILE Type): A binary large object type related to BFILE, used to handle binary files stored outside the database.

This chapter will elaborate on all built-in data types in YashanDB according to the major categories listed above, as well as the conversion rules between them.

> **Note**: 
>
> The following data types only apply to HEAP tables:
> - NCLOB
> - NCHAR
> - NVARCHAR
> - FLOAT (when USE_NATIVE_TYPE is FALSE)
> - XMLTYPE
> - BOX2D
> - ST_GEOMETRY
> - TIMESTAMP WITH LOCAL TIME ZONE
> - TIMESTAMP WITH TIME ZONE
> - [User-Defined Types](User-Defined Types)
> - BFILE
