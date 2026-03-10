DBMS_SESSION包提供了一组内置的存储过程，用于管理会话级别的信息。

该高级包仅适用于单机部署。

## SET\_IDENTIFIER

```plsql
DBMS_SESSION.SET_IDENTIFIER (
	client_identifier   IN   VARCHAR);
```

SET_IDENTIFIER为存储过程，通过传入client_identifer，为用户会话设置客户端标识符，设置后V$SESSION上展示对应字段CLIENT_IDENTIFIER。

| 参数                | 描述 |
| :------------------ | :--------|
|client_identifier    |用户传入用于设置客户端标识符的字符串，最大长度为64字节，输入超过64字节报错|

示例（单机部署）

```plsql
BEGIN
  DBMS_SESSION.SET_IDENTIFIER('abcd');
END;
/
```

## CLEAR\_IDENTIFIER

``` plsql
DBMS_SESSION.CLEAR_IDENTIFIER;
```

CLEAR_IDENTIFIER为存储过程，用于清除用户设置的标识符。

无参数。

示例（单机部署）

```plsql
BEGIN
    DBMS_SESSION.CLEAR_IDENTIFIER;
END;
/
```

## FREE\_UNUSED\_USER\_MEMORY

``` plsql
DBMS_SESSION.FREE_UNUSED_USER_MEMORY;
```

FREE_UNUSED_USER_MEMORY为存储过程，仅用于兼容，对系统无实际影响。

无参数。

示例（单机部署）

```plsql
BEGIN
    DBMS_SESSION.FREE_UNUSED_USER_MEMORY;
END;
/
```
