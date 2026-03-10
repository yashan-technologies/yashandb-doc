```ebnf+diagram
chartorowid::= CHARTOROWID "(" char ")"
```

CHARTOROWID is used to convert parameters of character, CLOB, NCLOB and RAW types into ROWID type.

When char is NULL, the function returns NULL.

Char must conform to the valid format requirements of the ROWID type.

***Example*** for Heap tables

```sql
SELECT CHARTOROWID('2368:0:0:3164:0') rowid1,TYPEOF(CHARTOROWID('2368:0:0:3164:0')) type1 FROM DUAL;
ROWID1                     TYPE1
-------------------------- ---------
2368:0:0:3164:0            rowid
```
