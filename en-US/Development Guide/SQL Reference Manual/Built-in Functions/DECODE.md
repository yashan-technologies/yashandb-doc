```ebnf
decode = DECODE "(" expr "," value "," result {"," value "," result} ["," default] ")".
```

The DECODE expression is equivalent to a conditional expression, similar to a series of nested IF-THEN-ELSE statements.

When the value of [expr](../General SQL Syntax/expr) equals one of the specified values listed afterwards, it returns the result immediately following that value; if it does not equal any of the listed values, it returns the default. When expr matches multiple values successfully, it returns the result corresponding to the first matching value.

This function is commonly used for ranking and level judgments.

**expr, value**

Both are [general expressions](../General SQL Syntax/expr).

When the data types of expr and the values are inconsistent:

- If both numeric and boolean types exist simultaneously, the function returns invalid datatype.

- In other scenarios, the function first unifies the data types before comparison; please refer to [comparison operators](../Operators/Comparison Operators) for unification rules.

Null values also participate in comparisons; when both expr and value are NULL, a successful match is executed.

***Example***

```sql
SELECT employee_name Name,
DECODE(sex,'1','Male','2','Female','Unknown') Sex
FROM employees;
NAME          SEX      
------------- ---------
Mask          Male    
John          Male    
Anna          Unknown 
Jack          Male    
Jim           Male    
 
SELECT DECODE('',1,1,2) res1,
DECODE(1,1,1,'1',2,3) res2,
DECODE(1,'',1,'1',2,3) res3,
DECODE('','',1,3) res4
FROM DUAL;
        RES1         RES2         RES3         RES4
------------ ------------ ------------ ------------
           2            1            2            1
```
