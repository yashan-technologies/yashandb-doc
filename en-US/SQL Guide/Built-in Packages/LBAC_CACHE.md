The LBAC_CACHE package provides a set of built-in subprograms for converting numeric values into specific content composed of LBAC mandatory control options.

## OPTION\_STRING

```plsql
LBAC_CACHE.OPTION_STRING (
    options    INTERGE
);
```
This function is used to view the content corresponding to the LBAC mandatory control option values for a given numeric input.

|Parameter |Description |
| :--- | :---- |
| options | Option value |

The option value is the result of the bitwise OR operation on the mandatory control option values. The values of the mandatory control options can be found in the [SA_SYSDBA](SA_SYSDBA) mandatory control option description.

***Example*** for Standalone Deployment

```sql
SELECT LBAC_CACHE.OPTION_STRING(2) AS OPTIONS FROM dual;

OPTIONS                                                          
---------------------------------------------------------------- 
INSERT_CONTROL                                                  

```
