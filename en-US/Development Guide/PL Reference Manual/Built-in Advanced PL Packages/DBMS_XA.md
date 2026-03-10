The DBMS_XA package provides a set of constants, data structures, and functions for calling XA interfaces in PL.

The DBMS_XA package is applicable only to Standalone Deployment.

The DBMS_XA package cannot be used with LSC tables.

Using the data structures and functions of the DBMS_XA package requires the system privileges EXECUTE ANY TYPE and EXECUTE ANY PROCEDURE.

## DBMS\_XA Data Structures

The DBMS_XA package uses UDT data structures as function parameters or return values.

### DBMS\_XA\_XID OBJECT TYPE

```plsql
TYPE DBMS_XA_XID IS OBJECT(
    formatid      NUMBER,
    gtrid         RAW(64),
    bqual         RAW(64),
    constructor function DBMS_XA_XID(
        formatid  IN   NUMBER,
        gtrid     IN   RAW,
        bqual     IN   RAW)
    RETURN SELF AS RESULT)
```

The DBMS_XA_XID provided by YashanDB is a UDT type body. All three parameters must be provided when using the constructor function. The parameter descriptions are as follows:

|Parameter |Description |
| :------------------- | :--------------------------------------------------------------------------------------|
| formatid            | Format identifier, compatible parameter, not meaningful in practice          |
| gtrid               | Global identifier, uniquely distinguishes global transactions, up to 64 bytes; trailing 0s will be ignored (i.e., '12300100' is equivalent to '123001') |
| bqual               | Branch qualifier, up to 64 bytes; trailing 0s will be ignored (i.e., '12300100' is equivalent to '123001') |

### DBMS\_XA\_XID\_ARRAY TABLE TYPE

The DBMS_XA_XID_ARRAY provided by YashanDB is a nested table type UDT.

```plsql
TYPE DBMS_XA_XID_ARRAY as TABLE of DBMS_XA_XID
```

## DBMS\_XA Constants

The DBMS_XA advanced package defines several constants to be used as function parameters or return values.

The following table lists constants that can be used as parameters for the XA_START and XA_END functions.

|Name |Type |Value |Description |
| :------------------- | :----------------| :------------------| :--------------------------------|
| TMNOFLAGS            | PLS_INTEGER      | 00000000           | Indicates no flags selected            |
| TMSUCCESS            | PLS_INTEGER      | 0x04000000         | Suspends transaction branch and cancels the association with the caller and the transaction branch |
| TMJOIN               | PLS_INTEGER      | 0x00200000         | Caller joins an existing transaction branch |
| TMSUSPEND            | PLS_INTEGER      | 0x02000000         | Only suspends the current transaction branch |
| TMRESUME             | PLS_INTEGER      | 0x08000000         | Equivalent to TMJOIN                 |

The following table lists possible constant return values from functions in the DBMS_XA package.

|Name |Type |Value |Description |
| :------------------- | :----------------| :----------| :--------------------------------|
| XA_OK                | PLS_INTEGER      | 0          | Executed successfully                 |
| XAER_ASYNC           | PLS_INTEGER      | -2         | Asynchronous operation not complete   |
| XAER_RMERR           | PLS_INTEGER      | -3         | Resource manager error in transaction branch |
| XAER_NOTA            | PLS_INTEGER      | -4         | Invalid XID                          |
| XAER_INVAL           | PLS_INTEGER      | -5         | Invalid parameter                    |
| XAER_PROTO           | PLS_INTEGER      | -6         | Improper relational context for the call |
| XAER_RMFAIL          | PLS_INTEGER      | -7         | Resource manager currently unavailable |
| XAER_DUPID           | PLS_INTEGER      | -8         | Specified XID already exists         |
| XAER_OUTSIDE         | PLS_INTEGER      | -9         | Resource manager operating outside of global transaction |
| XAER_PXACT           | PLS_INTEGER      | -10        | Parallel transactions cannot be converted to XA transactions |

## DBMS\_XA Functions

### XA\_START

```plsql
DBMS_XA.XA_START(
    xid IN DBMS_XA_XID,
    flag IN INTEGER)
RETURN PLS_INTEGER;
```

The XA_START function is used to associate the current session with the transaction branch specified by xid.

The flag parameter of the XA_START function can only be one of TMNOFLAGS, TMJOIN, or TMRESUME. If the flag is TMNOFLAGS, a new transaction branch will be started; otherwise, the current session will associate with an existing transaction branch.

|Parameter |Description |
| :------------------- | :-----------------------------------------------------------|
| xid                  | Specifies the transaction branch.        |
| flag                 | Flag constant.                           |

***Example*** for Standalone Deployment

```plsql
-- Declare variable of DBMS_XA_XID type and call XA_START function 
DECLARE
    ret INT;
    xid DBMS_XA_XID := DBMS_XA_XID(1, HEXTORAW('1'), HEXTORAW('1'));
BEGIN
    ret := DBMS_XA.XA_START(xid, DBMS_XA.TMNOFLAGS);
END;
/
```

### XA\_END

