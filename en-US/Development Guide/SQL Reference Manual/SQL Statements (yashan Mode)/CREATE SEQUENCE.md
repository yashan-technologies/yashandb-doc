General Description
----

CREATE SEQUENCE is used to create a sequence object, including an ascending sequence number generator (the generated sequence numbers are positive) and a descending sequence number generator (the generated sequence numbers are negative).

The data type for sequence number generators can only be integers.

For ascending sequence number generators, the generated sequence number cannot be less than 1; for descending sequence number generators, the generated sequence number cannot be greater than -1.

Users cannot execute this statement in ISC Distributed Cluster Deployment.

Statement Definition
----

**create sequence::=**

```ebnf+diagram
syntax::= CREATE SEQUENCE [schema "."] sequence 
[(((INCREMENT BY|START WITH) integer)|(MAXVALUE integer|NOMAXVALUE)|(MINVALUE integer|NOMINVALUE)|(CYCLE|NOCYCLE)|(ORDER|NOORDER)|(CACHE integer|NOCACHE)) 
{" " (((INCREMENT BY|START WITH) integer)|(MAXVALUE integer|NOMAXVALUE)|(MINVALUE integer|NOMINVALUE)|(CYCLE|NOCYCLE)|(ORDER|NOORDER)|(CACHE integer|NOCACHE))}]
```

### 1. sequence

This statement is used to specify the name of the sequence number generator to be created. It is mandatory and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

