The OWA_UTIL package provides a set of built-in subprograms for performing operations such as retrieving information about the calling PL/SQL procedure.

## WHO_CALLED_ME

```plsql
OWA_UTIL.WHO_CALLED_ME(
   owner          OUT      VARCHAR2,
   name           OUT      VARCHAR2,
   lineno         OUT      NUMBER,
   caller_t       OUT      VARCHAR2);
```

The WHO_CALLED_ME procedure returns information about the PL procedure unit that called it through output parameters.
|Parameter |Description |
| :------------------------- | :--------------------------------- |
| owner             | The owner of the procedure unit.      |
| name              | The name of the procedure unit.       |
| lineno            | The line number of the call within the procedure unit. |
| caller_t         | The type of the calling procedure unit. |

***Example***

```plsql
create or replace procedure proc1 as
owner    varchar(200);
name     varchar(200);
lineno   number;
caller_t varchar(200);
begin
owa_util.who_called_me(owner, name, lineno, caller_t);
dbms_output.put_line('owener: ' || owner || ' name: ' || name || ' lineno: ' || lineno || ' caller_t: '|| caller_t);
end;
/

begin
proc1();
end;
/
owener:  name:  lineno: 2 caller_t: ANONYMOUS BLOCK

PL/SQL Succeed.
```
