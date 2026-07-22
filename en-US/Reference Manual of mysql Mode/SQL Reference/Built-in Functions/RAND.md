```ebnf
rand = RAND "(" [expr] ")".
```

The RAND function returns a random number between 0 and 1, with a return type of DOUBLE.

**expr**

A [general expression](../General SQL Syntax/expr) used to specify the seed, which is an optional parameter.

If no seed is specified, the function returns a completely random number. If a seed is specified, the function returns a repeatable random number sequence.

***Example*** for Standalone Deployment Heap tables

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
