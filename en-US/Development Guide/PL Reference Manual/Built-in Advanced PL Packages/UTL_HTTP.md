The UTL_HTTP package is a built-in package for HTTP client operations and providing a set of built-in stored procedures, constants, and data structures. UTL_HTTP package enabling users to retrieve data from or send data to an WEB server via HTTP requests.

When using the UTL_HTTP package, ensure network connectivity between the database server and the HTTP server.

Accessing HTTP servers from the database consumes network resources, which may impact database performance. Grant privileges to operational users by the SYS user after carefully evaluate the necessity.

```sql
GRANT EXECUTE ON UTL_HTTP TO user_name;
```

## Data Structures

The UTL_HTTP package uses UDT data structures as function parameters or return values.

### REQ TYPE

```plsql
TYPE REQ IS RECORD(
   url           VARCHAR(32767 byte),
   method        VARCHAR(64),
   http_version  VARCHAR(64));
```

The REQ is a UDT type body. All three parameters must be provided when using the constructor function. The parameter descriptions are as follows:

|Parameter |Description |
| :------------------- | :--------------------------------------------------------------------------------------|
| url      | The URL of the HTTP request |
| method   | Specify the HTTP request method. Supported methods include: <br>- GET, default value<br>- POST<br>- PUT<br>- PATCH<br>- DELETE                |
| http_version    | HTTP protocol version for the request, and support HTTP 1.0 and 1.1.           |

### RESP TYPE

```plsql
TYPE RESP IS RECORD(
   status_code          INTEGER,
   reason_phrase        VARCHAR(256),
   http_version         VARCHAR(64));
```

The RESP is a UDT type body used to represent an HTTP response. All three parameters must be provided when using the constructor function. The parameter descriptions are as follows:

|Parameter |Description |
| :------------------- | :--------------------------------------------------------------------------------------|
| status_code      | The HTTP response status code returned by the Web service, which is configured after the HTTP response is obtained by the GET_RESPONSE subprogram. |
| reason_phrase   | A summary of the HTTP response information returned by the web service.                |
| http_version    | HTTP protocol version for the request, and support HTTP 1.0 and 1.1.           |

## Constants

The UTL_HTTP system package defines a set of constants for use by UTL_HTTP advanced package related functions and subroutines. The table below provides detailed information.


