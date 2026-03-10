The YashanDB JDBC driver supports the following UDT types for queries and parameter binding:

- Object
- Varray
- Nested Table

When querying and binding parameters, the mapping between server types and Java types is as follows:

|Server Type |Object |Varray |Nested Table |
|-----------------|------------------|-----------------|------------------|
| Java Type for Querying and Parameter Binding | java.sql.Struct  | java.sql.Array  | java.sql.Array   |

Additionally, JDBC supports mapping user-defined Java types to server Object types, see [SQLData Custom Class and UDT Type Mapping](java.sql.SQLData).

>**Note**:
> 
> UDT related functionality is not applicable to ISC Distributed Cluster Deployment.