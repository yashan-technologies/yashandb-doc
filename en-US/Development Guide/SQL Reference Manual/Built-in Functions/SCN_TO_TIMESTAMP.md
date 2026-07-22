```ebnf
scn_to_timestamp = SCN_TO_TIMESTAMP "(" expr ")".
```

The SCN_TO_TIMESTAMP function converts the SCN number represented by [expr](../General SQL Syntax/expr) into SCN data.

The value of expr must be of BIGINT type, or other integer types that can be converted to BIGINT (specifically TINYINT, SMALLINT, INT, BIGINT), otherwise the return type is not supported.

This function does not support passing null as a parameter.

***Example***

```sql
SELECT SCN_TO_TIMESTAMP(247677771776000000) timestamp1, SCN_TO_TIMESTAMP(555+3) timestamp2 FROM DUAL;
TIMESTAMP1                         TIMESTAMP2
---------------------------------- ------------------------------
2021-12-01 04:43:26.000000         2020-01-01 08:00:00.000000
```