```plsql
DBMS_XA.XA_END(
    xid IN DBMS_XA_XID,
    flag IN INTEGER)
RETURN PLS_INTEGER;
```

The XA_END function is used to end the association between the current session and the transaction branch specified by xid.

The flag parameter of the XA_END function can only be one of TMNOFLAGS, TMSUCCESS, or TMSUSPEND, with TMNOFLAGS being equivalent to TMSUSPEND.

After the function successfully returns, the transaction branch still exists.

|Parameter |Description |
| :------------------- | :-----------------------------------------------------------|
| xid                  | Specifies the transaction branch.        |
| flag                 | Flag constant.                           |

***Example*** for Standalone Deployment

```plsql
-- Declare variable of DBMS_XA_XID type and call XA_END function 
DECLARE
    ret INT;
    xid DBMS_XA_XID := DBMS_XA_XID(1, HEXTORAW('1'), HEXTORAW('1'));
BEGIN
    ret := DBMS_XA.XA_END(xid, DBMS_XA.TMNOFLAGS);
END;
/
```

### XA\_PREPARE

```plsql
DBMS_XA.XA_PREPARE(
    xid IN DBMS_XA_XID)
RETURN PLS_INTEGER;
```

The XA_PREPARE function is used to prepare to commit the transaction branch specified by xid.

|Parameter |Description |
| :------------------- | :-----------------------------------------------------------|
| xid                  | Specifies the transaction branch.        |

***Example*** for Standalone Deployment

```plsql
-- Declare variable of DBMS_XA_XID type and call XA_PREPARE function 
DECLARE
    ret INT;
    xid DBMS_XA_XID := DBMS_XA_XID(1, HEXTORAW('1'), HEXTORAW('1'));
BEGIN
    ret := DBMS_XA.XA_PREPARE(xid);
END;
/
```

### XA\_COMMIT

```plsql
DBMS_XA.XA_COMMIT(
    xid IN DBMS_XA_XID,
    onePhase IN BOOLEAN)
RETURN PLS_INTEGER;
```

The XA_COMMIT function is used to commit the transaction branch specified by xid.

If the value of the onePhase parameter is TRUE, the resource manager should use a one-phase protocol to commit on behalf of xid.

COMMIT statements cannot be executed in XA transactions.

|Parameter |Description |
| :------------------- | :-----------------------------------------------------------|
| xid                  | Specifies the transaction branch.        |
| onePhase             | Indicates whether to apply one-phase commit. |

***Example*** for Standalone Deployment

```plsql
-- Declare variable of DBMS_XA_XID type and call XA_COMMIT function 
DECLARE
    ret INT;
    xid DBMS_XA_XID := DBMS_XA_XID(1, HEXTORAW('1'), HEXTORAW('1'));
BEGIN
    ret := DBMS_XA.XA_COMMIT(xid, false);
END;
/
```

### XA\_RECOVER

```plsql
DBMS_XA.XA_RECOVER
RETURN DBMS_XA_XID_ARRAY;
```

The XA_RECOVER function is used to obtain a list of prepared or completed transaction branches from the resource manager (RM).

The formatid query result is always 0.

***Example*** for Standalone Deployment

```plsql
-- Declare variable of DBMS_XA_XID_ARRAY type and call XA_RECOVER function 
DECLARE
    xid_array DBMS_XA_XID_ARRAY;
BEGIN
    xid_array := DBMS_XA.XA_RECOVER();
END;
/
```

### XA\_FORGET

```plsql
DBMS_XA.XA_FORGET(
    xid IN DBMS_XA_XID)
RETURN PLS_INTEGER;
```

The XA_FORGET function is used to notify the resource manager (RM) to discard committed or rolled-back transaction branches.

|Parameter |Description |
| :------------------- | :-----------------------------------------------------------|
| xid                  | Specifies the transaction branch.        |

***Example*** for Standalone Deployment

```plsql
-- Declare variable of DBMS_XA_XID type and call XA_FORGET function 
DECLARE
    ret INT;
    xid DBMS_XA_XID := DBMS_XA_XID(1, HEXTORAW('1'), HEXTORAW('1'));
BEGIN
    ret := DBMS_XA.XA_FORGET(xid);
END;
/
```

### XA\_ROLLBACK

```plsql
DBMS_XA.XA_ROLLBACK(
    xid IN DBMS_XA_XID)
RETURN PLS_INTEGER;
```

The XA_ROLLBACK function is used to notify the resource manager (RM) to roll back the transaction branch.

ROLLBACK statements are not recommended in XA transactions.

|Parameter |Description |
| :------------------- | :-----------------------------------------------------------|
| xid                  | Specifies the transaction branch.        |

***Example*** for Standalone Deployment

```plsql
-- Declare variable of DBMS_XA_XID type and call XA_ROLLBACK function 
DECLARE
    ret INT;
    xid DBMS_XA_XID := DBMS_XA_XID(1, HEXTORAW('1'), HEXTORAW('1'));
BEGIN
    ret := DBMS_XA.XA_ROLLBACK(xid);
END;
/
```
