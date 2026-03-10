LBAC_CACHE包提供了一组内置子程序，用于将数值转为LBAC强制控制选项组成的具体内容。

## OPTION\_STRING

```plsql
LBAC_CACHE.OPTION_STRING (
    options    INTERGE
);
```
此函数用于查看数值对应的LBAC强制控制选项内容。

|  参数| 描述|
| :--- | :---- |
| options| 选项值 |

选项值为强制控制选项值进行或运算的结果值，强制控制选项的值见[SA_SYSDBA](./SA_SYSDBA)强制控制选项说明。

示例（单机部署）

```sql
SELECT LBAC_CACHE.OPTION_STRING(2) AS OPTIONS FROM dual;

OPTIONS                                                          
---------------------------------------------------------------- 
INSERT_CONTROL                                                  

```
