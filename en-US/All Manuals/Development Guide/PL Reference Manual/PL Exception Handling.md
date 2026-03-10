In PL program development, an exception handling mechanism is needed to logically process various exception situations to either terminate the program or avoid errors, rather than simply throwing error messages and aborting the program.

YashanDB provides three types of exceptions:

- System predefined exceptions, which are usually errors that trigger internal program logic and are thrown by the system. They can be caught in the procedure body, for example, NO_DATA_FOUND when the query returns no results, LOGIN_DENIED when authentication fails, etc.
- User-defined exceptions, which are errors defined by developers based on business logic rules. They must be thrown and caught by the developer within the procedure body, for example, validating the legality of an ID number.
- Unpredictable other exceptions that developers cannot foresee during program execution but can catch using OTHERS for friendly prompts and other logical processing.

Regardless of the type of exception mentioned above, it must be actively caught in the procedure body before logical processing can occur. Otherwise, the system will handle it according to its error mechanism and throw an error code and message.

## System Predefined Exceptions

YashanDB has predefined the following exception names that can be recognized in the procedure body:

|Built-in Exception Name |Description |
|-----------------------| ----------------- |
| COLLECTION_IS_NULL      | The accessed dataset has not been initialized. |
| CURSOR_ALREADY_OPEN     | Cursor is opened multiple times. |
| DUP_VAL_ON_INDEX        | An index conflict occurred. |
| INVALID_CURSOR          | An invalid cursor is used. |
| INVALID_NUMBER          | An illegal number is used. |
| LOGIN_DENIED            | Login denied. |
| NO_DATA_FOUND           | No data found in the stored procedure query. |
| NO_DATA_NEEDED          | Query thread no longer needs data, pipeline function stops writing. |
| ROWTYPE_MISMATCH        | Data column type mismatch. |
| STORAGE_ERROR           | Storage error. |
| SYS_INVALID_ROWID       | An illegal ROWID is used. |
| TIMEOUT_ON_RESOURCE     | Resource access timeout. |
| TOO_MANY_ROWS           | A select query specified in PL returns multiple rows. |
| VALUE_ERROR             | A numeric exception occurred. |
| ZERO_DIVIDE             | A divide by zero exception occurred. |

System predefined exceptions are thrown by the system, and the procedure body only needs to handle them. For specific operations, refer to [EXCEPTION Statement](PL Statements/EXCEPTION Statement).

## User Defined Exceptions

User-defined exceptions must be generated and thrown by users within the procedure body. They can be divided into two methods:

**1. Directly generating exceptions**

