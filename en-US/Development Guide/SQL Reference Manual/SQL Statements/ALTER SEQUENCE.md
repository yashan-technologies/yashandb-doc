General Description
----

ALTER SEQUENCE is used to modify various parameters of the sequence number generator.

In ISC Distributed Cluster Deployment, users cannot execute this statement.

Statement Definition
----

**alter sequence::=**

```ebnf
= ALTER SEQUENCE [schema "."] sequence
((INCREMENT BY integer) | (MAXVALUE integer|NOMAXVALUE) | (MINVALUE integer|NOMINVALUE) | (CYCLE|NOCYCLE) | (ORDER|NOORDER) | (CACHE integer|NOCACHE) | RESTART [START WITH integer])
{" " ((INCREMENT BY integer) | (MAXVALUE integer|NOMAXVALUE) | (MINVALUE integer|NOMINVALUE) | (CYCLE|NOCYCLE) | (ORDER|NOORDER) | (CACHE integer|NOCACHE) | RESTART [START WITH integer])}.
```

### INCREMENT BY

This statement is used to modify the increment value of the sequence number generator, which follows the same rules as [CREATE SEQUENCE](CREATE SEQUENCE). After modification, the starting value of the sequence will not be retroactive.

### MAXVALUE|NOMAXVALUE

This statement is used to modify the maximum value of the sequence number generator, which follows the same rules as [CREATE SEQUENCE](CREATE SEQUENCE).

### MINVALUE|NOMINVALUE

This statement is used to modify the minimum value of the sequence number generator, which follows the same rules as [CREATE SEQUENCE](CREATE SEQUENCE).

### CYCLE|NOCYCLE

This statement is used to modify the cycle switch setting of the sequence number generator, which follows the same rules as [CREATE SEQUENCE](CREATE SEQUENCE).

### ORDER|NOORDER

This statement is used to modify the ORDER attribute of the sequence number generator, which follows the same rules as [CREATE SEQUENCE](CREATE SEQUENCE).

### CACHE|NOCACHE

This statement is used to modify the number of pre-allocated sequence numbers, which follows the same rules as [CREATE SEQUENCE](CREATE SEQUENCE).

### RESTART

This statement is used to reset the sequence number of the sequence number generator. By default, ascending sequence number generators will be reset to the minimum value, and descending sequence number generators will be reset to the maximum value.

#### START WITH

This statement is used to specify the initial value when resetting the sequence. When this statement is specified, the sequence number of the sequence will be reset to the given value.

There is no mandatory order requirement between the RESTART keyword and START WITH integer.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create an ascending sequence number generator with a minimum value of 10
CREATE SEQUENCE seq_yashan MINVALUE 10;

SELECT seq_yashan.NEXTVAL FROM DUAL;
   SEQ_YASHAN.NEXTVAL
---------------------
                   10
SELECT seq_yashan.NEXTVAL FROM DUAL;
   SEQ_YASHAN.NEXTVAL
---------------------
                   11

-- Reset to minimum value
ALTER SEQUENCE seq_yashan RESTART;

SELECT seq_yashan.NEXTVAL FROM DUAL;
   SEQ_YASHAN.NEXTVAL
---------------------
                   10

-- Reset to specified value
ALTER SEQUENCE seq_yashan RESTART START WITH 11;

SELECT seq_yashan.NEXTVAL FROM DUAL;
   SEQ_YASHAN.NEXTVAL
---------------------
                   11
```
