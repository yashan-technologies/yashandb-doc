UTL_HTTP包是用于HTTP客户端操作的内置包，包含了一组子程序、常量及数据结构。用户可以通过UTL_HTTP从WEB服务器上获取数据，或者向HTTP服务器发送数据。

使用UTL_HTTP包时需保证数据库服务器到HTTP服务器网络需连通。

因数据库服务器访问HTTP服务器需要消耗网络资源，对数据库运行性能可能产生影响，请谨慎评估后通过sys用户给操作用户赋权。

```sql
GRANT EXECUTE ON UTL_HTTP TO user_name;
```

## 数据结构

UTL_HTTP包中使用自定义类型数据结构作为函数参数或返回值。

### REQ TYPE

```plsql
TYPE REQ IS RECORD(
   url           VARCHAR(32767 byte),
   method        VARCHAR(64),
   http_version  VARCHAR(64));
```

REQ是一种UDT类型主体，使用构造函数时必须输入全部3种参数，参数描述如下：

|  参数| 描述|
| :------------------- | :--------------------------------------------------------------------------------------|
| url      | 待访问的URL地址。 |
| method   | 指定HTTP请求方法。当前支持如下方法，请根据HTTP服务端开放的端口进行配置，不配置时通过GET方法进行进行连接请求。<br>- GET<br>- POST<br>- PUT<br>- PATCH<br>- DELETE        |
| http_version    | 发送请求的HTTP协议版本，当前支持HTTP1.0/1.1。 |

### RESP TYPE

```plsql
TYPE RESP IS RECORD(
   status_code          INTEGER,
   reason_phrase        VARCHAR(256),
   http_version         VARCHAR(64));
```

RESP是一种UDT类型主体，该类型用于表达HTTP响应信息。使用构造函数时必须输入全部3种参数，参数描述如下：

|  参数| 描述|
| :------------------- | :--------------------------------------------------------------------------------------|
| status_code      | Web服务返回的HTTP响应状态码，在GET_RESPONSE子程序获取HTTP响应后被配置。 |
| reason_phrase   | Web 服务返回的HTTP响应信息的摘要。        |
| http_version    | 发送请求的HTTP协议版本，当前支持HTTP1.0/1.1。 |

## 预定义常量

UTL_HTTP系统包定义了一组常量，供UTL_HTTP高级包相关函数及子程序调用。下表为详细信息。


