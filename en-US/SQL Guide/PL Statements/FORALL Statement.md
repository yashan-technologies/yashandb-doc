FORALL Statement is a bulk execution statement.

Statement Definition
-------

**forall::=**

```ebnf+diagram
normal_clause::= lower_bound..upper_bound
indices_of_clause::= INDICES OF collection [ BETWEEN lower_bound AND upper_bound ]
values_of_clause::= VALUES OF collection
forall::= FORALL index IN (normal_clause | indices_of_clause | values_of_clause) [SAVE EXCEPTIONS] dml_statement
```

There are three optional clauses after the IN keyword. When choosing the normal clause, the index can be directly defined with two values as the lower and upper bounds. The lower_bound is the index lower limit, and the upper_bound is the index high value. index, lower_bound, and upper_bound are all of INTEGER type, with a range that matches the INTEGER domain.

The INDICES_OF clause uses the index of collection types (VARRAY and NESTED-TABLE) variables as the index value in the loop, with lower_bound and upper_bound serving as the index limits.

The VALUES_OF clause uses the member values of collection types (VARRAY and NESTED-TABLE) variables as the index value in the loop.

SAVE EXCEPTIONS is optional; it enables the continuation of the FORALL statement even in the event of failure in certain DML statements, saving error information in a cursor specific to FORALL. After the entire FORALL statement is executed, a unified error code (YAS-06817) is thrown.

The body of a FORALL Statement must be a single DML statement, such as INSERT, UPDATE, or DELETE.

**index::=**

index is a locally declared variable in the FORALL Statement and can only be used within the FORALL statement; it becomes invalid after exiting the FORALL statement.

**lower\_bound|upper\_bound::=**

lower_bound and upper_bound can be numeric literals, numeric variables, or numeric expressions. 
In the normal clause, they increment by 1. In the INDICES_OF clause, it jumps directly to the next existing index value of the array member:

*   When lower_bound < upper_bound, the loop assigns values to index starting from lower_bound until upper_bound, thus ending the loop.
*   When lower_bound = upper_bound, lower_bound is assigned to index, and the loop executes only once.
*   When lower_bound > upper_bound, the FORALL statement ends immediately.

**collection::=**

Collection types (VARRAY or NESTED-TABLE) variables or bind parameters are used as access objects for INDICES_OF and VALUES_OF.

- When used as an access object for INDICES OF, the array cannot use arrays indexed by string.
- When used as an access object for VALUES OF, only arrays with member types of int are allowed.

Cursor Definition
-------

**SQL%BULK_EXCEPTIONS**

In the exception handling process for YAS-06817, information about each failed DML statement can be retrieved from the implicit cursor attribute SQL%BULK_EXCEPTIONS.
- SQL%BULK_EXCEPTIONS.COUNT: Records the number of failed DML statements.
- SQL%BULK_EXCEPTIONS(i).ERROR_INDEX: Records the position of the ith failed DML statement among all DML statements.
- SQL%BULK_EXCEPTIONS(i).ERROR_CODE: Records the error code of the ith failed DML statement.

**SQL%BULK_ROWCOUNT**

After the completion of FORALL, the number of affected rows for each DML can be obtained from the implicit cursor attribute SQL%BULK_ROWCOUNT.

SQL%BULK_ROWCOUNT(i): Records the number of rows affected after executing the ith DML statement.

***Example***

```plsql

DECLARE 
    TYPE numListType IS TABLE OF CHAR(2);
	numList numListType := numListType('01','02','03');
BEGIN
    forall i IN 1..6/1.9 save exceptions
        UPDATE area SET DHQ  = DHQ  || '3out_of_size' WHERE area_no  = numList(i);
    EXCEPTION
        WHEN others THEN 
            DBMS_OUTPUT.PUT_LINE('SQL%BULK_EXCEPTIONS.COUNT : ' || SQL%BULK_EXCEPTIONS.COUNT );

            FOR i IN 1..SQL%BULK_EXCEPTIONS.COUNT LOOP
            DBMS_OUTPUT.PUT_LINE('SQL%BULK_EXCEPTIONS('||i||').ERROR_CODE : ' || SQL%BULK_EXCEPTIONS(i).ERROR_CODE );
            DBMS_OUTPUT.PUT_LINE('SQL%BULK_EXCEPTIONS('||i||').ERROR_INDEX :' || SQL%BULK_EXCEPTIONS(i).ERROR_INDEX );
            END LOOP;
            
            FOR i IN 1..3 LOOP   ----4 out
            DBMS_OUTPUT.PUT_LINE('SQL%BULK_ROWCOUNT('||i||') : ' || SQL%BULK_ROWCOUNT(i) );
            END LOOP; 
END;
/

--result
SQL%BULK_EXCEPTIONS.COUNT : 1
SQL%BULK_EXCEPTIONS(1).ERROR_CODE : 4008
SQL%BULK_EXCEPTIONS(1).ERROR_INDEX :3
SQL%BULK_ROWCOUNT(1) : 1
SQL%BULK_ROWCOUNT(2) : 1
SQL%BULK_ROWCOUNT(3) : 0

-- INDICES OF
DECLARE 
    TYPE numListType IS TABLE OF INT INDEX BY INT;
	numList numListType := numListType();
BEGIN
    numList(-5) := -5;
    numList(-1.5) := -1.5;
    numList(25) := 25;
    numList(10) := 10;
    forall i IN indices OF numList BETWEEN -3 AND 15
        INSERT INTO area VALUES(numList(i), NULL, 'area' || numList(i));
END;
/

SELECT area_no,area_name,DHQ FROM area;

AREA_NO AREA_NAME                                                     DHQ
------- ------------------------------------------------------------- ---------------------
-2                                                                    area-2
10                                                                    area10

-- VALUES OF
DECLARE 
    TYPE charListType IS TABLE OF CHAR(2);
	charList charListType := charListType('A', 'B', 'C', 'D');
	
	TYPE numListType IS VARRAY(10) OF INT;
	indexList numListType := numListType(1, 3, 4);
BEGIN
    forall i IN VALUES OF indexList
        INSERT INTO area VALUES(charList(i), NULL, 'area' || charList(i));
END;
/

SELECT area_no,area_name,DHQ FROM area;

AREA_NO AREA_NAME                                                     DHQ
------- ------------------------------------------------------------- ---------------------
A                                                                     areaA
C                                                                     areaC
D                                                                     areaD

```
