In the OCI driver, when connecting to the database using the database driver, parameters can be passed to the SQL statement through the binding parameter functionality, which reduces the risk of SQL injection and improves the execution efficiency of the SQL statement.

Binding parameters refer to binding variables from the program to placeholders in the SQL statement, and then passing the values of these variables to execute the SQL statement. To use the binding parameter functionality, placeholders must first be defined in the SQL statement, and then variables from the program are bound to these placeholders.

|Interface Name |Interface Description |
|----------------------------------------------|------------------------------|
| [OCIBindArrayOfStruct](OCIBindArrayOfStruct) | Set the skip parameter for static array binding           |
| [OCIBindByName](OCIBindByName)               | Bind program variables to placeholders by name in the SQL statement or PL block       |
| [OCIBindByPos](OCIBindByPos)                 | Bind program variables to placeholders by position in the SQL statement or PL block   |
| [OCIDefineByPos](OCIDefineByPos)             | Associate items in the select list with types and output data cache    |
| [OCIDefineByPos2](OCIDefineByPos2)           | Extended version of OCIDefineByPos, supports larger buffer sizes |
| [OCIDescribeAny](OCIDescribeAny)             | Describe existing schema and sub-schema objects                |
| [OCIBindDynamic](OCIBindDynamic)             | Register user callback for dynamic data allocation                |
| [OCIDefineDynamic](OCIDefineDynamic)         | Set additional attributes required for OCI_DYNAMIC_FETCH mode    |