```ebnf+diagram
lsfa_listagg::= LSFA_LISTAGG "(" string ")"
```

The LSFA_LISTAGG function performs concatenation on multiple rows of data, separated by the delimiter `,`, and returns a single row of VARCHAR type string.

> **Note**:
>
> To use this function, ensure that the listagg plugin is installed (you can confirm this by checking if the listagg folder exists under the $YASDB_HOME/third path).
>
> If you still cannot use this function after the plugin is installed correctly, please add a line `PACKAGE3 = {library = yspi_listagg, name = /, schema = public}` in the $YASDB_HOME/plug-in/package/package.ini and restart the database for it to take effect.

This function follows the rules below:

- This function is only applicable for Standalone Deployment.

- This function does not support vectorization calculation.

- The output length of this function is at most 65534; exceeding this will cause an error.

- This function is an aggregate function but does not support DISTINCT and ALL.

**string**

The string can be:

- A generic expression [expr](../General SQL Syntax/expr)
- A single-column subquery that returns a single row

The value of string must be of character type or another type that can be converted to character type.

In single-row calculations, when the value of string is NULL, the function returns NULL.

In multi-row calculations, the function will ignore rows where the string value is null. If all rows are null, the result of the calculation will be NULL. When there are multiple concatenated rows, the results from multiple rows will be separated by the delimiter `,`.

***Example*** for Standalone Deployment Heap tables

```sql
-- Create exprs_listagg table and insert data
CREATE TABLE exprs_listagg (id INT,name VARCHAR(50),money FLOAT);
INSERT INTO exprs_listagg 
VALUES  (1,'Dong',10000),(2,'Ming',46450),
        (3,'Hong',46450 ),(4,'Dong',14465),
        (5,'Ming',46450),(6,'Dong',46450);

-- When GROUP BY is not specified, concatenate all rows and get a single row result
SELECT LSFA_LISTAGG(MONEY) AS money FROM exprs_listagg;
MONEY
---------------------------------------------------------------- 
4.645E+004,4.645E+004,1.4465E+004,4.645E+004,4.645E+004,1.0E+004

-- After grouping by, concatenate the multi-row data of each group into a single row, obtaining multi-row results by group
SELECT NAME,LSFA_LISTAGG(MONEY) AS money FROM exprs_listagg GROUP BY name;
NAME                                                  MONEY                                                            
----------------------------------------------------- ---------------------------------------------------------------- 
Dong                                                  4.645E+004,1.4465E+004,1.0E+004                                 
Ming                                                  4.645E+004,4.645E+004                                           
Hong                                                  4.645E+004
```
