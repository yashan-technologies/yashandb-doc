```ebnf+diagram
treat::= TREAT "(" expr AS [schema "."] type ")" 
```

The TREAT function is used to modify the type defined by [expr](../General SQL Syntax/expr).

The database user executing this function must have the EXECUTE privilege for the corresponding type.

- Both expr and type must be of the OBJECT type of [User UDT](../Data Types (yashan Mode)/User-Defined Types).
- If expr and type are of the same type, the function returns the value of expr.
- Type must be a supertype or subtype of the type defined by expr; otherwise, TREAT will result in a compilation error.
- If the value of expr is not of type or a subtype of type, TREAT will result in a runtime error, returning NULL in SQL and raising an exception in PL.

***Example***

```sql
CREATE OR REPLACE TYPE udt_object_sp FORCE IS OBJECT (
    branch_no CHAR(4),
    branch_name VARCHAR2(200)
) NOT FINAL;
/

CREATE OR REPLACE TYPE udt_object_sub FORCE UNDER udt_object_sp (
    area_no CHAR(2),
    address VARCHAR2(200)
) NOT FINAL;
/

DECLARE
    obj1 udt_object_sub := udt_object_sub('0101','Beijing','01','North Street');
    obj2 udt_object_sp;
BEGIN
    obj2 := TREAT (obj1 AS udt_object_sp);
    DBMS_OUTPUT.PUT_LINE(obj2.branch_name);
END;
/

DECLARE
    obj1 udt_object_sp := udt_object_sub('0101','Beijing','01','North Street');
    obj2 udt_object_sub;
BEGIN
    obj2 := TREAT (obj1 AS udt_object_sub);
    DBMS_OUTPUT.PUT_LINE(obj2.branch_name);
END;
/
```