|Name   |Value|Description         |
| ----------- |-----------|-----------|
| HTTP_VERSION_1_0          | 'HTTP/1.0' | HTTP 1.0                            |
| HTTP_VERSION_1_1          | 'HTTP/1.1' | HTTP 1.1                            |
| DEFAULT_HTTP_PORT         | 80         | Default TCP/IP port 80 for web server or proxy server    |
| DEFAULT_HTTPS_PORT        | 443        | Default TCP/IP port 443 for HTTPS web server  |
| HTTP_CONTINUE          | 100        | Client should continue its request. This temporary response is used to notify the client that the initial part of the request has been received and has not been rejected by the server. |
| HTTP_SWITCHING_PROTOCOLS       | 101        | The server understands and is willing to comply with the client's request issued through the Upgrade message header field to change the application protocol used on this connection. The server will immediately switch the protocol to that defined in the response Upgrade header field after the blank line ending the 101 response. |
| HTTP_OK            | 200        | Request successful. The information returned in the response depends on the method used in the request      |
| HTTP_CREATED       | 201        | The request has been fulfilled and resulted in a new resource being created.                |
| HTTP_ACCEPTED      | 202        | The request has been accepted for processing, but processing has not been completed. The request may eventually be processed or may not be processed, as it may be rejected during actual processing. |
| HTTP_NON_AUTHOR    | 203        | The meta-information returned in the entity header is not the definitive set provided by the source server, but rather collected from local or third-party copies. |
| HTTP_NO_CONTENT    | 204        | The server has completed the request but does not need to return an entity body, and may wish to return updated meta-information. |
| HTTP_RESET_CONT    | 205        | The server has completed the request, and the user agent should reset the document view that caused the request to be sent. The response must not contain an entity. |
| HTTP_PARTIAL_CONTENT       | 206        | The server has completed a partial GET request for the resource.       |
| HTTP_MULTIPLE_CHOICES      | 300        | The requested resource corresponds to any one of a set of representations, each with its own specific location, and proxy-driven negotiation information is being provided so that the user (or user agent) can select a preferred representation and redirect its request to that location. |
| HTTP_MOVED_PERMANENTLY         | 301        | The requested resource has been assigned a new permanent URI, and any future references to this resource should use one of the returned URIs. |
| HTTP_FOUND          | 302        | The requested resource resides temporarily under a different URI.              |
| HTTP_SEE_OTHER                 | 303        | The response to the request can be found under a different URI, and should be retrieved using the GET method on that resource. |
| HTTP_NOT_MODIFIED              | 304        | If the client performed a conditional GET request and access was allowed, but the document has not been modified, the server will respond with this status code. |
| HTTP_USE_PROXY                 | 305        | The requested resource must be accessed through the proxy specified by the Location field. The Location field gives the URI of the proxy. |
| HTTP_TEMPORARY_REDIRECT        | 307        | The requested resource resides temporarily under a different URI.         |
| HTTP_BAD_REQUEST          | 400        | The server cannot understand the request due to syntax errors.           |
| HTTP_UNAUTHORIZED        | 401        | The request requires user authentication. The client can repeat the request with a suitable Authorization header field. If the request already contains authorization credentials, the 401 response indicates that authorization has been refused for those credentials. |
| HTTP_PAYMENT_REQUIRED        | 402        | Not currently used                 |
| HTTP_FORBIDDEN       | 403        | The server understood the request, but refuses to execute it.         |
| HTTP_NOT_FOUND       | 404        | The server did not find anything matching the requested URI.          |
| HTTP_NOT_ACCEPTABLE    | 406        | The resource identified by the request is only capable of generating response entities with content characteristics that are unacceptable according to the Accept headers sent in the request. |
| HTTP_PROXY_AUTH_REQUIRED     | 407        | This code is similar to 401 (Unauthorized), but indicates that the client must first authenticate itself with the proxy. |
| HTTP_REQUEST_TIME_OUT     | 408        | The client did not produce a request within the time that the server was prepared to wait.     |
| HTTP_CONFLICT           | 409        | The request could not be completed due to a conflict with the current state of the resource.       |
| HTTP_GONE         | 410        | The requested resource is no longer available on the server and no forwarding address is known.         |
| HTTP_LENGTH_REQUIRED      | 411        | The server refuses to accept the request without a defined `Content-Length`.     |
| HTTP_PRECONDITION_FAILED       | 412        | One or more preconditions given in the request header fields evaluated to false when tested on the server |
| HTTP_REQUEST_ENTITY_TOO_LARGE  | 413        | The server refuses to process the request because the request entity is larger than the server is willing or able to process. |
| HTTP_REQUEST_URI_TOO_LARGE     | 414        | The server refuses to service the request because the request URI is longer than the server is willing to interpret. |
| HTTP_UNSUPPORTED_MEDIA_TYPE    | 415        | The server refuses to service the request because the format of the requested entity is not supported by the requested resource and the request method. |
| HTTP_REQ_RANGE_NOT_SATISFIABLE | 416        | If the request includes a Range request header field, and none of the range specifier values in this field overlap with the current extent of the selected resource, and the request does not include an If-Range request header field, then the server returns a response with this status code. |
| HTTP_EXPECTATION_FAILED        | 417        | The server cannot meet the expectations given in the Expect request header field, or, if the server is a proxy, the server has clear evidence that the next-hop server cannot satisfy the request. |
| HTTP_NOT_IMPLEMENTED           | 501        | The server does not support the functionality required to fulfill the request.                             |
| HTTP_BAD_GATEWAY               | 502        | The server, while acting as a gateway or proxy, received an invalid response from the upstream server it accessed in attempting to fulfill the request |
| HTTP_SERVICE_UNAVAILABLE       | 503        | The server is currently unable to handle the request due to temporary overloading or maintenance of the server.         |
| HTTP_GATEWAY_TIME_OUT          | 504        | The server, while acting as a gateway or proxy, did not receive a timely response from the upstream server specified by the URI (e.g., HTTP, FTP, LDAP) or other auxiliary server (e.g., DNS) that it needed to access in attempting to complete the request. |
| HTTP_VERSION_NOT_SUPPORTED     | 505        | The server does not support or refuses to support the HTTP protocol version used in the request message         |

## Subprograms Summary of UTL_HTTP

### BEGIN_REQUEST Function

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

The BEGIN_REQUEST function initializes an HTTP request and generates the request body structure to be used in subsequent interface functions.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| URL      | The URL of the HTTP request |
| Method   | Specify the HTTP request method. Supported methods include: <br>- GET, default value<br>- POST<br>- PUT<br>- PATCH<br>- DELETE                |
| HTTP_VERSION    | HTTP protocol version for the request, and support HTTP 1.0 and 1.1.           |
| REQUEST_CONTEXT    | Request body context key-value pair.            |
| HTTP_HOST   | Hostname for the HTTP request. The default value is NULL and the hostname will be derived from the URL.     |

### END_REQUEST Procedure

```PLSQL
UTL_HTTP.END_REQUEST
 (
   r IN OUT UTL_HTTP.REQ);
```

