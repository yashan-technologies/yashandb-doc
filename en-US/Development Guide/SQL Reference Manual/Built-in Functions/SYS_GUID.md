```ebnf
sys_guid = SYS_GUID "("")".
```

The SYS_GUID function returns a 16-byte globally unique number, with a return type of RAW.
> **Note**:
>
> Modifying the system time may lead to non-unique IDs being generated.
> 
***Example***

```sql
SELECT SYS_GUID() res FROM DUAL;

RES                        
--------------------------------- 
01016C6484F5932806001A0001000000 

1 row fetched.
```
