```ebnf
week = WEEK "("date ["," mode] ")".
```

The WEEK function returns the week number of the calendar week in which the specified date falls. The mode parameter is used to specify whether the first day of the week is Monday or Sunday, with return value ranges from 0-53 or 1-53 and how to determine the first week of a year.

If the mode parameter is not specified, the default value is the DEFAULT_WEEK_FORMAT system variable. Please refer to [System Variables in mysql mode](../../System Variables/List of System Variables In mysql Mode).

The values and effects of the mode parameter are as follows:

|mode value |First day of the week |Return value |Condition for the first week of the specified year |
| --- | --- | --- | --- |
| 0 | Sunday | 0-53 | The first week that contains a Sunday |
| 1 | Monday | 0-53 | The first week that contains at least 4 days of the specified year |
| 2 | Sunday | 1-53 | The first week that contains a Sunday |
| 3 | Monday | 1-53 | The first week that contains at least 4 days of the specified year |
| 4 | Sunday | 0-53 | The first week that contains at least 4 days of the specified year |
| 5 | Monday | 0-53 | The first week that contains a Monday |
| 6 | Sunday | 1-53 | The first week that contains at least 4 days of the specified year |
| 7 | Monday | 1-53 | The first week that contains a Monday |

When the mode parameter value is 1, 3, 4, or 6, the week number conforms to ISO 8601:1988, which means:

- If the week containing January 1 has at least 4 days in the new year, then that week is the first week;

- Otherwise, that week is the last week of the previous year, and the following week is the first week.

When the mode parameter value is 0, 1, 4, or 5, the return value of the WEEK function always corresponds to the year specified by the date parameter. If the date specified is from the last week of the previous year, the WEEK function returns 0 instead of 52.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT WEEK('2025-1-1') res FROM DUAL;

res               
----------------- 
0

SELECT WEEK('2025-1-1', 2) res FROM DUAL;

res               
----------------- 
52
```
