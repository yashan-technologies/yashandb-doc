```ebnf+diagram
rand::= RAND "(" [expr] ")"
```

RAND函数用于返回一个0到1之间的随机数，返回值为DOUBLE类型。

**expr**

[通用表达式](../通用SQL语法/expr)，用于指定seed，可选参数。

如果不指定seed，函数返回完全随机的数字。如果指定seed，函数返回可重复的随机数序列。

示例（HEAP表）

```sql
SELECT RAND() res FROM DUAL;
res                   
--------------------- 
9.5338377006197905E-001

SELECT RAND() res FROM DUAL;
res
--------------------- 
5.7863440890299023E-001
    
SELECT RAND(321) res FROM DUAL;
res       
--------- 
4.6399847926944354E-001

SELECT RAND(321) res FROM DUAL;
res
--------- 
4.6399847926944354E-001
```
