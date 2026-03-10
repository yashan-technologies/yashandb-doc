```ebnf+diagram
current_date::= CURRENT_DATE
```
CURRENT_DATE is used to get the current date in the session's time zone, and it returns the value in the Gregorian calendar as a DATE data type.

The return value of CURRENT_DATE may vary depending on the time zone configuration (via the [TIME_ZONE](../../All Manuals/参考手册/配置参数.html#time_zone) parameter).

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER SESSION SET DATE_FORMAT='yyyy-mm-dd hh24:mi:ss';

SHOW PARAMETER TIME_ZONE

NAME                                   VALUE
-------------------------------------- -----------------------------------------
TIME_ZONE                              +08:00

SELECT CURRENT_DATE,SYSDATE,SESSIONTIMEZONE FROM DUAL;

CURRENT_DATE                                     SYSDATE                                          SESSIONTIMEZONE
------------------------------------------------ ------------------------------------------------ ----------------------------------------------------------------
2025-08-14 15:16:24                              2025-08-14 15:16:24                              +08:00

-- Adjust the time zone
ALTER SESSION SET TIME_ZONE = '-07:00';

-- Retrieve CURRENT_DATE again
SELECT CURRENT_DATE,SYSDATE,SESSIONTIMEZONE FROM DUAL;

CURRENT_DATE                                     SYSDATE                                          SESSIONTIMEZONE
------------------------------------------------ ------------------------------------------------ ----------------------------------------------------------------
2025-08-14 00:16:38                              2025-08-14 15:16:38                              -07:00
```