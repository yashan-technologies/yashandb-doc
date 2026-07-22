```ebnf
yasdecode = YASDECODE "(" expr "," value "," result {"," value "," result} ["," default] ")".
```

The YASDECODE expression is equivalent to a conditional expression, similar to a series of nested IF-THEN-ELSE statements. This function is a synonym for the [DECODE](DECODE) function.

When the value of [expr](../General SQL Syntax/expr) matches one of the specified values that follow, it returns the result immediately following that value. If it does not match any of the listed values, it returns the default. When expr matches multiple values successfully, it returns the result corresponding to the first value.

This function is commonly used for determining ranking levels.

**expr, value**

Both are [general expressions](../General SQL Syntax/expr).

When the data types of expr and each value are inconsistent:

- If both numeric and boolean types exist, the function returns invalid datatype.

- In other scenarios, the function first unifies the data types before comparison. Refer to [comparison operators](../Operators/Comparison Operators) for unification rules.

NULLs also participate in comparisons, and when both expr and value are NULL, a successful match is executed.

***Example***

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
