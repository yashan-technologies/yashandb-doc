UTL_SMTP is a built-in package for SMTP (Simple Mail Transfer Protocol) client operations, providing a set of subprograms, constants, and data structures. Users can send emails through SMTP protocol using PL/SQL programs through the UTL_SMTP package.

The usage rules for this advanced package are as follows:

- When using this advanced package, ensure that the database server can access the SMTP server port. Since this operation consumes network resources and may affect database performance, plan the usage time reasonably.

- SMTP commands have strict ordering requirements and must be executed in the sequence: HELO, MAIL, RCPT, and DATA.

- After sending emails, promptly call QUIT or CLOSE_CONNECTION to release connection resources.

## Data Structures

UTL_SMTP package uses custom type data structures as function parameters or return values.

### CONNECTION Type

```plsql
TYPE CONNECTION IS RECORD(
   host             VARCHAR2(255),
   port             PLS_INTEGER,
   tx_timeout       PLS_INTEGER,
   private_tcp_con  INTEGER,
   private_state    INTEGER
);
```

The CONNECTION type is used to represent an SMTP connection session. The parameters are described as follows:

| Parameter | Description |
| :--- | :--- |
| host | Domain name or IP address of the SMTP server. |
| port | Port number of the SMTP server. |
| tx_timeout | Transmission timeout in seconds. |
| private_tcp_con | Internal TCP connection handle. |
| private_state | Internal state information. |

### REPLY Type

```plsql
TYPE REPLY IS RECORD(
   code     INTEGER,
   text     VARCHAR2(508));
```

The REPLY type is used to represent SMTP response information. The parameters are described as follows:

| Parameter | Description |
| :--- | :--- |
| code | SMTP response code (3-digit number), such as 220, 250, 354, etc. |
| text | Text information of the SMTP response. |

### REPLIES Type

```plsql
TYPE REPLIES IS TABLE OF REPLY INDEX BY BINARY_INTEGER;
```

The REPLIES type is used to store multiple SMTP responses, mainly used for the server extension list returned by the EHLO command.

## Predefined Constants

The UTL_SMTP system package defines a set of constants for use by UTL_SMTP advanced package related functions and subprograms.

| Name | Value | Description |
| :--- | :--- | :--- |
| ALL_SCHEMES | 'CRAM-MD5 PLAIN LOGIN' | All supported authentication schemes. |
| NON_CLEARTEXT_PASSWORD_SCHEMES | 'CRAM-MD5' | Non-plaintext password authentication schemes. |

## Exception Description

Exceptions defined in the UTL_SMTP package.

| Exception Name | Error Code | Description |
| :--- | :--- | :--- |
| invalid_operation | ERR_PL_SMTP_INVALID_OPERATION | Invalid operation, triggered when SMTP commands are not called in the correct sequence. |
| transient_error | ERR_PL_SMTP_TRANSIENT_ERROR | Transient server error, triggered when the SMTP server returns a 4xx response. |
| permanent_error | ERR_PL_SMTP_PERMANENT_ERROR | Permanent server error, triggered when the SMTP server returns a 5xx response. |
| unsupported_scheme | ERR_PL_SMTP_UNSUPPORTED_SCHEME | Unsupported authentication scheme. |
| no_supported_scheme | ERR_PL_SMTP_NO_SUPPORTED_SCHEME | No supported authentication scheme. |

## Subprogram Description

### OPEN_CONNECTION

```plsql
-- Overload 1: Returns REPLY type
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

-- Overload 2: Returns CONNECTION type directly
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

Establishes a TCP connection with the SMTP server.

Before calling OPEN_CONNECTION, ensure that the SMTP server is accessible.

| Parameter | Description |
| :--- | :--- |
| host | Domain name or IP address of the SMTP server. |
| port | Port number of the SMTP server, defaults to 25. |
| c | Output parameter, SMTP connection. |
| tx_timeout | Transmission timeout in seconds, defaults to NULL. |
| wallet_path | Reserved for compatibility, has no practical meaning. |
| wallet_password | Reserved for compatibility, has no practical meaning. |
| secure_connection_before_smtp | Reserved for compatibility, has no practical meaning. |
| secure_host | Reserved for compatibility, has no practical meaning. |


Example

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

Sends the HELO command to the SMTP server to identify the client.

Before calling HELO, a TCP connection must be established (OPEN_CONNECTION).

| Parameter | Description |
| :--- | :--- |
| c | SMTP connection. |
| domain | Client domain name. |

### EHLO

```plsql
UTL_SMTP.EHLO(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   domain IN VARCHAR2)
 RETURN UTL_SMTP.REPLIES;
