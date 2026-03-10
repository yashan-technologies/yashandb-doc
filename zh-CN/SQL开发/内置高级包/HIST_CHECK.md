HIST_CHECK高级包提供了一组内置的存储过程/函数，用于开启/关闭指定表的防篡改功能、校验指定表是否发生过篡改。

篡改主要是指对目标表执行了增、删、改等操作，执行方式包括：

- 直接修改：对表执行INSERT/DELETE/UPDATE/TRUNCATE等SQL语句。
- 间接修改：通过LOAD DATA导入表数据、通过DBMS_LOB高级包修改表数据等。

该内置高级包仅适用于单机部署中的HEAP表，且不能为系统表。

开启/关闭/校验均需要当前用户对指定表具有READ/SELECT权限。

## ENABLE\_CHECK

```plsql
HIST_CHECK.ENABLE_CHECK (
    user_name VARCHAR,
    table_name  VARCHAR
);
```

此存储过程用于对指定表开启防篡改能力。

|  参数| 描述|
|:-----------| :---- |
| user_name  | 用户名 |
| table_name | 表名 |

示例（单机HEAP表）

```plsql
BEGIN
    HIST_CHECK.ENABLE_CHECK('sales','employees');
END;
/
```

## DISABLE\_CHECK

```plsql
HIST_CHECK.DISABLE_CHECK (
    user_name VARCHAR,
    table_name  VARCHAR
);
```

此存储过程用于对指定表关闭防篡改能力。

|  参数| 描述|
|:-----------| :---- |
| user_name  | 用户名 |
| table_name | 表名 |

示例（单机HEAP表）

```plsql
BEGIN
    HIST_CHECK.DISABLE_CHECK('sales','employees');
END;
/
```

## ACHECK

```plsql
HIST_CHECK.ACHECK (
    user_name VARCHAR,
    table_name  VARCHAR
);
```

此函数用于对指定表校验是否发生篡改，返回结果为TRUE或FALSE。

- TRUE：表示未发生篡改或防篡改功能暂未开启。
- FALSE：表示从开启防篡改功能至今，发生过篡改。

|  参数| 描述|
|:-----------| :---- |
| user_name  | 用户名 |
| table_name | 表名 |

示例（单机HEAP表）

```plsql
-- 当指定表未开启防篡改能力/未发生篡改时，校验函数返回TRUE。
SELECT HIST_CHECK.ACHECK('sales', 'employees') employees FROM dual;

EMPLOYEES
--------------------
true

-- 当指定表开启防篡改能力，并且发生篡改操作后，校验函数返回FALSE。
EXEC HIST_CHECK.ENABLE_CHECK('sales','employees');

INSERT INTO sales.employees VALUES ('0101','000','0101000001','Mask','1',SYSDATE-1000);

SELECT HIST_CHECK.ACHECK('sales', 'employees') employees FROM dual;

EMPLOYEES
--------------------
false

-- 关闭指定表的防篡改功能。
EXEC HIST_CHECK.DISABLE_CHECK ('sales','employees');

```
