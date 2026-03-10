The datetime type is a data type on the time dimension, commonly used for data extraction and analysis on the time dimension. It can be further divided into:

*   DateTime Data Type (Date&Time Data Type): Represents a specific date or moment in time. This includes the data types of Date (DATE), Time (TIME), SCN (TIMESTAMP), Time Zone (TIMESTAMP WITH LOCAL TIME ZONE, TIMESTAMP WITH TIME ZONE).
*   Interval Data Type: Represents the length of the interval between two dates or moments in time. This includes two data types: Year to Month Interval (INTERVAL YEAR TO MONTH) and Day to Second (INTERVAL DAY TO SECOND).

Date Type (Date Data Type)
--------------------

The date type stores logical calendar information that is independent of time zones, including year, month, day (hours, minutes, seconds).
The date format can be set using the set command.

### Storage Attributes

|Type |Byte Length |Value Range |Precision |
| --- | --- | --- | --- |
| DATE | 8   | 0001-01-01 00:00:00 ~ 9999-12-31 23:59:59 | Seconds |

### Data Format

The default format for DATE type is YYYY-MM-DD, which can also be specified in a similar format of YYYY-MM-DD \[HH\[24\]\]\[:MI\]\[:SS\], where each character represents:

*   YYYY: Year represented as a four-digit number, value range \[0001,9999\].
*   MM: Month represented as one or two digits, value range \[1,12\].
*   DD: Day represented as one or two digits, value range \[1,31\].
*   HH\[24\]: Hour represented as one or two digits, 24 indicates a 24-hour format, value range \[0,23\].
*   MI: Minute represented as one or two digits, value range \[0,59\].
*   SS: Second represented as one or two digits, value range \[0,59\].

***Example***

```sql
CREATE TABLE date_date(C1 DATE);
INSERT INTO date_date VALUES ('2020-01-01');
INSERT INTO date_date VALUES ('2020-1-1');
COMMIT;
  
SELECT C1 FROM date_date;
C1
--------------------------------
2020-01-01 00:00:00
2020-01-01 00:00:00
```

Time Type (Time Data Type)
--------------------

The time type represents a time within a day, including hours, minutes, seconds, and microseconds.

### Storage Attributes

|Type |Byte Length |Value Range |Precision |
| --- | --- | --- | --- |
| TIME | 8   | 0:0:0.000000 ~ 23:59:59.999999 | Microseconds  |

### Data Format

Specified in a format similar to HH\[24\]\[:MI\]\[:SS\]\[.FF\], where each character represents:

*   HH\[24\]: Hour represented as one or two digits, 24 indicates a 24-hour format, value range \[0,23\].
*   MI: Minute represented as one or two digits, value range \[0,59\].
*   SS: Second represented as one or two digits, value range \[0,59\].
*   FF: Microseconds represented as one to six digits, value range \[0,999999\].

When performing string conversion on TIME type, all parts of the standard format above can be omitted from lower positions upwards, with omitted parts padded with 0.

***Example***

```sql
CREATE TABLE date_time(C1 TIME);
INSERT INTO date_time VALUES ('23:59:59.999999');
INSERT INTO date_time VALUES ('2:5:5');
INSERT INTO date_time VALUES ('1:2');
COMMIT;
   
SELECT C1 FROM date_time ;
C1
--------------------
23:59:59.999999
02:05:05.000000
01:02:00.000000
```

SCN Type (Timestamp Data Type)
--------------------------

SCN type is the most comprehensive date time type, containing year, month, day, hour, minute, second, and microsecond information.

### Storage Attributes

|Type |Byte Length |Value Range |Precision |
| --- | --- | --- | --- |
| TIMESTAMP | 8   | 1-1-1 00:00:00.000000 ~ 9999-12-31 23:59:59.999999 | Microseconds  |

### Data Format

Specified in a format similar to YYYY-MM-DD \[HH\[24\]\]\[:MI\]\[:SS\]\[.FF\], where each character represents:

*   YYYY: Year represented as a four-digit number, value range \[0001,9999\].
*   MM: Month represented as one or two digits, value range \[1,12\].
*   DD: Day represented as one or two digits, value range \[1,31\].
*   HH\[24\]: Hour represented as one or two digits, 24 indicates a 24-hour format, value range \[0,23\].
*   MI: Minute represented as one or two digits, value range \[0,59\].
*   SS: Second represented as one or two digits, value range \[0,59\].
*   FF: Microseconds represented as one to six digits, value range \[0,999999\].

