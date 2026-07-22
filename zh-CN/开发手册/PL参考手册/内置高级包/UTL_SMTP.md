UTL_SMTP包是用于SMTP（简单邮件传输协议）客户端操作的内置包，提供了一组子程序、常量及数据结构。用户可以通过UTL_SMTP包使用PL程序通过SMTP协议发送电子邮件。

该高级包的使用规则如下：

- 使用该高级包时，务必确保数据库服务器能够访问SMTP服务器端口。由于此操作会消耗网络资源，可能对数据库运行性能造成一定影响，请合理规划使用时间。

- SMTP命令有严格的顺序要求，需按HELO、MAIL、RCPT以及DATA顺序执行。

- 完成邮件发送后应及时调用QUIT或CLOSE_CONNECTION释放连接资源。

## 数据结构

UTL_SMTP包中使用自定义类型数据结构作为函数参数或返回值。

### CONNECTION类型

```plsql
TYPE CONNECTION IS RECORD(
   host             VARCHAR2(255),
   port             PLS_INTEGER,
   tx_timeout       PLS_INTEGER,
   private_tcp_con  INTEGER,
   private_state    INTEGER
);
```

CONNECTION类型用于表示SMTP连接会话。参数描述如下：

| 参数 | 描述 |
| :--- | :--- |
| host | SMTP服务器的域名或IP地址。 |
| port | SMTP服务器的端口号。 |
| tx_timeout | 传输超时时间（单位：秒）。 |
| private_tcp_con | 内部TCP连接句柄。 |
| private_state | 内部状态信息。 |

### REPLY类型

```plsql
TYPE REPLY IS RECORD(
   code     INTEGER,
   text     VARCHAR2(508));
```

REPLY类型用于表示SMTP响应信息。参数描述如下：

| 参数 | 描述 |
| :--- | :--- |
| code | SMTP响应码（3位数字），例如220、250、354等。 |
| text | SMTP响应的文本信息。 |

### REPLIES类型

```plsql
TYPE REPLIES IS TABLE OF REPLY INDEX BY BINARY_INTEGER;
```

REPLIES类型用于存储多个SMTP响应，主要用于EHLO命令返回的服务器扩展功能列表。

## 预定义常量

UTL_SMTP系统包定义了一组常量，供UTL_SMTP高级包相关函数及子程序调用。

| 名称 | 值 | 描述 |
| :--- | :--- | :--- |
| ALL_SCHEMES | 'CRAM-MD5 PLAIN LOGIN' | 支持的所有认证方案。 |
| NON_CLEARTEXT_PASSWORD_SCHEMES | 'CRAM-MD5' | 非明文密码认证方案。 |

## 异常说明

UTL_SMTP包中定义的异常类型。

| 异常名 | 错误码 | 说明 |
| :--- | :--- | :--- |
| invalid_operation | ERR_PL_SMTP_INVALID_OPERATION | 无效操作，未按正确顺序调用SMTP命令时触发。 |
| transient_error | ERR_PL_SMTP_TRANSIENT_ERROR | 瞬时服务器错误，SMTP服务器返回4xx响应时触发。 |
| permanent_error | ERR_PL_SMTP_PERMANENT_ERROR | 永久服务器错误，SMTP服务器返回5xx响应时触发。 |
| unsupported_scheme | ERR_PL_SMTP_UNSUPPORTED_SCHEME | 不支持的认证方案。 |
| no_supported_scheme | ERR_PL_SMTP_NO_SUPPORTED_SCHEME | 没有支持的认证方案。 |

## 子程序说明

### OPEN_CONNECTION

