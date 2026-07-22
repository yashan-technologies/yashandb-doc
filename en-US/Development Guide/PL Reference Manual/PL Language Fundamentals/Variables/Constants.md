The syntax for declaring a variable as a constant is:

```plsql
variable_name CONSTANT datatype [[NOT] NULL] (:=|DEFAULT) expression;
```

Specifying CONSTANT indicates that what is being declared is a constant, which must be initialized at the same time and cannot be assigned a value within the procedure body.

The expression can be NULL, a number, a string, or another declared variable (initialization of the variable is not required, and it is equivalent to NULL in this case).

If the NOT NULL attribute is specified when declaring a constant, it is not allowed to initialize it with NULL.

When defining a RECORD, its members cannot be defined as constants.

***Example***

```plsql
DECLARE
a INT;
a1 INT := 3;
b CONSTANT INT := a;
c CONSTANT INT := NULL;
d CONSTANT INT NOT NULL DEFAULT a1;
BEGIN
a := 4;
a1 := 4;
DBMS_OUTPUT.PUT_LINE('b is '||b);
DBMS_OUTPUT.PUT_LINE('c is '||c);
DBMS_OUTPUT.PUT_LINE('d is '||d);
END;
/

--result
b is 
c is 
d is 3
```
