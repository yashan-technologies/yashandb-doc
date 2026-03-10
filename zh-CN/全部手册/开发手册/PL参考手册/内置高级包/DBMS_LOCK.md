DBMS_LOCK包提供了一组内置的存储过程/函数，用于实现锁相关功能。

## SLEEP

```plsql
DBMS_LOCK.SLEEP(
	second IN NUMBER);
```

SLEEP为存储过程，根据输入的参数值暂停对应的时间，单位为秒。

执行本程序时，系统将按参数指定的秒数进行休眠，如需在秒数结束前停止休眠，须在终端执行CTRL+C、Shutdown等指令进行强制退出。

|  参数| 描述|
| :----- | :----------------------------------------------------------- |
| second | 指定休眠的时间，不可输入空，数值范围必须为[0,21474836.47]，支持入参类型隐式转换。 |

示例

```plsql
-- 数值型
exec DBMS_LOCK.SLEEP (10);

-- 字符串型
exec DBMS_LOCK.SLEEP ('10');

-- BIT型
DECLARE
vsql VARCHAR(256);
vbit BIT(8);
BEGIN
vsql := 'exec DBMS_LOCK.SLEEP (?)';
vbit := 5;
EXECUTE IMMEDIATE vsql USING vbit;
END;
/

-- 布尔型
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
