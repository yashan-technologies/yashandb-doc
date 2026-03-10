Autonomous transactions are independent transactions initiated by another transaction (the main transaction) in PL. Autonomous transactions do not share locks, resources, or commit dependencies with the main transaction, allowing them to independently execute SQL operations and commit or roll back.

The restrictions related to autonomous transactions are as follows:

- DML operations on LSC table cold data are not allowed within autonomous transactions.

## Declaring Autonomous Transactions

An autonomous transaction can be declared at the beginning of the program using the following command: PRAGMA AUTONOMOUS_TRANSACTION. Once declared, it begins the AUTONOMOUS ROUTINE, which may contain multiple COMMIT and ROLLBACK statements.

The rules for declaring autonomous transactions are as follows:

- An autonomous transaction must be declared in the outermost DECLARE block.
- An autonomous transaction is not a nested transaction; its commit and rollback operations do not affect the main transaction.
- An autonomous routine may contain multiple autonomous transactions.
- The statements related to the transaction in the routine must explicitly use COMMIT or ROLLBACK at the end, or it will lead to an error and rollback the statement.
- Exceptions occurring during autonomous transaction execution or when there is no COMMIT/ROLLBACK will be raised, allowing exception handling in the outer layer if corresponding EXCEPTION is present.
- Anonymous blocks, procedure bodies, triggers, and functions can be declared as autonomous transactions; other types cannot be used.

***Example***

```plsql
CREATE OR replace PROCEDURE Autonomous_Insert
AS
   PRAGMA autonomous_transaction;
BEGIN
    INSERT INTO area VALUES ( '00','autonomous','Autonomous Insert' );
    INSERT INTO area VALUES ( '99','autonomous','Autonomous Insert' );
    COMMIT;
END;
/

BEGIN
    INSERT INTO area VALUES ( '98','autonomous','Commit Block' );
    Autonomous_Insert;
    ROLLBACK;
END;
/

SELECT area_no,area_name,DHQ FROM area;

--result

AREA_NO AREA_NAME                                                     DHQ
------- ------------------------------------------------------------- ---------------------
01      EastChina                                                          Shanghai
02      WestChina                                                          Chengdu
03      SouthChina                                                          Guangzhou
04      NorthChina                                                          Beijing
05      CentralChina                                                          Wuhan
00      autonomous                                                      Autonomous Insert
99      autonomous                                                      Autonomous Insert


DELETE FROM area WHERE area_name='autonomous';

CREATE OR replace PROCEDURE Autonomous_Insert
AS
   PRAGMA autonomous_transaction;
BEGIN
    INSERT INTO area VALUES ( '00','autonomous','Autonomous Insert' );
END;
/

BEGIN
    INSERT INTO area VALUES ( '97','autonomous','Rollback Block' );
    Autonomous_Insert;
    ROLLBACK;
END;
/

YAS-05232 active autonomous transaction detected and rolled back
```