| 名称| 值| 描述|
| ----------- |-----------|-----------|
| HTTP_VERSION_1_0          | 'HTTP/1.0' | HTTP 1.0                            |
| HTTP_VERSION_1_1          | 'HTTP/1.1' | HTTP 1.1                            |
| DEFAULT_HTTP_PORT         | 80         | Web服务器或代理服务器侦听的默认TCP/IP 80端口    |
| DEFAULT_HTTPS_PORT        | 443        | HTTPS Web服务器监听的默认TCP/IP 443端口  |
| HTTP_CONTINUE          | 100        | 客户端应继续其请求。此临时响应用于通知客户端，请求的初始部分已收到，并且尚未被服务器拒绝。 |
| HTTP_SWITCHING_PROTOCOLS       | 101        | 服务器理解并愿意遵循客户端通过升级消息头字段发出的请求，更改此连接上使用的应用协议。服务器将在结束101响应的空行之后立即将协议切换到响应升级头字段中定义的协议。 |
| HTTP_OK            | 200        | 请求成功。响应返回的信息取决于请求中使用的方法      |
| HTTP_CREATED       | 201        | 请求已得到满足并导致新资源被创建。                |
| HTTP_ACCEPTED      | 202        | 请求已被接受处理，但尚未完成。该请求最终可能会被处理，也可能不会被处理，因为实际处理时可能会被拒绝。 |
| HTTP_NON_AUTHOR    | 203        | 实体头中返回的元信息不是源服务器提供的最终集合，而是从本地或第三方副本收集的。 |
| HTTP_NO_CONTENT    | 204        | 服务器已完成请求但不需要返回实体主体，并且可能希望返回更新的元信息。 |
| HTTP_RESET_CONT    | 205        | 服务器已完成请求，用户代理应重置导致发送请求的文档视图。响应中不得包含实体。 |
| HTTP_PARTIAL_CONTENT       | 206        | 服务器已完成对资源的部分GET请求。       |
| HTTP_MULTIPLE_CHOICES      | 300        | 所请求的资源对应于一组表示中的任何一个，每个表示都有其自己的特定位置，并且正在提供代理驱动的协商信息，以便用户（或用户代理）可以选择首选表示并将其请求重定向到该位置。 |
| HTTP_MOVED_PERMANENTLY         | 301        | 所请求的资源已被分配一个新的永久URI，并且对该资源的任何未来引用都应使用返回的URI之一。 |
| HTTP_FOUND          | 302        | 请求的资源暂时驻留在不同的URI下。              |
| HTTP_SEE_OTHER                 | 303        | 可以在不同的URI下找到对请求的响应，并且应该使用该资源上的 GET 方法进行检索。 |
| HTTP_NOT_MODIFIED              | 304        | 如果客户端执行了有条件的GET请求并且允许访问，但文档尚未修改，则服务器将使用此状态代码进行响应。 |
| HTTP_USE_PROXY                 | 305        | 请求的资源必须通过Location字段指定的代理进行访问。Location 字段给出了代理的 URI。 |
| HTTP_TEMPORARY_REDIRECT        | 307        | 请求的资源暂时驻留在不同的URI下。         |
| HTTP_BAD_REQUEST          | 400        | 由于语法错误，服务器无法理解该请求。           |
| HTTP_UNAUTHORIZED        | 401        | 该请求需要用户身份验证。客户端可以使用合适的Authorization标头字段重复该请求。如果请求中已包含授权凭证，则401响应表示针对这些凭证的授权已被拒绝。 |
| HTTP_PAYMENT_REQUIRED        | 402        | 暂未使用                 |
| HTTP_FORBIDDEN       | 403        | 服务器理解了该请求，但拒绝执行。         |
| HTTP_NOT_FOUND       | 404        | 服务器未找到任何与请求 URI 匹配的内容。          |
| HTTP_NOT_ACCEPTABLE    | 406        | 请求所标识的资源仅能够生成具有根据请求中发送的接受标头不可接受的内容特征的响应实体。 |
| HTTP_PROXY_AUTH_REQUIRED     | 407        | 此代码类似于401（未授权），但表示客户端必须首先向代理进行身份验证。 |
| HTTP_REQUEST_TIME_OUT     | 408        | 客户端未在服务器准备等待的时间内发出请求。     |
| HTTP_CONFLICT           | 409        | 由于与资源的当前状态冲突，无法完成请求。       |
| HTTP_GONE         | 410        | 所请求的资源在服务器上不再可用，并且不知道转发地址。         |
| HTTP_LENGTH_REQUIRED      | 411        | 服务器拒绝接受未定义的请求`Content-Length`。     |
| HTTP_PRECONDITION_FAILED       | 412        | 在服务器上测试时，请求标头字段中给出的一个或多个先决条件被评估为 false |
| HTTP_REQUEST_ENTITY_TOO_LARGE  | 413        | 服务器拒绝处理请求，因为请求实体大于服务器愿意或能够处理的大小。 |
| HTTP_REQUEST_URI_TOO_LARGE     | 414        | 服务器拒绝为该请求提供服务，因为请求URI的长度超出了服务器愿意解释的长度。 |
| HTTP_UNSUPPORTED_MEDIA_TYPE    | 415        | 服务器拒绝为该请求提供服务，因为请求的实体的格式不受请求的资源和请求的方法支持。 |
| HTTP_REQ_RANGE_NOT_SATISFIABLE | 416        | 如果请求包含Range请求标头字段，并且此字段中的任何范围说明符值均未与所选资源的当前范围重叠，并且请求不包含If-Range请求标头字段，则服务器返回带有此状态代码的响应。 |
| HTTP_EXPECTATION_FAILED        | 417        | 该服务器无法满足 Expect 请求标头字段中给出的期望，或者，如果服务器是代理，则服务器有明确的证据表明下一跳服务器无法满足该请求。 |
| HTTP_NOT_IMPLEMENTED           | 501        | 服务器不支持满足请求所需的功能。                             |
| HTTP_BAD_GATEWAY               | 502        | 服务器在充当网关或代理时，在尝试完成请求时从其访问的上游服务器收到了无效响应 |
| HTTP_SERVICE_UNAVAILABLE       | 503        | 由于服务器暂时过载或维护，服务器目前无法处理该请求。         |
| HTTP_GATEWAY_TIME_OUT          | 504        | 该服务器在充当网关或代理时，未从URI（例如HTTP、FTP、LDAP）指定的上游服务器或尝试完成请求时需要访问的其他辅助服务器（例如DNS）收到及时响应。 |
| HTTP_VERSION_NOT_SUPPORTED     | 505        | 服务器不支持或拒绝支持请求消息中使用的HTTP协议版本         |

