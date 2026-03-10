UTL_TCP包提供了一种额外的方法来主动与应用程序来进行通信，该过程包含了CLOSE_ALL_CONNECTIONS子函数和CRLF变量。

UTL_TCP包仅进行语法兼容适配，无实际含义。

## CRLF

```PLSQL
CRLF CONSTANT VARCHAR(2) 
```

代表序列回车换行的字符，用于赋值或者初始化。

## CLOSE\_ALL\_CONNECTIONS

```PLSQL
UTL_TCP.CLOSE_ALL_CONNECTIONS;
```

用于在PL/SQL程序结束前关闭所有连接，以避免出现悬空连接。仅语法兼容，执行后默认返回`Succeed`，无实际含义。