This method directly generates exceptions using [RAISE_APPLICATION_ERROR](#RAISEAPPLICATIONERROR) at the required location, specifying the error code and message to throw without needing to specify the exception name or catch it.

**2. Exception definition -> Exception initialization -> Exception throwing -> Exception receiving**

This method requires first declaring exception variables (exception definition), followed by binding error codes to those exceptions (exception initialization), throwing exceptions and catching exceptions (exception receiving).

- Exception definition: Declare custom exception variables. For specific operations, refer to the documentation on [User-defined exceptions](PL Language Fundamentals/Variables/User-Defined Exceptions).
- [Exception initialization](#EXCEPTIONINIT): Bind error codes to exceptions. If this action is not executed, the system will automatically generate error codes.
- Exception throwing: Two different throwing actions can be executed: RAISE (throwing outwards after catching) or RAISE exception_name (throwing for the current layer to catch).
- Exception receiving: Catch the exception in the exception handler and perform logical processing. For specific operations, refer to [EXCEPTION Statement](PL Statements/EXCEPTION Statement).

## OTHERS Exception

For exceptions that developers cannot foresee, or exceptions not actively caught in the procedure body, all can be aggregated under OTHERS for handling. For specific operations, refer to [EXCEPTION Statement](PL Statements/EXCEPTION Statement).

## Error Codes

YashanDB has established a set of error codes to throw errors that users need to perceive. In this case, users can only receive error messages, and the program will be aborted. The exception handling mechanism gives users the opportunity to capture and handle specified errors, and each exception needs to correspond to the appropriate error code. Below is the framework of YashanDB's error codes:

|Category |Error Number Range |Corresponding Method |
| ---------------- | --------------- | ----------------------- |
| System Predefined Errors       | (0,20000]          | --                   |
| System Predefined Exceptions    | [20000,21000)      | System-generated      |
| User-defined Exceptions        | [21000,30000)      | EXCEPTION variable declaration |
| User-defined Exceptions        | [30000,100000)     | RAISE_APPLICATION_ERROR |
| System package predefined errors | [100000,109999]    | System-generated      |
| User-defined Exceptions        | (0,109999]         | EXCEPTION_INIT       |

For predefined errors, refer to [Error Codes](../../Reference Manual/Error Codes).

<span id="RAISEAPPLICATIONERROR" name="RAISEAPPLICATIONERROR" class="yaslink"></span>

### RAISE\_APPLICATION\_ERROR

RAISE_APPLICATION_ERROR is an exception handling function provided by the system, and its syntax is:

*RAISE_APPLICATION_ERROR(errCode, errMsg);*

**errCode**

Error number that must be specified within the range in the table above; otherwise, the system will throw YAS-04426 error when executing the program.

**errMsg**

Error message.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(vno CHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
BEGIN
IF vno>'10' THEN
RAISE_APPLICATION_ERROR(40000,'no data found for '||vno);
END IF;
EXECUTE IMMEDIATE str1 INTO no,name USING vno;      
DBMS_OUTPUT.PUT_LINE('num:'||no||'name:'||name);
END;
/

exec ya_proc('99');
--result
YAS-40000 no data found for 99
```

<span id="EXCEPTIONINIT" name="EXCEPTIONINIT" class="yaslink"></span>

### EXCEPTION\_INIT

EXCEPTION_INIT is a built-in block of the system, and its syntax format is:

*PRAGMA EXCEPTION_INIT(exception_name,errCode)*;

For the user-declared EXCEPTION variable, using EXCEPTION_INIT binds it to an error code; otherwise, the system will automatically generate an error code according to the range above.

**exception_name**

The name of the declared variable.

**errCode**

The bound error code must be specified within the range in the table above; otherwise, a compilation error will occur.

> **Caution**: 
> 
> Definition of the 2017 error code is forbidden:
>
> The 2017 error code indicates DC failure. According to YashanDB's internal processing logic, a reparse will occur and then execute upon DC failure. If this error code is manually raised without a receiving exception handler, it will cause the system to enter an endless loop of reparsing and execution.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(vno CHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
err EXCEPTION;
PRAGMA EXCEPTION_INIT(err, 45000);
BEGIN
IF vno>'10' THEN
RAISE err;
END IF;
EXECUTE IMMEDIATE str1 INTO no,name USING vno;      
DBMS_OUTPUT.PUT_LINE('num:'||no||'name:'||name);
EXCEPTION
WHEN err THEN
DBMS_OUTPUT.PUT_LINE('no data found for '||vno);
END;
/

exec ya_proc('99');
--result
no data found for 99
```

### SQLCODE&SQLERRM

SQLCODE and SQLERRM are system-provided functions for exception handling, used to obtain the error number and error message corresponding to exceptions. Their syntax is:

*SQLCODE[()]*

*SQLERRM[(errCode)]*

SQLCODE and SQLERRM can only be used in procedural statements and not in SQL statements. When used in non-exception handling statements, the SQLCODE function will return a value of 0, and the SQLERRM function will return "YAS-00000 normal, successful completion."

When used in exception handling statements, the SQLCODE function will return the current captured exception error number, and SQLERRM will return according to the following rules:

- When the errCode parameter is not specified, it returns the corresponding error message for the current error.
- When the errCode parameter is specified, if errCode exceeds the specified range in the table, it returns "%d non-yas exception"; otherwise, it looks up the error message corresponding to errCode and returns it.
- If the current error number or errCode does not have a defined error message, it returns "YAS-%05d message %05d not found".

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(vno CHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
err EXCEPTION;
PRAGMA EXCEPTION_INIT(err, 45000);
BEGIN
DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
IF vno>'10' THEN
RAISE err;
END IF;
EXECUTE IMMEDIATE str1 INTO no,name USING vno;      
DBMS_OUTPUT.PUT_LINE('num:'||no||'name:'||name);
EXCEPTION
WHEN err THEN
DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
END;
/

exec ya_proc('99');
--result
0:YAS-00000 normal, successful completion
45000:YAS-45000 message 45000 not found
```

### STANDARD

STANDARD is used to indicate that the subsequent exception_name is a system predefined exception name. When user-defined exceptions may have the same name as system predefined exceptions, using STANDARD can accurately identify the system predefined exception. The syntax format is:

*STANDARD.exception_name*

exception_name must be the name of the system predefined exception; otherwise, a compilation error will occur.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(vno CHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
no_data_found EXCEPTION;
PRAGMA EXCEPTION_INIT(no_data_found, 45000);
BEGIN
IF vno>'10' THEN
RAISE no_data_found;
ELSE 
EXECUTE IMMEDIATE str1 INTO no,name USING vno;      
DBMS_OUTPUT.PUT_LINE('num:'||no||'name:'||name);
END IF;
EXCEPTION
WHEN STANDARD.NO_DATA_FOUND THEN
DBMS_OUTPUT.PUT_LINE('Standard exception.');
WHEN NO_DATA_FOUND THEN
DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
END;
/

exec ya_proc('99');
--result
45000:YAS-45000 message 45000 not found

exec ya_proc('09');
--result
Standard exception.
```

<span id="RAISE" name="RAISE" class="yaslink"></span>

### RAISE

RAISE statement is used to explicitly throw exceptions. Its syntax format is:

*RAISE [exception_name];*

When exception_name is not specified, this statement must be located within an exception handler (EXCEPTION Statement), indicating that the current exception will be thrown outward.

exception_name can be a user-defined exception or a system predefined exception.

If the exception handler at this layer does not catch the exception_name after RAISE, that exception will be thrown outward; in cases where a user-defined exception shares the same name as a system predefined exception, STANDARD must be used to catch it.

***Example***

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(vno CHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
no_data_found EXCEPTION;
BEGIN
IF vno>'10' THEN
BEGIN
DBMS_OUTPUT.PUT_LINE('First:');
RAISE no_data_found;
EXCEPTION
WHEN NO_DATA_FOUND THEN
RAISE;
END;
ELSE
DBMS_OUTPUT.PUT_LINE('Second:');
EXECUTE IMMEDIATE str1 INTO no,name USING vno;      
DBMS_OUTPUT.PUT_LINE('num:'||no||'name:'||name);
END IF;
EXCEPTION
WHEN STANDARD.NO_DATA_FOUND THEN
DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
WHEN OTHERS THEN
DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
END;
/

exec ya_proc('99');
--result
First:
21000:YAS-21000 message 21000 not found

exec ya_proc('09');
--result
Second:
5206:YAS-05206 no data found
```