## 子程序说明

### BEGIN_REQUEST

```PLSQL
UTL_HTTP.BEGIN_REQUEST
 (
   URL IN  VARCHAR,
   Method IN VARCHAR DEFAULT 'GET',
   HTTP_VERSION  IN VARCHAR DEFAULT NULL,
   REQUEST_CONTEXT IN REQUEST_CONTEXT_KEY DEFAULT NULL,
   HTTP_HOST IN VARCHAR DEFAULT NULL) 
  RETURN UTL_HTTP.REQ;
```

BEGIN_REQUEST函数将HTTP请求初始化，生成HTTP请求体结构将用于后续接口函数中。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| URL      | 待访问的URL地址。 |
| Method   | 指定HTTP请求方法。当前支持如下方法，请根据HTTP服务端开放的端口进行配置，不配置时通过GET方法进行进行连接请求。<br>- GET<br>- POST<br>- PUT<br>- PATCH<br>- DELETE        |
| HTTP_VERSION    | 发送请求的HTTP协议版本，当前支持HTTP1.0/1.1。 |
| REQUEST_CONTEXT    | 请求体上下文key值。 |
| HTTP_HOST   | HTTP请求的主机名，默认值为NULL，为NULL时使用URL作为主机名。|

### END_REQUEST

```PLSQL
UTL_HTTP.END_REQUEST
 (
   r IN OUT UTL_HTTP.REQ);
```

结束HTTP请求，清理HTTP资源。对于设置了持久化连接的请求，可以通过END_REQUEST关闭连接。

### END_RESPONSE

```PLSQL
UTL_HTTP.END_RESPONSE
 (
   RESP IN OUT UTL_HTTP.RESP);
```

结束HTTP请求，清理HTTP资源。对于设置了持久化连接的请求，无法通过END_RESPONSE关闭连接。

### GET_BUFFER_SIZE

```PLSQL
UTL_HTTP.GET_BUFFER_SIZE
 (
   REQ IN  UTL_HTTP.REQ,
   SIZE OUT INTEGER)
  RETURN BUFFER_SIZE;
```

获取HTTP收发数据可用的缓存大小。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| REQ   | HTTP请求头结构               |
| SIZE  | HTTP收发数据实际可用的缓存大小 |

### GET_DETAILED_SQLERRM

```PLSQL
UTL_HTTP.GET_DETAILED_SQLERRM ()
RETURN DETAILED_SQLERRM;
```

获取HTTP请求错误的详细返回信息，数据类型为VARCHAR。

### GET_RESPONSE

```PLSQL
UTL_HTTP.GET_RESPONSE
 (
   REQ IN  UTL_HTTP.REQ,
   RETURN_INFO_RESPONSE IN BOOLEAN DEFAULT FALSE)
  RETURN UTL_HTTP.RESP;
```

获取HTTP的响应体，用于判断HTTP请求的状态码等信息。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| REQ    | HTTP请求头结构               |
| RETURN_INFO_RESPONSE    | 是否返回`1**`状态码。仅语法兼容，无实际含义。 |

### GET_TRANSFER_TIMEOUT

```PLSQL
UTL_HTTP.GET_TRANSFER_TIMEOUT
 (
   TIMEOUT OUT INTEGER DEFAULT NULL
 );
```

获取全局HTTP请求超时时间。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| TIMEOUT     | 超时时间（秒）  |

### READ_LINE

```PLSQL
UTL_HTTP.READ_LINE
 (
   RESP IN OUT UTL_HTTP.RESP,
   DATA OUT VARCHAR,
   REMOVE_CRLF IN BOOL DEFAULT FALSE
);
```

按行从HTTP响应体中读取数据。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| RESP   | HTTP的响应结构 |
| DATA    | HTTP响应体数据 |
| REMOVE_CRLF     | 是否删除换行符       |

### READ_RAW

```PLSQL
UTL_HTTP.READ_RAW
 (
   RESP IN OUT UTL_HTTP.RESP,
   DATA OUT RAW,
   LEN IN INTEGER DEFAULT NULL);
```