```plsql
-- 重载版本1：返回connection类型
UTL_SMTP.OPEN_CONNECTION(
   host            IN  VARCHAR2,
   port            IN  PLS_INTEGER DEFAULT 25,
   c               OUT CONNECTION,
   tx_timeout      IN  PLS_INTEGER DEFAULT NULL,
   wallet_path     IN  VARCHAR2 DEFAULT NULL,
   wallet_password IN  VARCHAR2 DEFAULT NULL,
   secure_connection_before_smtp IN BOOLEAN DEFAULT FALSE,
   secure_host     IN  VARCHAR2 DEFAULT NULL)
 RETURN REPLY;

-- 重载版本2：直接返回connection类型
UTL_SMTP.OPEN_CONNECTION(
   host            IN  VARCHAR2,
   port            IN  PLS_INTEGER DEFAULT 25,
   tx_timeout      IN  PLS_INTEGER DEFAULT NULL,
   wallet_path     IN  VARCHAR2 DEFAULT NULL,
   wallet_password IN  VARCHAR2 DEFAULT NULL,
   secure_connection_before_smtp IN BOOLEAN DEFAULT FALSE,
   secure_host     IN  VARCHAR2 DEFAULT NULL)
 RETURN CONNECTION;
```

建立与SMTP服务器的TCP连接。

调用OPEN_CONNECTION前，需确保SMTP服务器可访问。

| 参数 | 描述 |
| :--- | :--- |
| host | SMTP服务器的域名或IP地址。 |
| port | SMTP服务器的端口号，默认为25。 |
| c | 输出参数，SMTP连接。 |
| tx_timeout | 传输超时时间（秒），默认为NULL。 |
| wallet_path |  仅用于兼容，无实际含义。 |
| wallet_password |  仅用于兼容，无实际含义。 |
| secure_connection_before_smtp |  仅用于兼容，无实际含义。 |
| secure_host |  仅用于兼容，无实际含义。 |


示例

```sql
DECLARE
   c UTL_SMTP.CONNECTION;
BEGIN
   c := UTL_SMTP.OPEN_CONNECTION('smtp.example.com', 25);
END;
/
```

### HELO

```plsql
UTL_SMTP.HELO(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   domain IN VARCHAR2);
```

向SMTP服务器发送HELO命令，标识客户端身份。

调用HELO前，需先建立TCP连接（OPEN_CONNECTION）。

| 参数 | 描述 |
| :--- | :--- |
| c | SMTP连接。 |
| domain | 客户端域名。 |

### EHLO

```plsql
UTL_SMTP.EHLO(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   domain IN VARCHAR2)
 RETURN UTL_SMTP.REPLIES;
```

向SMTP服务器发送EHLO命令，获取服务器支持的扩展功能列表。

| 参数 | 描述 |
| :--- | :--- |
| c | SMTP连接。 |
| domain | 客户端域名。 |

### MAIL

```plsql
UTL_SMTP.MAIL(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   sender IN VARCHAR2,
   parameters IN VARCHAR2 DEFAULT NULL);
```

向SMTP服务器发送MAIL命令，指定发件人。

调用MAIL前，需先完成HELO或EHLO。

| 参数 | 描述 |
| :--- | :--- |
| c | SMTP连接。 |
| sender | 发件人邮箱地址，格式为`<user@example.com>`。 |
| parameters | 可选参数，如SIZE等。 |

### RCPT

```plsql
UTL_SMTP.RCPT(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   recipient IN VARCHAR2,
   parameters IN VARCHAR2 DEFAULT NULL);
```

向SMTP服务器发送RCPT命令，指定收件人。

调用RCPT前，需先完成MAIL命令。

| 参数 | 描述 |
| :--- | :--- |
| c | SMTP连接。 |
| recipient | 收件人邮箱地址，格式为`<user@example.com>`。 |
| parameters | 可选参数，如NOTIFY等。 |

### DATA Function

```plsql
UTL_SMTP.DATA(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   body IN VARCHAR2)
 RETURN UTL_SMTP.REPLY;
```

向SMTP服务器发送邮件内容。

调用DATA前，需先完成RCPT命令。

