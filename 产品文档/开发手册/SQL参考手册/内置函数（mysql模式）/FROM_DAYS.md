
```ebnf+diagram
from_days::= FROM_DAYS "(" days_expr ")"
```

FROM_DAYS函数根据输入的天数计算从0000-00-00开始的天数对应日期值，返回类型为DATE。

**days_expr**

表示从0000-00-00开始的天数，BIGINT类型或可转换为BIGINT类型的其他类型。

示例（单机HEAP表）

```sql
SELECT FROM_DAYS(730669) FROM dual;

from_days(730669)                                
------------------------------------------------ 
2000-07-03

```