Terminate the HTTP request and release HTTP resources. For requests with persistent connections enabled, the connection can be terminated via END_REQUEST.

### END_RESPONSE Procedure

```PLSQL
UTL_HTTP.END_RESPONSE
 (
   RESP IN OUT UTL_HTTP.RESP);
```

Terminate the HTTP request and release HTTP resources. For requests with persistent connections enabled, the connection cannot be terminated via END_RESPONSE.

### GET_BUFFER_SIZE Function

```PLSQL
UTL_HTTP.GET_BUFFER_SIZE
 (
   REQ IN  UTL_HTTP.REQ,
   SIZE OUT INTEGER)
  RETURN BUFFER_SIZE;
```

Get the available buffer size for HTTP send/receive data

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| REQ   | The HTTP request               |
| SIZE  | The buffer size for the HTTP send/receive data          |

### GET_DETAILED_SQLERRM Function

```PLSQL
UTL_HTTP.GET_DETAILED_SQLERRM ()
RETURN DETAILED_SQLERRM;
```

Get detailed return information for HTTP request errors in VARCHAR type.

### GET_RESPONSE Function

```PLSQL
UTL_HTTP.GET_RESPONSE
 (
   REQ IN  UTL_HTTP.REQ,
   RETURN_INFO_RESPONSE IN BOOLEAN DEFAULT FALSE)
  RETURN UTL_HTTP.RESP;
```

Retrieve the HTTP response body, used to determine the status code and other metadata from the HTTP request.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| REQ    | The HTTP request               |
| RETURN_INFO_RESPONSE    | Return `1**` information or not. Syntax compatibility only, no actual effect.       |

### GET_TRANSFER_TIMEOUT Procedure

```PLSQL
UTL_HTTP.GET_TRANSFER_TIMEOUT
 (
   TIMEOUT OUT INTEGER DEFAULT NULL
 );
```

Get the global HTTP request timeout duration

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| TIMEOUT     | Timeout duration in seconds (default: 60 seconds)          |

### READ_LINE Procedure

```PLSQL
UTL_HTTP.READ_LINE
 (
   RESP IN OUT UTL_HTTP.RESP,
   DATA OUT VARCHAR,
   REMOVE_CRLF IN BOOL DEFAULT FALSE
);
```

Read response body data line by line in text format to buffer.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| RESP   | The HTTP response   |
| DATA    | The HTTP response body data           |
| REMOVE_CRLF     | Whether to strip newline characters         |

### READ_RAW Procedure

```PLSQL
UTL_HTTP.READ_RAW
 (
   RESP IN OUT UTL_HTTP.RESP,
   DATA OUT RAW,
   LEN IN INTEGER DEFAULT NULL);
```

Read response body data in RAW format to buffer

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| RESP   | The HTTP response   |
| DATA    | The HTTP response body data           |
| LEN       | Byte length of data to read (default: NULL)          |

### READ_TEXT Procedure

```PLSQL
UTL_HTTP.READ_TEXT
 (
   RESP IN OUT UTL_HTTP.RESP,
   DATA OUT VARCHAR,
   LEN IN INTEGER DEFAULT NULL
);
```

Read response body data in text format to buffer.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| RESP   | The HTTP response   |
| DATA    | The HTTP response body data           |
| LEN       | Byte length of data to read (default: NULL)          |

### WRITE_LINE Procedure

```PLSQL
UTL_HTTP.WRITE_LINE
 (
   REQ IN OUT UTL_HTTP.REQ,
   DATA  IN VARCHAR);
```

Write a line of data to the HTTP request body in text format.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| REQ   | The HTTP request               |
| DATA     | The HTTP request body data           |

### WRITE_RAW Procedure

```PLSQL
UTL_HTTP.WRITE_RAW

 (
   REQ IN OUT UTL_HTTP.REQ,
   DATA  IN RAW);
```

Write HTTP request body data in RAW format.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| REQ   | The HTTP request               |
| DATA     | The HTTP request body data           |

### WRITE_TEXT Procedure

```PLSQL
UTL_HTTP.WRITE_TEXT
 (
   REQ IN OUT UTL_HTTP.REQ,
   DATA  IN VARCHAR);
```

Write HTTP request body information in text format, commonly used with HTTP methods such as PUT, POST and DELETE.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| REQ   | The HTTP request               |
| DATA     | The HTTP request body data           |

### SET_AUTHENTICATION Procedure

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

Set HTTP authentication credentials.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| REQ         | The HTTP request               |
| USERNAME    | Username for authentication.                   |
| PASSWORD    | Password for authentication.         |
| SCHEME      | HTTP authentication scheme, `basic` as default.  |
| FOR_PROXY   | Flag indicating whether the credentials are used for proxy server authentication. This parameter is configured for syntax compatibility only, and have no actual effect. |