| 参数 | 描述 |
| :--- | :--- |
| c | SMTP连接。 |
| body | 邮件正文内容。 |

### OPEN_DATA Procedure

```plsql
UTL_SMTP.OPEN_DATA(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION);
```

打开数据发送模式，准备流式发送邮件内容。

调用OPEN_DATA前，需先完成RCPT命令。

### WRITE_DATA Procedure

```plsql
UTL_SMTP.WRITE_DATA(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   data IN VARCHAR2);
```

在流式发送模式下，写入邮件内容。

调用WRITE_DATA前，需先调用OPEN_DATA。

| 参数 | 描述 |
| :--- | :--- |
| c | SMTP连接。 |
| data | 邮件内容。 |

### WRITE_RAW_DATA Procedure

```plsql
UTL_SMTP.WRITE_RAW_DATA(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   data IN RAW);
```

在流式发送模式下，写入原始二进制邮件内容。

调用WRITE_RAW_DATA前，需先调用OPEN_DATA。

| 参数 | 描述 |
| :--- | :--- |
| c | SMTP连接。 |
| data | 原始二进制数据。 |

### CLOSE_DATA

```plsql
UTL_SMTP.CLOSE_DATA(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION)
 RETURN UTL_SMTP.REPLY;
```

关闭数据发送模式，完成邮件发送。

调用CLOSE_DATA前，需先调用OPEN_DATA。

### NOOP

```plsql
UTL_SMTP.NOOP(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION);
```

向SMTP服务器发送NOOP命令，用于保持连接活跃或测试连接状态。

### RSET

```plsql
UTL_SMTP.RSET(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION);
```

向SMTP服务器发送RSET命令，重置当前邮件会话。

### QUIT

```plsql
UTL_SMTP.QUIT(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION)
 RETURN UTL_SMTP.REPLY;
```

向SMTP服务器发送QUIT命令，关闭连接。

### CLOSE_CONNECTION Procedure

```plsql
UTL_SMTP.CLOSE_CONNECTION(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION);
```

关闭SMTP连接，释放资源。

### COMMAND

```plsql
UTL_SMTP.COMMAND(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   cmd IN VARCHAR2,
   arg IN VARCHAR2 DEFAULT NULL)
 RETURN UTL_SMTP.REPLY;
```

向SMTP服务器发送自定义命令。

| 参数 | 描述 |
| :--- | :--- |
| c | SMTP连接。 |
| cmd | SMTP命令名称。 |
| arg | 命令参数。 |

示例

```sql
DECLARE
   c UTL_SMTP.CONNECTION;
   reply UTL_SMTP.REPLY;
BEGIN
   c := UTL_SMTP.OPEN_CONNECTION('smtp.example.com', 25);
   UTL_SMTP.HELO(c, 'example.com');
   UTL_SMTP.COMMAND(c, 'HELP');
   UTL_SMTP.QUIT(c);
END;
/
```

## 使用示例

以下示例演示如何使用UTL_SMTP发送简单邮件：

```sql
DECLARE
   c UTL_SMTP.CONNECTION;
BEGIN
   -- 建立连接
   c := UTL_SMTP.OPEN_CONNECTION('smtp.example.com', 25);

   -- 标识客户端
   UTL_SMTP.HELO(c, 'example.com');

   -- 指定发件人
   UTL_SMTP.MAIL(c, '<sender@example.com>');

   -- 指定收件人
   UTL_SMTP.RCPT(c, '<recipient@example.com>');

   -- 发送邮件内容
   UTL_SMTP.DATA(c, 'From: sender@example.com' || UTL_TCP.CRLF ||
                   'To: recipient@example.com' || UTL_TCP.CRLF ||
                   'Subject: Test Mail' || UTL_TCP.CRLF ||
                   UTL_TCP.CRLF ||
                   'This is a test email.');

   -- 关闭连接
   UTL_SMTP.QUIT(c);
END;
/
```