> **Note**: 
>
> Special handling of microseconds:
>
> - When defining a timestamp type, the microsecond precision can be specified in the range of 0 to 9, but the maximum actual storage precision is 6.
> - When the microsecond format is `'FF'`, the system accepts microsecond inputs ranging from 0 to 9 digits, but will pad or round them to the specified precision.

***Example***

```sql
CREATE TABLE date_timestamp (C0 TIMESTAMP(0), C1 TIMESTAMP, C2 TIMESTAMP(3), C3 TIMESTAMP(9));
INSERT INTO date_timestamp VALUES (
    '2020-01-01 12:30:30.123456789','2020-01-01 12:30:30.123456789',
    '2020-01-01 1:1:1.123456789','2020-01-01 1:1:1.123456789');
INSERT INTO date_timestamp VALUES (
    '2021-1-1 12:30:30.123456','2021-1-1 12:30:30.123456',
    '2021-1-1 12:30:30.123456','2021-1-1 12:30:30.123456');
INSERT INTO date_timestamp VALUES ('2022-1-1','2022-1-1','2022-1-1','2022-1-1');
COMMIT;
  
SELECT C0,C1,C2,C3 FROM date_timestamp;
C0                            C1                            C2                            C3
----------------------------- ----------------------------- ----------------------------- -----------------------------
2020-01-01 12:30:30.          2020-01-01 12:30:30.123457    2020-01-01 01:01:01.123       2020-01-01 01:01:01.123457
2021-01-01 12:30:30.          2021-01-01 12:30:30.123456    2021-01-01 12:30:30.123       2021-01-01 12:30:30.123456
2022-01-01 00:00:00.          2022-01-01 00:00:00.000000    2022-01-01 00:00:00.000       2022-01-01 00:00:00.000000
```

Local Time Zone Type (Timestamp With Local Time Zone)
---------------------------------------

The Timestamp With Local Time Zone type (LTZ type) is similar to SCN type and contains information about year, month, day, hour, minute, second, and microsecond.

This data type is applicable only to HEAP tables.

### Storage Attributes

|Type |Byte Length |Value Range |Precision |
| --- | --- | --- | --- |
| TIMESTAMP WITH LOCAL TIME ZONE | 8   | 1-1-1 00:00:00.000000 ~ 9999-12-31 23:59:59.999999 | Microseconds  |

### Data Format

Specified in a format similar to YYYY-MM-DD \[HH\[24\]\]\[:MI\]\[:SS\]\[.FF\], where each character represents:

*   YYYY: Year represented as a four-digit number, value range \[0001,9999\].
*   MM: Month represented as one or two digits, value range \[1,12\].
*   DD: Day represented as one or two digits, value range \[1,31\].
*   HH\[24\]: Hour represented as one or two digits, 24 indicates a 24-hour format, value range \[0,23\].
*   MI: Minute represented as one or two digits, value range \[0,59\].
*   SS: Second represented as one or two digits, value range \[0,59\].
*   FF: Microseconds represented as one to six digits, value range \[0,999999\].

> **Note**: 
>
> Special handling of microseconds:
>
> - When defining a LTZ type, the microsecond precision can be specified in the range of 0 to 9, but the maximum actual storage precision is 6.
> - When the microsecond format is `'FF'`, the system accepts microsecond inputs ranging from 0 to 9 digits, but will pad or round them to the specified precision.
>
> Differences from TIMESTAMP type:
>
> - The TIMESTAMP type returns the current SCN of the system, whereas the LTZ type returns the SCN corresponding to the current session time zone.

***Example*** for Heap tables

```sql
CREATE TABLE timestamp_ltz (C1 TIMESTAMP WITH LOCAL TIME ZONE);
INSERT INTO timestamp_ltz VALUES ('2020-01-01 12:30:30.123456');
INSERT INTO timestamp_ltz VALUES ('2020-1-1 12:30:30.123456');
INSERT INTO timestamp_ltz VALUES ('2020-1-1');
COMMIT;
  
SELECT C1 FROM timestamp_ltz;
C1
----------------------------------------------------------------
2020-01-01 12:30:30.123456
2020-01-01 12:30:30.123456
2020-01-01 00:00:00.000000
```

Time Zone Type (Timestamp With Time Zone)
--------------------------

The Timestamp With Time Zone type (TZ type) consists of SCN + time zone, containing information about year, month, day, hour, minute, second, microsecond, and time zone information.

This data type is applicable only to HEAP tables.

### Storage Attributes

