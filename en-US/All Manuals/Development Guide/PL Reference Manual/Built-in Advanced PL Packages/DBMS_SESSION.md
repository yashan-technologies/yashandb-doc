The DBMS_SESSION package provides a set of built-in stored procedures for managing session context information and session details.

The advanced package is not suitable for ISC Distributed Cluster Deployment.

## SET\_IDENTIFIER

```plsql
DBMS_SESSION.SET_IDENTIFIER (
	client_identifier   IN   VARCHAR);
```

SET_IDENTIFIER is a stored procedure that sets the client identifier for the user session by passing in the client_identifier, which, once set, displays the corresponding CLIENT_IDENTIFIER field in V$SESSION.

|Parameter |Description |
| :------------------ | :--------|
| client_identifier   | A string provided by the user to set the client identifier, with a maximum length of 64 bytes; exceeding 64 bytes will cause an error. |

***Example*** for Standalone Deployment

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

CLEAR_IDENTIFIER is a stored procedure used to clear the user-defined identifier.

No parameters.

***Example*** for Standalone Deployment

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

FREE_UNUSED_USER_MEMORY is a stored procedure used solely for compatibility, with no actual impact on the system.

No parameters.

***Example*** for Standalone Deployment

```plsql
BEGIN
    DBMS_SESSION.FREE_UNUSED_USER_MEMORY;
END;
/
```

## SET\_CONTEXT

```plsql
DBMS_SESSION.SET_CONTEXT (
   namespace VARCHAR(64),
   attribute VARCHAR(128),
   value     VARCHAR(4000),
   username  VARCHAR(128),
   client_id VARCHAR(64) );
```

This stored procedure is used to set application context information, and the procedure can only be executed within the package associated with the CONTEXT object.

For session program context, username and client_id are not effective.

|Parameter |Description |
| :--------| :--------------------------------- |
| namespace   | Context name                            |
| attribute   | Context attribute name                  |
| value       | Context value                            |
| username    | Username corresponding to context information, default is NULL |
| client_id   | Client ID corresponding to context information, default is NULL |

***Example*** for Standalone/YAC/Distributed Cluster Deployment
```plsql
-- Create context object
CREATE CONTEXT ctx1 USING set_context_pkg;

-- Create package associated with context object
CREATE OR REPLACE PACKAGE set_context_pkg IS 
   PROCEDURE set_attr(name IN VARCHAR2,attr IN VARCHAR2, value IN VARCHAR2);
END; 
/

CREATE OR REPLACE PACKAGE BODY set_context_pkg IS
   PROCEDURE set_attr(name IN VARCHAR2,attr IN VARCHAR2, value IN VARCHAR2) 
   IS 
   BEGIN
    DBMS_SESSION.SET_CONTEXT(name, attr, value);
   END;
 END;
/

-- Execute set context stored procedure
exec set_context_pkg.set_attr('ctx1', 'attr1', 'aaa');
```

## CLEAR\_CONTEXT

```plsql
DBMS_SESSION.CLEAR_CONTEXT(
    namespace  VARCHAR(64),
    client_identifier VARCHAR(64), 
    attribute  VARCHAR(128));
```

This stored procedure is used to clear the information corresponding to the set context attribute, and the procedure can only be executed within the package associated with the CONTEXT object.

- For session program context, client_id is not effective.
- If the attribute parameter is empty, all information in the namespace context will be cleared.

|Parameter |Description |
| :---------------- | :--------------------------------- |
| namespace            | Context name                            |
| client_identifier    | Client ID corresponding to context information, default is NULL |
| attribute            | Context attribute name                  |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Create context object
CREATE CONTEXT ctx1 USING set_context_pkg;

-- Create package associated with context object
CREATE OR REPLACE PACKAGE set_context_pkg IS 
   PROCEDURE clear_attr(name IN VARCHAR2, attr IN VARCHAR2);
END; 
/

CREATE OR REPLACE PACKAGE BODY set_context_pkg IS
   PROCEDURE clear_attr(name IN VARCHAR2,attr IN VARCHAR2) 
   IS 
   BEGIN
    DBMS_SESSION.CLEAR_CONTEXT(name, attr);
   END;
 END;
/

-- Execute clear context stored procedure
exec set_context_pkg.clear_attr('ctx1', 'attr1');
```

## CLEAR\_ALL\_CONTEXT 

```plsql
DBMS_SESSION.CLEAR_ALL_CONTEXT(namespace  VARCHAR(64));
```

This stored procedure is used to clear all information corresponding to the context name, and the procedure can only be executed within the package associated with the CONTEXT object.

|Parameter |Description |
| :--------- | :----------- |
| namespace   | Context name  |

***Example*** for Standalone/YAC/Distributed Cluster Deployment
```plsql
-- Create context object
CREATE CONTEXT ctx1 USING set_context_pkg;

-- Create package associated with context object
CREATE OR REPLACE PACKAGE set_context_pkg IS 
   PROCEDURE clear_all_attr(name IN VARCHAR2);
END; 
/

CREATE OR REPLACE PACKAGE BODY set_context_pkg IS
   PROCEDURE clear_all_attr(name IN VARCHAR2) 
   IS 
   BEGIN
    DBMS_SESSION.CLEAR_ALL_CONTEXT(name);
   END;
 END;
/

-- Execute clear all context stored procedure
exec set_context_pkg.clear_all_attr('ctx1');
```