If all subsequent statements in this clause are omitted, an ascending sequence number generator will be created starting from 1, incrementing by 1, and with no maximum limit (but limited by the operating system's maximum integer limit).

Sequence numbers are divided into two types: CURRVAL and NEXTVAL, which are specifically described in the [pseudo-columns](../Basic SQL Elements/Pseudo Columns) chapter.

***Example*** for Standalone Deployment and YAC Deployment

```sql
CREATE SEQUENCE seq_yashan1;
 
SELECT seq_yashan1.NEXTVAL FROM DUAL;
  SEQ_YASHAN1.NEXTVAL
---------------------
                    1
```

### 2. INCREMENT BY

This statement is used to specify the increment of the sequence number generator, i.e., the next sequence number value = current sequence number value + increment. This value cannot be zero; if omitted, the default is 1.

A positive value indicates an ascending sequence number generator, while a negative value indicates a descending sequence number generator.

### 3. START WITH

This statement specifies the starting value of the sequence number generator, which must be between MINVALUE and MAXVALUE. If omitted, the default is 1 (for ascending sequence number generators) or -1 (for descending sequence number generators).

### 4. MAXVALUE|NOMAXVALUE

This statement specifies the maximum value for the sequence number generator.

For ascending sequence number generators, NOMAXVALUE indicates the maximum positive integer value of the operating system; for descending sequence number generators, NOMAXVALUE indicates -1.

If this statement is omitted, the default is NOMAXVALUE.

### 5. MINVALUE|NOMINVALUE

This statement specifies the minimum value for the sequence number generator.

For ascending sequence number generators, NOMINVALUE indicates 1; for descending sequence number generators, NOMINVALUE indicates the maximum negative integer value of the operating system.

If this statement is omitted, the default is NOMINVALUE.

### 6. CYCLE|NOCYCLE

This statement specifies whether to cycle the sequence number when it reaches MAXVALUE (for ascending sequence number generators) or MINVALUE (for descending sequence number generators).

- CYCLE: For ascending sequence number generators, when the next sequence number exceeds MAXVALUE, it will be reset to MINVALUE; for descending sequence number generators, when the next sequence number is below MINVALUE, it will be reset to MAXVALUE.

- NOCYCLE: If the next sequence number exceeds MAXVALUE (for ascending sequence number generators) or is below MINVALUE (for descending sequence number generators), it will not generate a value and will return an error message.

If this statement is omitted, the default is NOCYCLE.

***Example*** for Standalone Deployment and YAC Deployment

```sql
-- Create an ascending sequence number generator with a maximum value of 20
CREATE SEQUENCE seq_yashan3 INCREMENT BY 10 MAXVALUE 20 CYCLE NOCACHE;
 
-- When the next sequence number exceeds 20, it restarts from the minimum value 1
SELECT seq_yashan3.NEXTVAL FROM DUAL;
  SEQ_YASHAN3.NEXTVAL
---------------------
                    1
SELECT seq_yashan3.NEXTVAL FROM DUAL;
  SEQ_YASHAN3.NEXTVAL
---------------------
                   11
SELECT seq_yashan3.NEXTVAL FROM DUAL;
  SEQ_YASHAN3.NEXTVAL
---------------------
                    1

-- Create a table with an auto-increment column
CREATE TABLE area_seq(area_no INT DEFAULT seq_yashan3.NEXTVAL, area_name VARCHAR2(60));
```
<span id="sequence_order" name="sequence_order" class="yaslink"></span>

### 7. ORDER|NOORDER

ORDER/NOORDER is used to specify whether to ensure that sequence numbers are generated in the order requested.

- In Standalone Deployment, YashanDB has ensured that sequence numbers are generated in the order requested; ORDER/NOORDER has no effect on the sequence number's performance.

- In YAC Deployment, if ORDER is specified, the sequence numbers will remain ordered across all instances; if NOORDER is specified, and the number of pre-allocated sequence numbers is greater than 1, then the sequence numbers will remain ordered within each instance.

***Example*** for YAC Deployment

In a two-instance YAC Deployment, Instance 1 executes:

```sql
-- Create an ordered sequence with 5 pre-allocated sequence numbers 
CREATE SEQUENCE seq_yashan4_order CACHE 5 ORDER;

-- Create an unordered sequence with 5 pre-allocated sequence numbers 
CREATE SEQUENCE seq_yashan4_noorder CACHE 5 NOORDER;

-- Retrieve sequence numbers for both sequences from Instance 1
SELECT seq_yashan4_order.NEXTVAL FROM DUAL;
SEQ_YASHAN4_ORDER.NE
--------------------
                   1
SELECT seq_yashan4_noorder.NEXTVAL FROM DUAL;
SEQ_YASHAN4_NOORDER.
--------------------
                   1
```

Instance 2 executes:

```sql
-- Retrieve sequence numbers for both sequences from Instance 2
SELECT seq_yashan4_order.NEXTVAL FROM DUAL;
SEQ_YASHAN4_ORDER.NE
--------------------
                   2
SELECT seq_yashan4_noorder.NEXTVAL FROM DUAL;
SEQ_YASHAN4_NOORDER.
--------------------
                   6
```

### 8. CACHE|NOCACHE

This statement specifies whether to pre-allocate sequence numbers in memory. If omitted, the default is to pre-allocate 20 sequence numbers in memory.

- NOCACHE: No pre-allocation of sequence numbers in memory.

- CACHE: Specify the number of pre-allocated sequence numbers, which can be any integer greater than 1. If CYCLE is also specified, this value must also be less than the number of sequence numbers that can be generated in one cycle of the sequence generator, i.e., it must be less than `(MAXVALUE-MINVALUE)/ABS(INCREMENT BY)+1`.

***Example*** for Standalone Deployment and YAC Deployment
```sql
 -- Since ((11-1)/10)+1=2, and the cache value must be less than this value, an error is returned
CREATE SEQUENCE seq_yashan5 INCREMENT BY 10 MAXVALUE 11 CYCLE CACHE 2;
YAS-02096 sequence param CACHE error, number of CACHE must be less than one cycle
```

In YAC Deployment, pre-allocation is at the instance level, with each instance pre-allocating a segment of sequence numbers in order; the sequence numbers are ordered within the instance but not globally across the cluster.

If specified as NOCACHE, then no pre-allocation occurs, and the sequence numbers are ordered globally across the cluster.

***Example*** for YAC Deployment

**Pre-allocated Sequence Number Scenario**

In a two-instance YAC Deployment, Instance 1 executes:

```sql
CREATE SEQUENCE seq_yashan6 CACHE 5;
-- Instance 1 pre-allocated sequence numbers 1, 2, 3, 4, 5
SELECT seq_yashan6.NEXTVAL FROM DUAL;
SEQ_YASHAN6.NEXTVAL
-------------------
                  1
```

Instance 2 executes:

```sql
-- Instance 2 pre-allocated sequence numbers 6, 7, 8, 9, 10 after Instance 1
SELECT seq_yashan6.NEXTVAL FROM DUAL;
SEQ_YASHAN6.NEXTVAL
-------------------
                  6
```

**Non-pre-allocated Sequence Number Scenario**

In a two-instance YAC Deployment, Instance 1 executes:

```sql
CREATE SEQUENCE seq_yashan7 NOCACHE;
-- Instance 1 retrieves sequence number 1
SELECT seq_yashan7.NEXTVAL FROM DUAL;
SEQ_YASHAN7.NEXTVAL
-------------------
                  1
```

Instance 2 executes:

```sql
-- Instance 2 retrieves sequence number 2 after Instance 1
SELECT seq_yashan7.NEXTVAL FROM DUAL;
SEQ_YASHAN7.NEXTVAL
-------------------
                  2
```
