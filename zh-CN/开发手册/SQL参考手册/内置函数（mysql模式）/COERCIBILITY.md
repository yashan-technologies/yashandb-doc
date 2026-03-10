```ebnf+diagram
coercibility::= COERCIBILITY "(" expr ")"
```
COERCIBILITY函数用于获取expr参与运算时的字符序优先级。

表达式在进行运算时，默认按字符序作为排序规则。但字符序不同的两个变量进行运算时，只能选取其中一种字符序作为排序规则，排序规则选取结果通常取决于字符序的优先级，若两个字符序不同但优先级相同的变量进行运算，二者中存在_bin二进制则选取_bin作为排序规则，不存在_bin则报错。

字符序的优先级如下表所示，Coercibility值越小，优先级越高。

|  表达式类型| coercibility值| 说明|
|----------|---------------|-------------------|
| 显式指定的字符序 | 0             | 显式COLLATE指定。      |
| 计算字符序    | 1             | 不同字符序字符串的连接。      |
| 隐式指定的字符序 | 2             | 列值或局部变量。          |
| 系统变量     | 3             | 系统内部使用的变量。        |
| 可降级值     | 4             | 文字字符串。            |
| 数字型值     | 5             | 数值或时间值。           |
| 可忽略的值    | 6             | NULL或结果为NULL的表达式。 |

示例（单机HEAP表）

```sql
-- 以下语句中，系统将使用显式指定的utf8mb4_general_ci作为运算时的排序规则
SELECT COERCIBILITY('aaa' collate utf8mb4_general_ci) FROM dual;

coercibility('aaa' collate utf8mb4_general_ci)
----------------------------------------------
                                             0
```
