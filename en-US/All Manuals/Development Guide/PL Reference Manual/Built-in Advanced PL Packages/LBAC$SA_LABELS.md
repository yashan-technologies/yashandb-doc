The LBAC$SA_LABELS package provides a set of built-in sub-functions to convert string content composed of values for policies, component types, levels, and ranges into string content composed of names for levels and ranges.

This built-in advanced package is only applicable for Standalone Deployment.

## FROM\_LABEL

```plsql
LBAC$SA_LABELS.FROM_LABEL (
    ILABEL    VARCHAR(4000)
);
```
This function is used to view the string content corresponding to the level and range names under the policy, which is composed of values for policy, component type, level, and range.

|Parameter |Description |
| :--- | :---- |
| ILABEL| String content composed of values for the policy, component type, level, and range |

The format of ILABEL is as follows:

```txt
policyId + componentType + level + '.' + [%range%...%] + '.'
```

|Parameter |Description |
| :--- | :---- |
| policyId | The numeric string value of the corresponding policy, 20 bytes in length; if less than 20 bytes, it needs to be padded with '0' at the front |
| componentType | "10" indicates it contains only level, "11" indicates it contains level and separator |
| level | The numeric string value corresponding to the level, 4 bytes in length; if less than 4 bytes, it needs to be padded with '0' at the front |
| range | The numeric string value corresponding to the interval, 4 bytes in length; if less than 4 bytes, it needs to be padded with '0' at the front |

***Example*** for Standalone Deployment

```sql
SELECT LBAC$SA_LABELS.FROM_LABEL(ILABEL) AS LABEL FROM SYS.YLS$LAB;

LABEL                                                            
---------------------------------------------------------------- 
GENERAL:MNG                                                     

```
