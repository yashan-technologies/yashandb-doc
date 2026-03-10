CLOSE Statement为关闭游标语句，系统对显式游标和动态游标的关闭方法一致，且格式均为：

_CLOSE cursor_name;_

其中，cursor_name为显式游标或动态游标的名称， cursor_name不能是绑定参数。

关闭游标表示关闭游标指向的结果集。如果游标并非打开状态，或者重复关闭，系统将返回invalid cursor错误。

示例

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