|Component |Byte Length |Value Range |Precision |
| --- | --- | --- | --- |
| SCN   | 8   | 1-1-1 00:00:00.000000 ~ 9999-12-31 23:59:59.999999 | Microseconds  |
| Time Zone | 2   | -15:59 ~ 15:59 | Minutes  |

### Data Format

Specified in a format similar to YYYY-MM-DD \[HH\[24\]\]\[:MI\]\[:SS\]\[.FF\] \[TZH:\]\[TZM\], where each character represents:

*   YYYY: Year represented as a four-digit number, value range \[0001,9999\].
*   MM: Month represented as one or two digits, value range \[1,12\].
*   DD: Day represented as one or two digits, value range \[1,31\].
*   HH\[24\]: Hour represented as one or two digits, 24 indicates a 24-hour format, value range \[0,23\].
*   MI: Minute represented as one or two digits, value range \[0,59\].
*   SS: Second represented as one or two digits, value range \[0,59\].
*   FF: Microseconds represented as one to six digits, value range \[0,999999\].
*   TZH: Time zone hour represented as two digits, value range \[-15,15\].
*   TZM: Time zone minutes represented as two digits, value range \[0,59\].

> **Note**: 
>
> Special handling of microseconds:
>
> - When defining a TZ type, the microsecond precision can be specified in the range of 0 to 9, but the maximum actual storage precision is 6.
> - When the microsecond format is `'FF'`, the system accepts microsecond inputs ranging from 0 to 9 digits, but will pad or round them to the specified precision.
>
> Differences from TIMESTAMP WITH LOCAL TIME ZONE type:
>
> - The TZ type includes SCN and time zone, while the LTZ type includes only SCN.
> - The TZ type uses the format specifier TIMESTAMP_TZ_FORMAT, while the LTZ type uses the TIMESTAMP_FORMAT specifier.

### Time Zone Conversion

The AT TIME ZONE syntax can be used to modify/specify the time zone of data:

- Modify the time zone for TZ type data (data type remains unchanged).
- Specify the time zone for TIMESTAMP, TIMESTAMP LTZ type data (will convert to TZ type, similar in effect to [FROM_TZ](../Built-in Functions/FROM_TZ) and [TO_TIMESTAMP_TZ](../Built-in Functions/TO_TIMESTAMP_TZ) function).

The syntax is as follows:

`expr1 AT TIME ZONE expr2`

**expr1**

The original data to modify/specify the time zone; must be data or constant NULL of TIMESTAMP, TIMESTAMP LTZ, or TIMESTAMP TZ type.

**expr2**

Specifies the time zone information; must be a string type or constant NULL, in the format 'TZH:TZM', with a value range of -15:59 ~ 15:59.

***Example*** for Heap tables

```sql
CREATE TABLE timestamp_tz (C1 TIMESTAMP WITH TIME ZONE);
INSERT INTO timestamp_tz VALUES ('2020-01-01 12:30:30.123456 +8:00');
INSERT INTO timestamp_tz VALUES ('2020-1-1 12:30:30.123456 +7:00');
COMMIT;
  
SELECT C1 FROM timestamp_tz;
C1
----------------------------------------------------------------
2020-01-01 12:30:30.123456 +08:00
2020-01-01 12:30:30.123456 +07:00

SELECT TIMESTAMP'2012-1-1 1:2:3.123' at TIME zone '8:00' FROM dual;

TIMESTAMP'2012-1-11:2:3.123'ATTIMEZONE'8:00'
----------------------------------------------------------------
2012-01-01 01:02:03.123000 +08:00
```

Year to Month Interval Type (Interval Year To Month Data Type)
-----------------------------------------

The Year to Month Interval type represents an interval of time measured in years and months.

### Storage Attributes

|Type |Byte Length |Value Range |Precision |
| --- | --- | --- | --- |
| INTERVAL YEAR TO MONTH | 4   | -178000000-00 ~ 178000000-00 | Months   |

### Definition Format

The definition format for Year to Month Interval type is INTERVAL YEAR \[(year_precision)\] TO MONTH. Here, year_precision indicates the precision of years, with a value range of \[0,9\], defaulting to 2 when omitted.

> **Note**: 
>
> Special handling of 00-00:
>
> When within the maximum year precision of 9 and the maximum month precision of 2, any number of 0s input will be treated as '00', regardless of the precision specified in the definition format.

### Data Format

Specified in the standard format of `YYYY-MM` or `INTERVAL 'YYYY' YEAR(MM)`:

