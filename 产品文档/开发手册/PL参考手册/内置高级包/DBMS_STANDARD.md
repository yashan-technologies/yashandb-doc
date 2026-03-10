DBMS_STANDARD包提供了一组内置的存储过程/函数，主要用于PL中的异常处理，事务处理，及外置java函数控制。

调用DBMS_STANDARD包的子程序时，可以省略DBMS_STANDARD，直接使用子程序名。

## RAISE\_APPLICATION\_ERROR

示例

```plsql
DBMS_STANDARD.RAISE_APPLICATION_ERROR(
    errCode         IN INTEGER,
    errMsg          IN VARCHAR,
    reserveErrStack IN BOOLEAN DEFAULT FALSE);
```

RAISE_APPLICATION_ERROR存储过程用于在PL程序中手动抛出异常。

| 参数    | 描述                     |
| :------ |:-----------------------|
| errCode | 错误号，范围在[30000, 99999]。 |
| errMsg  | 错误消息，长度不大于1024bytes。错误消息字符串的空白符后缀会被移除。   |
| reserveErrStack  | 保留字段，仅语法兼容。   |

示例
```plsql
CREATE OR replace PROCEDURE account_status (
    due_date DATE,
    today DATE) IS
BEGIN
    IF due_date < today THEN -- explicitly RAISE EXCEPTION
      DBMS_STANDARD.RAISE_APPLICATION_ERROR(29999+1, 'Account past due.');
    END IF;
END;
/

DECLARE
    past_due EXCEPTION; -- DECLARE EXCEPTION
    PRAGMA EXCEPTION_INIT (past_due, 30000); 
BEGIN
    account_status (TO_DATE('01-JUL-2010', 'DD-MON-YYYY'),
    TO_DATE('09-JUL-2010', 'DD-MON-YYYY')); 
EXCEPTION
    WHEN past_due THEN -- handle EXCEPTION
        DBMS_OUTPUT.PUT_LINE('SUCCESS!');
END;
/

--result
SUCCESS!
```

## LOADJAVA

示例

```plsql
DBMS_STANDARD.LOADJAVA(
    javaPath IN VARCHAR);
```

LOADJAVA存储过程用于加载一个java文件，在外置UDF场景中使用。

YashanDB会在当前用户下自动创建一个自定义库对象，对象的名称由输入的路径按照一定规则生成，区分大小写，需要符合YashanDB的[对象命名规范](../../SQL参考手册/基本SQL元素/标识符)。

YashanDB已提供直接创建自定义库的功能，此方式不再建议使用。

| 参数     | 描述                                                |
| :------- | :-------------------------------------------------- |
| javaPath | 需要加载的class文件或者jar路径，长度不超过255字节。 |

示例

```plsql
call DBMS_STANDARD.LOADJAVA('/mnt/d/com/test/Hello.class');
```

## DROPJAVA

```plsql
DBMS_STANDARD.DROPJAVA(
    javaPath IN VARCHAR);
```

DROPJAVA存储过程用于卸载一个java文件，在外置UDF场景中使用。

| 参数     | 描述                                                |
| :------- | :-------------------------------------------------- |
| javaPath | 需要卸载的class文件或者jar路径，长度不超过255字节。 |

示例

```plsql
call DBMS_STANDARD.DROPJAVA('/mnt/d/com/test/Hello.class');
```

## INSERTING

INSERTING函数用于在INSERT语句中执行的触发器内返回TRUE，该函数无参数，使用请参考[触发器](../PL对象/触发器)中的例程。

## DELETING

DELETING函数用于在DELETE语句中执行的触发器内返回TRUE，该函数无参数，使用请参考[触发器](../PL对象/触发器)中的例程。

## UPDATING

示例

```plsql
DBMS_STANDARD.UPDATING(
    colName IN VARCHAR);
```
UPDATING函数用于在UPDATE语句中执行的触发器内更新的列与输入参数匹配时返回TRUE。

| 参数    | 描述                                                         |
| :------ | :----------------------------------------------------------- |
| colName | UPDATE操作中更新的列名。当更新的列与colName一致时返回TRUE。当没有设置colName时，更新任何列都返回TRUE。 |

使用请参考[触发器](../PL对象/触发器)中的例程。
