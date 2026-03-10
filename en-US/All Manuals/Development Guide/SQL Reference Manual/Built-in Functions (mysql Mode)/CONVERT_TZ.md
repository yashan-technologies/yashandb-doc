```ebnf+diagram
convert_tz::= CONVERT_TZ "(" dt "," from_tz "," to_tz ")" 
```

The CONVERT_TZ function converts the TIMESTAMP type value specified by the dt parameter from the timezone specified by the from_tz parameter to the timezone specified by the to_tz parameter and returns the resulting TIMESTAMP type value. The dt parameter can be any data type that can be converted to TIMESTAMP.

If the from_tz or to_tz parameters have illegal values, the CONVERT_TZ function returns NULL.

If the result of the conversion exceeds the legal values for TIMESTAMP type, the CONVERT_TZ function returns the value of the dt parameter.

The legal values for from_tz and to_tz parameters are as follows, case insensitive:

- 'SYSTEM', specifies that the database timezone is the same as the operating system timezone.

- A string describing the offset from UTC time, formatted as `[H]H:MM`, with a prefix `+` or `-`, such as '+10:00' or '-7:30'. Leading zeros can be omitted when the hour value is less than 10. Legal values range from '-13:59' to '+14:00'.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT CONVERT_TZ('2025-8-1 23:12:56', '+3:00', '+8:30') res FROM DUAL;

res               
----------------- 
2025-08-02 04:42:56.000000
```