*   YYYY: Year, valid value range -178000000 to 178000000.
*   MM: Month, valid value range 0 to 11.

***Example***

```sql
CREATE TABLE date_iytm (no INT, c1 INTERVAL YEAR(4) TO MONTH);
INSERT INTO date_iytm VALUES(1,'1000-10');
INSERT INTO date_iytm VALUES(2,'1000-0');
INSERT INTO date_iytm VALUES(3,'000000000-11');
INSERT INTO date_iytm VALUES(4,INTERVAL '1000' YEAR(9));
INSERT INTO date_iytm VALUES(5,INTERVAL '11' MONTH);
COMMIT;
  
SELECT no,c1 FROM date_iytm ORDER BY no;
          NO C1            
------------ ---------------
           1 +1000-10     
           2 +1000-00     
           3 +00-11       
           4 +1000-00     
           5 +00-11
```

Day to Second Interval Type (Interval Day To Second Data Type)
-----------------------------------------

The Day to Second Interval type represents an interval of time measured in days, hours, minutes, seconds, and microseconds.

### Storage Attributes

|Type |Byte Length |Value Range |Precision |
| --- | --- | --- | --- |
| INTERVAL DAY TO SECOND | 8   | -100000000 00:00:00.000000<br>~ 100000000 00:00:00.000000 | Microseconds  |

### Definition Format

The definition format for Day to Second Interval type is `INTERVAL DAY[(day_precision)] TO SECOND[(fractional_seconds_precision)]`, where:

*   day_precision: Indicates the precision of days, syntax supports \[0,9\], with actual value range \[0,8\].
*   fractional_seconds_precision: Indicates the precision of microseconds, with value range \[0,9\].

When `(day_precision)` is omitted, the system will take the default value of 2; when `(fractional_seconds_precision)` is omitted, the system will take the default value of 6.

### Data Format

The standard format for this type is `DD HH:MI:SS[.FF]` or `INTERVAL 'DD' DAY(day_precision)` or `INTERVAL 'HH' HOUR` or `INTERVAL 'MI:SS.DDDDDD' MINUTE TO SECOND(fractional_seconds_precision)`, where each character represents:

*   DD: Day, valid range \[-100000000, 100000000\].
*   HH: Hour, valid range \[0, 23\].
*   MI: Minute, valid range \[0, 59\].
*   SS: Second, valid range \[0, 59\].
*   FF: Microseconds, valid range \[0, 999999\].

> **Note**: 
>
> Special handling of microseconds:
>
> - The precision for microseconds can be defined as a range between 0-9 when defining the data type, but the actual precision value stored will be 6.
> - The system can accept microsecond inputs in the range of 0-9 digits but will pad or round to the defined precision (if the defined precision is greater than 6, it will be treated as 6).

***Example***

```sql
CREATE TABLE date_idts(no INT, c1 INTERVAL DAY(9) TO SECOND(9));
INSERT INTO date_idts VALUES(1,'50 10:30:59.999999');
INSERT INTO date_idts VALUES(2,'50 00:00:59.999999');
INSERT INTO date_idts VALUES(3,'00 10:30:00.000000');
INSERT INTO date_idts VALUES(4,'50 00:00:00.000000');
INSERT INTO date_idts VALUES(5,INTERVAL '50' DAY(6));
INSERT INTO date_idts VALUES(6,INTERVAL '10' HOUR);
INSERT INTO date_idts VALUES(7,INTERVAL '30:59.9' MINUTE TO SECOND(6));
INSERT INTO date_idts VALUES(8,INTERVAL '30:59.9999999' MINUTE TO SECOND(6));
INSERT INTO date_idts VALUES(9,INTERVAL '30:59.9999990' MINUTE TO SECOND(6));
INSERT INTO date_idts VALUES(10,INTERVAL '30:59.9999990' MINUTE TO SECOND(3));
INSERT INTO date_idts VALUES(11,INTERVAL '30:59.999999567' MINUTE TO SECOND(8));
COMMIT;
   
SELECT no,c1 FROM date_idts ORDER BY no;
          NO C1                             
------------ --------------------------------
           1 +50 10:30:59.999999           
           2 +50 00:00:59.999999           
           3 +00 10:30:00.000000           
           4 +50 00:00:00.000000           
           5 +50 00:00:00.000000           
           6 +00 10:00:00.000000           
           7 +00 00:30:59.900000           
           8 +00 00:31:00.000000           
           9 +00 00:30:59.999999           
          10 +00 00:31:00.000000             
          11 +00 00:31:00.000000 
```
