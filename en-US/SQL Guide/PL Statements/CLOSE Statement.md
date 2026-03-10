The CLOSE statement is used to close a cursor. The method for closing explicit cursors and dynamic cursors is the same, and the format is: 

_CLOSE cursor_name;_

Where cursor_name is the name of the explicit cursor or dynamic cursor, and cursor_name cannot be a bind parameter.

Closing a cursor means closing the result set that the cursor points to. If the cursor is not open, or if it is closed multiple times, the system will return an invalid cursor error.

***Example***

```plsql
DECLARE
  CURSOR cur IS SELECT 1 FROM dual;
BEGIN
  OPEN cur;
  CLOSE cur;
  IF cur%isopen THEN
    CLOSE cur;
  END IF;
END;
/
```