按RAW类型读HTTP响应体数据。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| RESP   | HTTP的响应结构 |
| DATA    | HTTP响应体数据 |
| LEN       | 读取数据的字节长度，默认长度为NULL       |

### READ_TEXT

```PLSQL
UTL_HTTP.READ_TEXT
 (
   RESP IN OUT UTL_HTTP.RESP,
   DATA OUT VARCHAR,
   LEN IN INTEGER DEFAULT NULL
);
```

从HTTP响应缓冲区中读取Body信息。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| RESP   | HTTP的响应结构 |
| DATA    | HTTP响应体数据 |
| LEN       | 读取数据的字节长度，默认长度为NULL       |

### WRITE_LINE

```PLSQL
UTL_HTTP.WRITE_LINE
 (
   REQ IN OUT UTL_HTTP.REQ,
   DATA  IN VARCHAR);
```

以文本格式写入一行数据到HTTP请求体。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| REQ   | HTTP请求头结构               |
| DATA     | HTTP请求体数据  |

### WRITE_RAW

```PLSQL
UTL_HTTP.WRITE_RAW

 (
   REQ IN OUT UTL_HTTP.REQ,
   DATA  IN RAW);
```

按RAW类型写入HTTP请求体数据。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| REQ   | HTTP请求头结构               |
| DATA     | HTTP请求体数据  |

### WRITE_TEXT

```PLSQL
UTL_HTTP.WRITE_TEXT
 (
   REQ IN OUT UTL_HTTP.REQ,
   DATA  IN VARCHAR);
```

按文本格式写入HTTP请求体信息，常结合PUT/POST/DELETE等HTTP方法使用。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| REQ   | HTTP请求头结构               |
| DATA     | HTTP请求体数据  |

### SET_AUTHENTICATION

```PLSQL
UTL_HTTP.SET_AUTHENTICATION

 (
   REQ IN OUT UTL_HTTP.REQ,
   USERNAME IN VARCHAR,
   PASSWORD IN VARCHAR,
   SCHEME IN VARCHAR DEFAULT 'Basic',
   FOR_PROXY IN BOOL DEFAULT FALSE
);
```

设置HTTP认证信息。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| REQ         | HTTP请求头结构               |
| USERNAME    | 用于认证的用户名    |
| PASSWORD    | 用于认证的密码          |
| SCHEME      | 认证模式， 默认为Basic   |
| FOR_PROXY   | 是否用于代理服务器的认证。仅语法兼容，无实际含义。  |

### SET_BODY_CHARSET

```PLSQL
UTL_HTTP.SET_BODY_CHARSET
 ( CHARSET IN VARCHAR DEFAULT NULL);
```

```PLSQL
UTL_HTTP.SET_BODY_CHARSET
 ( CHARSET IN VARCHAR DEFAULT NULL,
   REQ IN OUT UTL_HTTP.REQ);
```

```PLSQL
UTL_HTTP.SET_BODY_CHARSET
 ( CHARSET IN VARCHAR DEFAULT NULL,
   RESP IN OUT UTL_HTTP.RESP);
```

设置HTTP请求体、响应体的字符集信息，主要用于请求和响应的BODY字符集。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| CHARSET    | 设置整个HTTP请求的字符集信息    |
| REQ   | HTTP请求头结构               |
| RESP   | HTTP的响应结构 |

### SET_BUFFER_SIZE

```PLSQL
UTL_HTTP.SET_BUFFER_SIZE
 (
   REQ IN  UTL_HTTP.REQ,
   SIZE IN INTEGER);
```

设置HTTP收发数据可用的缓存大小，默认为2M。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| REQ   | HTTP请求头结构               |
| SIZE  | 设置HTTP收发数据可用的缓存大小 |

### SET_DETAILED_EXCP_SUPPORT

```PLSQL
UTL_HTTP.SET_DETAILED_EXCP_SUPPORT
 (
   ENABLE IN BOOL DEFAULT FALSE);
```

设置是否返回详细的错误信息，默认FALSE为不返回。

### SET_HEADER

```PLSQL
UTL_HTTP.SET_HEADER
 (
   REQ IN OUT UTL_HTTP.REQ,
   NAME IN VARCHAR,
   VALUE  IN VARCHAR DEFAULT NULL
   );
```

