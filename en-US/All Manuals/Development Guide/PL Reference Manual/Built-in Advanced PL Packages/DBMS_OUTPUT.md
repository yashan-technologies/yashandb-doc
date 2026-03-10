The DBMS_OUTPUT package provides a set of built-in stored procedures mainly used for debugging PL/SQL by outputting the values of variables, expressions, etc.

> **Note**:
>
> - Executing the command `set serveroutput on` calls `DBMS_OUTPUT.ENABLE(NULL)` and opens the print switch. When the switch is on, executing a SQL statement or calling an anonymous PL/SQL block will trigger `DBMS_OUTPUT.GET_LINES()` to retrieve the remaining information in the cache and print it.
> - Executing the command `set serveroutput off` calls `DBMS_OUTPUT.DISABLE()` and closes the print switch. By default, both the cache and the print switch are off.
> - Only the client *yasql* supports the set serveroutput option.
> - The size of each line in the cache cannot exceed 65534 bytes.
> - In ISC Distributed Cluster Deployment, all users on the CN node can call this advanced package, but on DN/MN nodes, only the sys user can call this advanced package.
> - In HA environments, this advanced package can only be called on the primary database / master node.

## CHARARR

```plsql
TYPE CHARARR IS TABLE OF VARCHAR2(65534) INDEX BY BINARY_INTEGER;
```

CHARARR is a data type defined within the DBMS_OUTPUT advanced package, used in conjunction with `GET_LINES` to receive the information input into cache from `PUT` and `PUT_LINE`. For specific usage, refer to the `GET_LINES` example.

## ENABLE

```plsql
DBMS_OUTPUT.ENABLE(
    buffer_size IN INTEGER DEFAULT 20000);
```

The ENABLE stored procedure enables the cache, allowing it to be operated by the `PUT`, `PUT_LINE`, `NEW_LINE`, `GET_LINE`, and `GET_LINES` stored procedures.

|Parameter |Description |
| :---------- | :----------------------------------------------------------- |
| buffer_size | The size of the enabled cache (in bytes), the range is [2000,1000000]; if out of range, the value is adjusted to the nearest boundary, with a default value of 20000. Setting it to NULL is equivalent to setting it to 4000000. |

## DISABLE

```plsql
DBMS_OUTPUT.DISABLE();
```

The DISABLE stored procedure disables the cache, preventing `PUT`, `PUT_LINE`, `NEW_LINE`, `GET_LINE`, and `GET_LINES` from operating on the cache, and clears all remaining information in the cache. This stored procedure does not require parameters.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
BEGIN
    DBMS_OUTPUT.ENABLE(3000);
    DBMS_OUTPUT.PUT('yashanDB');   
    DBMS_OUTPUT.PUT_LINE('hello world!');
    DBMS_OUTPUT.DISABLE();
END;        
/

--result
```

## PUT

```plsql
DBMS_OUTPUT.PUT(
    string IN VARCHAR2);
```

The PUT stored procedure inputs a segment of text without a newline into the cache. It can be called multiple times to concatenate multiple segments into a single line. After using PUT to complete a line, a NEW_LINE needs to be called to create a newline. `GET_LINE` and `GET_LINES` will not return lines without a newline character.

## NEW\_LINE

```plsql
DBMS_OUTPUT.NEW_LINE();
```

The NEW_LINE stored procedure inputs a newline character into the cache. `GET_LINE` and `GET_LINES` will return lines separated by newline characters. Each call to `PUT_LINE` or `NEW_LINE` generates a line that can be returned by `GET_LINE` or `GET_LINES`.

## PUT\_LINE

```plsql
DBMS_OUTPUT.PUT_LINE(
    line IN VARCHAR2);
```

The PUT_LINE stored procedure inputs a line into the cache. The content input by PUT_LINE ends with a newline character, so there is no need to call NEW_LINE to create a newline.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
SET serveroutput ON;
BEGIN
    DBMS_OUTPUT.ENABLE(3000);
    DBMS_OUTPUT.PUT('yashanDB');
    DBMS_OUTPUT.PUT_LINE('hello world!');
    DBMS_OUTPUT.PUT('coming'); 
END;
/

--result
yashanDBhello world!
```

## GET\_LINE

```plsql
DBMS_OUTPUT.GET_LINE(
    line OUT VARCHAR2,
    status OUT INTEGER);
```

The GET_LINE stored procedure retrieves a line of information ending with a newline character from the cache and discards the newline character. When `GET_LINE` is triggered, if `PUT`, `PUT_LINE`, or `NEW_LINE` is called for the first time, the cache will be cleared before writing.

|Parameter |Description |
| :----- | :----------------------------------------------------------- |
| line      | The line of information returned from the cache, excluding the final newline character. |
| status    | The status of the call. If successful, the status value is set to 0. If there are no lines left in the cache, the status value is set to 1. |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
SET serveroutput ON;

DECLARE
    line VARCHAR2(100);
    status INT;
BEGIN
    DBMS_OUTPUT.ENABLE(3000);
    DBMS_OUTPUT.PUT_LINE('Workshop');
    DBMS_OUTPUT.PUT_LINE('Workshop Man');
    DBMS_OUTPUT.GET_LINE(line,status);
    INSERT INTO department VALUES(status,line);
END;
/

SELECT deparment_no,department_name FROM department ORDER BY deparment_no;
DELETE FROM department WHERE deparment_no='0';

--result
Workshop Man

DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
0            Workshop
000          Public Dep
002          Finance Dep
008          Purchasing Dep
010          Sales Dep

```

## GET\_LINES

```plsql
DBMS_OUTPUT.GET_LINES(
    lines OUT CHARARR,
    numlines IN OUT INTEGER);
```

The GET_LINES stored procedure retrieves an array of lines from the cache. If the number of remaining lines in the cache is greater than or equal to the requested number of lines, the returned number will be the requested number of lines. If the remaining lines are fewer than the requested number, the returned number will be the actual remaining number of lines. When `GET_LINES` is triggered, if `PUT`, `PUT_LINE`, or `NEW_LINE` is called for the first time, the cache will be cleared before writing.

|Parameter |Description |
| :------- | :------------------------------------------- |
| lines     | An array of multiple lines returned from the cache. |
| numlines  | The number of lines to retrieve from the cache, returning the actual number retrieved. |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
DECLARE
    lines DBMS_OUTPUT.CHARARR;
    num INT;
BEGIN
    DBMS_OUTPUT.ENABLE(3000);
    DBMS_OUTPUT.PUT_LINE('Workshop');
    DBMS_OUTPUT.PUT_LINE('Workshop Man');
    num:=10;
    DBMS_OUTPUT.GET_LINES(lines,num);
    INSERT INTO department VALUES('050',lines(1));
    INSERT INTO department VALUES(num,lines(2));
END;
/
SELECT deparment_no,department_name FROM department ORDER BY deparment_no;
DELETE FROM department WHERE deparment_no IN ('050','2');

--result
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
002          Finance Dep
008          Purchasing Dep
010          Sales Dep
050          Workshop
2            Workshop Man

```

## Exception Explanation

When calling the DBMS_OUTPUT advanced package for cache writing, if the length of the string being written exceeds the limit, the following exceptions will be thrown:

|Error Code |Error Content |Explanation |
| :-------- | :-------------------------------------------------- | :--------------------------------------------- |
| YAS-30005  | line length overflow, limit of 65534 bytes per line | Writing to the current line causes overflow, with a per-line limit of 65534 bytes |
| YAS-30006  | buffer overflow, limit of %d bytes               | Writing to the cache causes overflow, with a total length limit of %d bytes |