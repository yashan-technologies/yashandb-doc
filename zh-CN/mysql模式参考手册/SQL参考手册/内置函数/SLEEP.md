```ebnf
sleep = SLEEP "(" expr ")".
```

SLEEP函数用于暂停参数指定的时长，暂停间隔为[expr](../通用SQL语法/expr.md)秒。

函数正常调用后返回0，若在暂停过程中被中断则返回1或错误码。若被中断时存在以下情况，函数将返回错误码：

- WHERE子句中存在SLEEP函数。

- JOIN子句中存在SLEEP函数。

- HAVING子句中存在SLEEP函数。

**expr**

通用表达式，其值须为数值型或可转换为数值型的其他类型。

示例（HEAP表）

```sql
select sleep(1.5) res;

                  res
---------------------
                    0

1 row fetched.

select sleep('true');

[1:14]YAS-00008 type convert error : not a valid number
```
