```ebnf
from_days = FROM_DAYS "(" days_expr ")".
```

The FROM_DAYS function calculates the date value corresponding to the number of days from 0000-00-00 based on the input days, returning a DATE type.

**days_expr**

Represents the number of days from 0000-00-00, of type BIGINT or other types that can be converted to BIGINT.

***Example*** for  Heap tables

```sql
select from_days(730669) res from dual;

res                               
---------------------- 
2000-07-03

```
