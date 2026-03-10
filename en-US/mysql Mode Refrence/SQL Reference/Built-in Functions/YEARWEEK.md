```ebnf+diagram
yearweek::= YEARWEEK "("date ["," mode] ")"
```

The YEARWEEK function returns the year and week number of the calendar week that the specified date falls into. The usage of the mode parameter is the same as in the [WEEK](WEEK) function, but the return value range of the YEARWEEK function is always 1-53.

If the mode parameter is not specified, the default value of mode is 0. The default value for mode in the YEARWEEK function is not affected by the DEFAULT_WEEK_FORMAT system variable.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT YEARWEEK('2025-1-1', 2) res FROM DUAL;

res               
----------------- 
202452
```