设置HTTP请求头信息。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| REQ    | HTTP请求头结构               |
| NAME    | HTTP请求头的名称                 |
| VALUE    | HTTP请求头的值                    |

### SET_PERSISTENT_CONN_SUPPORT

```PLSQL
UTL_HTTP.SET_PERSISTENT_CONN_SUPPORT
 ( ENABLE IN BOOL DEFAULT FALSE,
   MAX_CONNS IN INTEGER DEFAULT 0,
   REQ IN OUT UTL_HTTP.REQ);
```

设置HTTP持久化连接。该函数当前仅为语法兼容，无实际含义。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| ENABLE    | 是否持久化连接    |
| MAX_CONNS   | 最大连接数    |
| REQ IN OUT   | 需要建立持久化连接的HTTP请求体             |

### SET_TRANSFER_TIMEOUT

```PLSQL
UTL_HTTP.SET_TRANSFER_TIMEOUT
 (
   REQ IN OUT UTL_HTTP.REQ,
   TIMEOUT IN INTEGER DEFAULT 60
);
```

设置全局HTTP请求超时时间或设置单个请求的超时时间。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| REQ   | HTTP请求头结构               |
| TIMEOUT     | 超时时间（秒），默认为60秒。  |

## 示例

示例一：常用用法示例

```plsql
DECLARE
  req  UTL_HTTP.REQ;
  resp UTL_HTTP.RESP;
  l_url VARCHAR2(100) := 'http://localhost:5000/api/get';
  l_mothod VARCHAR2(100) := 'get';
  l_buffer VARCHAR2(4000);
BEGIN
  -- Begin a HTTP request
  req := UTL_HTTP.BEGIN_REQUEST(l_url, l_mothod, 'HTTP/1.1');
  UTL_HTTP.SET_BODY_CHARSET(req, 'UTF8'); 
  UTL_HTTP.set_header(req, 'Accept', 'application/json');

  -- Get the HTTP response
  resp := UTL_HTTP.GET_RESPONSE(req);
  -- Print the status of the HTTP response
  DBMS_OUTPUT.put_line(l_mothod|| 'The status of the HTTP response is: ' || resp.status_code || ' ' || resp.reason_phrase);
  
  -- Confirm the response
  UTL_HTTP.READ_TEXT(resp, l_buffer, 4000);
  DBMS_OUTPUT.PUT_LINE(' Response: ' || l_buffer);
  
  -- Close the response
  UTL_HTTP.END_RESPONSE(resp);
  DBMS_OUTPUT.put_line(l_mothod || 'The HTTP request processing is completed.');

EXCEPTION
  WHEN OTHERS THEN
    DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
    UTL_HTTP.END_RESPONSE(resp);
END;
/

--result

get The status of the HTTP response is: 200 OK
 Response: {
  "data": {
    "secret": "Top Secret Data"
  },
  "message": "Access granted"
}

getThe HTTP request processing is completed.

PL/SQL Succeed.
```

示例二：获取响应之后不关闭响应，如重复执行打开的次数超过设定值（5次）则无法继续响应。

```plsql
DECLARE
  req  UTL_HTTP.REQ;
  resp UTL_HTTP.RESP;
  l_url VARCHAR2(100) := 'http://localhost:5000/api/get1';
  l_mothod VARCHAR2(100) := 'get';
  l_api_key VARCHAR2(100) := 'secret123';
  l_buffer VARCHAR2(32767);
BEGIN
  -- Begin a HTTP request
  req := UTL_HTTP.BEGIN_REQUEST(l_url, l_mothod, 'HTTP/1.1');
  UTL_HTTP.SET_BODY_CHARSET(req, 'UTF8');
  UTL_HTTP.set_header(req, 'X-API-Key', l_api_key);  -- Set API Key
  UTL_HTTP.set_header(req, 'Accept', 'application/json');

  -- Get the HTTP response
  resp := UTL_HTTP.GET_RESPONSE(req);
  -- Print the status of the HTTP response
  DBMS_OUTPUT.put_line(l_mothod|| 'The status of the HTTP response is: ' || resp.status_code || ' ' || resp.reason_phrase);

  -- Confirm the response
  UTL_HTTP.READ_TEXT(resp, l_buffer, 32767);
  DBMS_OUTPUT.PUT_LINE(' Response: ' || l_buffer);

  DBMS_OUTPUT.put_line(l_mothod || 'The HTTP request processing is completed.');

EXCEPTION
  WHEN OTHERS THEN
    DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
    UTL_HTTP.END_RESPONSE(resp);
END;
  28   29   30   31   32   33 /
Error: YAS-06869 too many open requests or responses

PL/SQL Succeed.
```

