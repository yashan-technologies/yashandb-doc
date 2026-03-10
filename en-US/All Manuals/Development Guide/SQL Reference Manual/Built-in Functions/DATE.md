```ebnf+diagram
date::= DATE "("expr ["," format] ")" 
```

The DATE function has 1 or 2 parameters. When there is only one [expr](../General SQL Syntax/expr) parameter, the DATE function converts the value of expr to DATE type according to the format specified by the configuration parameter DATE_FORMAT; when there are two parameters, the DATE function converts the value of expr according to the second parameter format.

**expr**

The data type of the expression expr must be DATE/TIMESTAMP/TIME, and the content will return the correct result regardless of whether it conforms to the date_format or format.

When the value of expr is NULL, the function returns NULL.

**format**

The format supports Chinese year, month, and day, and must enclose Chinese characters in double quotes, while Chinese characters in expr do not need double quotes.

Specify the format for conversion, which includes the following character combinations:

* Year
    * Year digits: `YYYY`, `Y`, `YY`, `YYY`
* Month
    * Month digits: `MM`
    * Full month name: `MONTH`
    * Abbreviated month name: `MON`
* Day
    * Day digits: `DD`
* Date description
    * Full name of a day of the week (SUNDAY-SATURDAY): `DAY`
    * Day of the week digits (1-7): `D`
    * Day of the year (1-366): `DDD`
* Hour
    * 24-hour format: `HH24`
    * 12-hour format: `HH`, `HH12`
* Minute
    * Minute digits: `MI`
* Second
    * Second digits: `SS`
*   Connector characters: `:`, `-`, `/`, `.`, `,`, `;`, `\`, `_`, ` `
    * `:`, `-`, `/`, `.`, `,`, `;`, `\`, `_`: Eight connector characters match one by one
    * `  `: Ignore all spaces, spaces do not participate in matching

If format is not specified, the system will convert according to the default format specified for the DATE type. If the content of expr does not match the default format, a format matching error is returned.

When the value of format is NULL, the function returns NULL.

***Example***

```sql
SELECT DATE( 'January 15, 1989, 11:00 A.M.', 'Month dd, YY, HH:MI A.M.' ) res FROM DUAL;
RES                               
--------------------------------
1989-01-15 11:00:00    
 
SELECT DATE( '1989/2/23', 'YYYY/MM/DD HH24:MI:SS' ) res FROM DUAL;
RES                             
--------------------------------
1989-02-23 00:00:00

SELECT DATE( '1989/2/23' ) res FROM DUAL;
RES
-------------------------------- 
1989-02-23                      

    
SELECT DATE( '1989/2/23', '') res FROM DUAL;
RES
--------------------------------

SELECT DATE( '1989/2/23', null) res FROM DUAL;
RES
--------------------------------

-- When the default DATE format 'YYYY-MM-DD' does not match, an error is returned
SELECT DATE( '1989//2/23' ) res FROM DUAL;
[1:16]YAS-00008 type convert error : literal does not match format string
```