```

Sends the EHLO command to the SMTP server to obtain the list of server-supported extensions.

| Parameter | Description |
| :--- | :--- |
| c | SMTP connection. |
| domain | Client domain name. |

### MAIL

```plsql
UTL_SMTP.MAIL(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   sender IN VARCHAR2,
   parameters IN VARCHAR2 DEFAULT NULL);
```

Sends the MAIL command to the SMTP server to specify the sender.

Before calling MAIL, HELO or EHLO must be completed.

| Parameter | Description |
| :--- | :--- |
| c | SMTP connection. |
| sender | Sender email address in the format `<user@example.com>`. |
| parameters | Optional parameters such as SIZE. |

### RCPT

```plsql
UTL_SMTP.RCPT(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   recipient IN VARCHAR2,
   parameters IN VARCHAR2 DEFAULT NULL);
```

Sends the RCPT command to the SMTP server to specify the recipient.

Before calling RCPT, the MAIL command must be completed.

| Parameter | Description |
| :--- | :--- |
| c | SMTP connection. |
| recipient | Recipient email address in the format `<user@example.com>`. |
| parameters | Optional parameters such as NOTIFY. |

### DATA

```plsql
UTL_SMTP.DATA(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   body IN VARCHAR2)
 RETURN UTL_SMTP.REPLY;
```

Sends email content to the SMTP server.

Before calling DATA, the RCPT command must be completed.

| Parameter | Description |
| :--- | :--- |
| c | SMTP connection. |
| body | Email body content. |

### OPEN_DATA

```plsql
UTL_SMTP.OPEN_DATA(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION);
```

Opens data sending mode to prepare for streaming email content.

Before calling OPEN_DATA, the RCPT command must be completed.

### WRITE_DATA

```plsql
UTL_SMTP.WRITE_DATA(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   data IN VARCHAR2);
```

Writes email content in streaming sending mode.

Before calling WRITE_DATA, OPEN_DATA must be called.

| Parameter | Description |
| :--- | :--- |
| c | SMTP connection. |
| data | Email content. |

### WRITE_RAW_DATA

```plsql
UTL_SMTP.WRITE_RAW_DATA(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   data IN RAW);
```

Writes raw binary email content in streaming sending mode.

Before calling WRITE_RAW_DATA, OPEN_DATA must be called.

| Parameter | Description |
| :--- | :--- |
| c | SMTP connection. |
| data | Raw binary data. |

### CLOSE_DATA

```plsql
UTL_SMTP.CLOSE_DATA(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION)
 RETURN UTL_SMTP.REPLY;
```

Closes data sending mode and completes email sending.

Before calling CLOSE_DATA, OPEN_DATA must be called.

### NOOP

```plsql
UTL_SMTP.NOOP(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION);
```

Sends the NOOP command to the SMTP server for keeping the connection alive or testing the connection status.

### RSET

```plsql
UTL_SMTP.RSET(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION);
```

Sends the RSET command to the SMTP server to reset the current mail session.

### QUIT

```plsql
UTL_SMTP.QUIT(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION)
 RETURN UTL_SMTP.REPLY;
```

Sends the QUIT command to the SMTP server to close the connection.

### CLOSE_CONNECTION

```plsql
UTL_SMTP.CLOSE_CONNECTION(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION);
```

Closes the SMTP connection and releases resources.

### COMMAND

```plsql
UTL_SMTP.COMMAND(
   c IN OUT NOCOPY UTL_SMTP.CONNECTION,
   cmd IN VARCHAR2,
   arg IN VARCHAR2 DEFAULT NULL)
 RETURN UTL_SMTP.REPLY;
```

Sends a custom command to the SMTP server.

| Parameter | Description |
| :--- | :--- |
| c | SMTP connection. |
| cmd | SMTP command name. |
| arg | Command parameter. |

Example

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

## Usage Example

The following example demonstrates how to send a simple email using UTL_SMTP:

```sql
DECLARE
   c UTL_SMTP.CONNECTION;
BEGIN
   -- Establish connection
   c := UTL_SMTP.OPEN_CONNECTION('smtp.example.com', 25);

   -- Identify client
   UTL_SMTP.HELO(c, 'example.com');

   -- Specify sender
   UTL_SMTP.MAIL(c, '<sender@example.com>');

   -- Specify recipient
   UTL_SMTP.RCPT(c, '<recipient@example.com>');

   -- Send email content
   UTL_SMTP.DATA(c, 'From: sender@example.com' || UTL_TCP.CRLF ||
                   'To: recipient@example.com' || UTL_TCP.CRLF ||
                   'Subject: Test Mail' || UTL_TCP.CRLF ||
                   UTL_TCP.CRLF ||
                   'This is a test email.');

   -- Close connection
   UTL_SMTP.QUIT(c);
END;
/
```