## 异常说明

| 名称| 错误码| 描述|
| ----------- | ----------|-----------|
| init_failed            | 6857    |    HTTP请求初始化异常        |
| request_failed         | 6858    |    请求执行失败                       |
| bad_argument           | 6859    |    传递给接口的参数不正确          |
| bad_url                | 6860    |    请求的URL格式不正确                          |
| protocol_error         | 6861    |    与Web服务器通信时发生HTTP协议错误                |
| unknown_scheme         | 6862    |    请求的URL的方案未知              |
| header_not_found       | 6863    |    未找到请求头                  |
| end_of_body            | 6864    |    HTTP响应主体已结束           |
| illegal_call           | 6865    |    当前HTTP请求状态是一个非法的UTL_HTTP调用         |
| http_redirect_error    | 6866    |    HTTP请求过程中重定向异常               |
| http_client_error      | 6867    |    使用GET_RESPONSE函数时，返回4xx范围内的响应状态码表示客户端发生错误；或者在使用begin_request函数通过HTTP代理发出HTTPS请求时收到了HTTP代理返回了4xx范围内的状态码。               |
| http_server_error      | 6868    |    使用GET_RESPONSE函数时，返回5xx范围内的响应状态码表示客户端发生错误；或者在使用begin_request函数通过HTTP代理发出HTTPS请求时收到了HTTP代理返回了5xx范围内的状态码。             |
| too_many_requests      | 6869    |    开放的HTTP请求或响应过多           |
| partial_multibyte_char | 6870    |    未读取完整字符，在响应主体末尾发现部分多字节字符          |
| transfer_timeout       | 6871    |    未读取任何数据并且发生读取超时                    |
| network_access_denied  | 6872    |    远程网络主机拒绝访问                         |

```plsql
DECLARE
   l_url        VARCHAR2(2000);       
   l_response   CLOB;                 
   l_http_req   UTL_HTTP.REQ;         
   l_http_resp  UTL_HTTP.RESP;        
   l_buffer     VARCHAR2(32767);      
   l_status_code NUMBER;              
   l_line        NUMBER;              
   l_remove_crlf BOOLEAN := null;     
 BEGIN
   DBMS_OUTPUT.PUT_LINE('=== TEST 1.1: Basic http request read line 3 ===');
   l_url := 'http://127.0.0.1:8080/text/charset4';    
   l_http_req := UTL_HTTP.BEGIN_REQUEST(l_url, 'GET');
   l_http_resp := UTL_HTTP.GET_RESPONSE(l_http_req);
   UTL_HTTP.SET_BODY_CHARSET(l_http_resp, 'GB18030');
 
   -- 获取状态码
   l_status_code := l_http_resp.STATUS_CODE;
   DBMS_OUTPUT.PUT_LINE('status code: ' || l_status_code);
 
   -- 读取响应内容
   l_line := 1;
   l_response := EMPTY_CLOB;
   LOOP
     l_remove_crlf := CASE
       WHEN l_remove_crlf IS NULL THEN true
       WHEN l_remove_crlf THEN false
       ELSE null
     END;
     UTL_HTTP.READ_LINE(l_http_resp, l_buffer, l_remove_crlf);
     DBMS_OUTPUT.PUT_LINE(l_line || ': ' || l_buffer);
     l_line := l_line + 1;
     l_response := l_response || l_buffer;
   END LOOP;
 
   UTL_HTTP.END_RESPONSE(l_http_resp);
 EXCEPTION
   WHEN UTL_HTTP.PARTIAL_MULTIBYTE_CHAR THEN
     DBMS_OUTPUT.PUT_LINE('PARTIAL_MULTIBYTE_CHAR: ' || SQLERRM);
     DBMS_OUTPUT.PUT_LINE('Response data(head 500): ' || SUBSTR(l_response, 1, 500));
     UTL_HTTP.END_RESPONSE(l_http_resp);
 END;
 /

 
=== TEST 1.1: Basic http request read line 3 ===
status code: 200
PARTIAL_MULTIBYTE_CHAR: YAS-06870 partial multibyte character
Response data(head 500): 

PL/SQL Succeed.
```