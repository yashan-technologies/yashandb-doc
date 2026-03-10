The DBMS_LOCK package provides a set of built-in stored procedures/functions to implement lock-related functionality.

## SLEEP

```plsql
DBMS_LOCK.SLEEP(
	second IN NUMBER);
```

SLEEP is a stored procedure that pauses for a specified amount of time based on the input parameter value, measured in seconds.

When this program is executed, the system will sleep for the number of seconds specified by the parameter. If you need to stop the sleep before the time is up, you must force quit by executing CTRL+C, Shutdown, or other commands in the terminal.

|Parameter |Description |
| :----- | :----------------------------------------------------------- |
| second    | Specifies the sleep time, cannot be empty, value range must be [0,21474836.47], supports implicit type conversion for input parameters. |

***Example***

```plsql
-- Numeric type
exec DBMS_LOCK.SLEEP (10);

-- String type
exec DBMS_LOCK.SLEEP ('10');

-- BIT type
DECLARE
vsql VARCHAR(256);
vbit BIT(8);
BEGIN
vsql := 'exec DBMS_LOCK.SLEEP (?)';
vbit := 5;
EXECUTE IMMEDIATE vsql USING vbit;
END;
/

-- BOOLEAN type
DECLARE
vsql VARCHAR(256);
vbool BOOLEAN;
BEGIN
vsql := 'exec DBMS_LOCK.SLEEP (?)';
vbool := 'true';
EXECUTE IMMEDIATE vsql USING vbool;
END;
/
```
