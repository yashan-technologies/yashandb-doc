When writing SQL statements in applications supported by YashanDB JDBC, date-time variables can be written in the following format: 

```sql
-- The following d/s/ts are case-insensitive.

-- Date
{d 'yyyy-mm-dd'}
{d 'yyyymmdd'} 
{d 'yyyy/mm/dd'} 
-- Time
{t 'hh:mm:ss'}
-- Timestamp
{ts 'yyyy-mm-dd hh:mm:ss.fff'}
{ts 'yyyy/mm/dd hh:mm:ss.fff'}
```

And before passing them to the server for execution, they will be escaped to the date-time format acceptable by the YashanDB server.

### Date Variable Escaping 

For date type variables, YashanDB JDBC escapes the string in quotes as a parameter of the TO_DATE function. For example, `select {d'2020-02-01'} from dual` is escaped to `select TO_DATE ('2020-02-01', 'YYYY-MM-DD') from dual`; `select {d'20200201'} from dual` is escaped to `select TO_DATE ('20200201', 'YYYY-MM-DD') from dual`. Please note that the escaped date format is always `YYYY-MM-DD`. 

When the variable is a bind parameter, the rule is to escape, for example, `select {d ?} from dual` to `select TO_DATE (?, 'YYYY-MM-DD') from dual`. 

In addition, consistent with the JDBC standard syntax escape format specification, the following writing style is also supported and escaped according to the above rules:

```sql
select {d'2020-02-01'} from dual
select {d'2020-02-01'}/*This is a comment*/ from dual
select { d   /*This is a comment*/  '20200201'} from dual
```

### Time Variable Escaping 

For time type variables, YashanDB JDBC pads the string in quotes with a date (1970-01-01) and escapes it as a parameter of the TO_TIMESTAMP function. For example, `select {t'01:99:01'} from dual` is escaped to `select TO_TIMESTAMP('1970-01-01 01:99:01', 'YYYY-MM-DD HH24:MI:SS.FF') from dual`. Please note that the timestamp format is always `YYYY-MM-DD HH24:MI:SS.FF`. 

When the variable is a bind parameter, the rule is to escape, for example, `select {t ?} from dual` to `select TO_TIMESTAMP('1970-01-01 '||?, 'YYYY-MM-DD') from dual`. 

In addition, consistent with the JDBC standard syntax escape format specification, the following writing style is also supported and escaped according to the above rules:

```sql
select {t'01:9:01'} from dual
select {t'01:9:01'}/*This is a comment*/ from dual
select { t   /*This is a comment*/  '01:9:01'} from dual
```

### Timestamp Variable Escaping 

For timestamp type variables, YashanDB JDBC escapes the string in quotes as a parameter of the TO_TIMESTAMP function. For example, `select {ts '2020-02-01 01:09:01'} from dual` is escaped to `select TO_TIMESTAMP('2020-02-01 01:09:01', 'YYYY-MM-DD HH24:MI:SS.FF') from dual`; `select {ts '2020/02/01 01:09:01.444'} from dual` is escaped to `select TO_TIMESTAMP('2020/02/01 01:09:01.444', 'YYYY-MM-DD HH24:MI:SS.FF') from dual`. Please note that the escaped timestamp format is always `YYYY-MM-DD HH24:MI:SS.FF`. 

When the variable is a bind parameter, the rule is to escape, for example, `select {ts ?} from dual` to `select TO_TIMESTAMP(?, 'YYYY-MM-DD HH24:MI:SS.FF') from dual`. 

In addition, consistent with the JDBC standard syntax escape format specification, the following writing style is also supported and escaped according to the above rules:

```sql
select {ts'2020-02-01 01:99:01'} from dual
select {ts'2020-02-01 01:99:01'}/*This is a comment*/ from dual
select { ts   /*This is a comment*/  '2020-02-01 01:99:01'} from dual
```

