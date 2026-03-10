OWA_UTIL包提供了一组内置子程序，用于执行返回过程体调用信息等操作。

## WHO\_CALLED\_ME

```plsql
OWA_UTIL.WHO_CALLED_ME(
   owner          OUT      VARCHAR2,
   name           OUT      VARCHAR2,
   lineno         OUT      NUMBER,
   caller_t       OUT      VARCHAR2);
```

WHO_CALLED_ME程序通过出参的方式返回调用它的PL过程体单元信息。
|  参数| 描述|
| :------------------------- | :--------------------------------- |
| owner         | 过程体单元的所有者。  |
| name          | 过程体单元的名字。   |
| lineno        | 进行调用的过程体单元内的调用位置行号。 |
| caller_t      | 进行调用的过程体单元的类型。 |

示例

```plsql
CREATE OR replace PROCEDURE proc1 AS
owner    VARCHAR(200);
name     varchar(200);
lineno   number;
caller_t varchar(200);
BEGIN
owa_util.who_called_me(owner, name, lineno, caller_t);
dbms_output.put_line('owener: ' || owner || ' name: ' || name || ' lineno: ' || lineno || ' caller_t: '|| caller_t);
END;
/

BEGIN
proc1();
END;
/
owener:  name:  lineno: 2 caller_t: ANONYMOUS BLOCK

```
