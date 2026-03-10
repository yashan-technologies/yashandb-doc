```ebnf+diagram
yasdecode::= YASDECODE "(" expr "," (value "," result) {"," (value "," result)} ["," default] ")"
```

YASDECODE表达式相当于条件表达式，与一系列嵌套的IF-THEN-ELSE语句类似。此函数是[DECODE](DECODE)函数的同义词。

当[expr](../通用SQL语法/expr)的值等于其后列出中的某一个指定value，则返回紧接着该value后的result；若不等于所列出的所有value，则返回default；当expr与多个value匹配成功时，返回第一个value对应的result。

本函数通常用于做排名等级判断。

**expr、value**

均为[通用表达式](../通用SQL语法/expr)。

当expr与各value的数据类型不一致时：

- 同时存在数值型和布尔型时，函数返回invalid datatype。

- 其它场景中，函数先将数据类型进行统一再比较，统一规则请查阅[比较运算符](../运算符/比较运算符)。

空值同样参与比较，当expr与value均为NULL时，执行匹配成功处理。



示例

```sql
SELECT employee_name Name,
YASDECODE(sex,'1','Male','2','Female','Unknown') Sex
FROM employees;
NAME          SEX      
------------- ---------
Mask          Male    
John          Male    
Anna          Unknown 
Jack          Male    
Jim           Male    
 
SELECT YASDECODE('',1,1,2) res1,
YASDECODE(1,1,1,'1',2,3) res2,
YASDECODE(1,'',1,'1',2,3) res3,
YASDECODE('','',1,3) res4
FROM DUAL;
        RES1         RES2         RES3         RES4
------------ ------------ ------------ ------------
           2            1            2            1
```