### SET_BODY_CHARSET Procedure

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

This procedure is configuring the character set for the request and response bodies, Primarily used to define the character encoding for the BODY of requests and responses.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| CHARSET    | Configure the character set for the entire HTTP request                    |
| REQ   | The HTTP request               |
| RESP   | The HTTP response   |

### SET_BUFFER_SIZE Procedure

```PLSQL
UTL_HTTP.SET_BUFFER_SIZE
 (
   REQ IN  UTL_HTTP.REQ,
   SIZE IN INTEGER);
```

Set the available buffer size for HTTP send/receive data, and the default value is 2MB.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| REQ   | The HTTP request               |
| SIZE  | The buffer size for the HTTP send/receive data          |

### SET_DETAILED_EXCP_SUPPORT Procedure

```PLSQL
UTL_HTTP.SET_DETAILED_EXCP_SUPPORT
 (
   ENABLE IN BOOL DEFAULT FALSE);
```

Asks UTL_HTTP to return a detailed message directly if set to TRUE; otherwise FALSE.

### SET_HEADER Procedure

```PLSQL
UTL_HTTP.SET_HEADER
 (
   REQ IN OUT UTL_HTTP.REQ,
   NAME IN VARCHAR,
   VALUE  IN VARCHAR DEFAULT NULL
   );
```

This procedure sets an HTTP request header.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| REQ    | The HTTP request               |
| NAME    | Name of the HTTP request              |
| VALUE    | Value of the HTTP request          |

### SET_PERSISTENT_CONN_SUPPORT Procedure

```PLSQL
UTL_HTTP.SET_PERSISTENT_CONN_SUPPORT
 ( ENABLE IN BOOL DEFAULT FALSE,
   MAX_CONNS IN INTEGER DEFAULT 0,
   REQ IN OUT UTL_HTTP.REQ);
```

Enable HTTP persistent connection. This function is syntax compatibility only, and have no actual effect.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| ENABLE    | Enable persistent connection               |
| MAX_CONNS   | Maximum number of connections              |
| REQ IN OUT   | HTTP request requiring a persistent connection           |

### SET_TRANSFER_TIMEOUT Procedure

```PLSQL
UTL_HTTP.SET_TRANSFER_TIMEOUT
 (
   REQ IN OUT UTL_HTTP.REQ,
   TIMEOUT IN INTEGER DEFAULT 60
);
```

Set the global HTTP request timeout duration or configure the timeout duration for an individual request.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| REQ   | The HTTP request               |
| TIMEOUT     | Timeout duration in seconds (default: 60 seconds)          |

## Example

Example 1: Common usage example

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

Example 2: After obtaining the response, the response is not closed. If the number of repeated executions exceeds the set limit, further responses will be blocked

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

## Exception Handling

|Name   |Error Code  |Description         |
| ----------- | ----------|-----------|
| init_failed            | 6857    |    The UTL_HTTP pkg initialization failed        |
| request_failed         | 6858    |    The HTTP request failed                       |
| bad_argument           | 6859    |    A bad argument was passed to an API           |
| bad_url                | 6860    |    The URL is bad                                |
| protocol_error         | 6861    |    A HTTP protocol error occurred                |
| unknown_scheme         | 6862    |    The scheme of the URL is unknown              |
| header_not_found       | 6863    |    The HTTP header is not found                  |
| end_of_body            | 6864    |    The end of response body is reached           |
| illegal_call           | 6865    |    The API call is illegal at this stage         |
| http_redirect_error    | 6866    |    HTTP redirect exception during request processing               |
| http_client_error      | 6867    |    When using GET_RESPONSE function, returning response status codes in the 4xx range indicates client errors; or when using begin_request function to send HTTPS requests through HTTP proxy, receiving status codes in the 4xx range from the HTTP proxy.               |
| http_server_error      | 6868    |    When using GET_RESPONSE function, returning response status codes in the 5xx range indicates server errors; or when using begin_request function to send HTTPS requests through HTTP proxy, receiving status codes in the 5xx range from the HTTP proxy.             |
| too_many_requests      | 6869    |    Too many open requests or responses           |
| partial_multibyte_char | 6870    |    Incomplete character read, partial multibyte characters found at the end of response body          |
| transfer_timeout       | 6871    |    Transfer time-out occurred                    |
| network_access_denied  | 6872    |    Network access denied                         |

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
 
   -- Print the response status
   l_status_code := l_http_resp.STATUS_CODE;
   DBMS_OUTPUT.PUT_LINE('status code: ' || l_status_code);
 
   -- Get the response body
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