General Description
----

ALTER SEQUENCE is used to modify various parameters of the sequence number generator.

In ISC Distributed Cluster Deployment, users cannot execute this statement.

Statement Definition
----

**alter sequence::=**

```ebnf+diagram
syntax::= ALTER SEQUENCE [schema "."] sequence 
((INCREMENT BY integer) | (MAXVALUE integer|NOMAXVALUE) | (MINVALUE integer|NOMINVALUE) | (CYCLE|NOCYCLE) | (ORDER|NOORDER) | (CACHE integer|NOCACHE))
{" " ((INCREMENT BY integer) | (MAXVALUE integer|NOMAXVALUE) | (MINVALUE integer|NOMINVALUE) | (CYCLE|NOCYCLE) | (ORDER|NOORDER) | (CACHE integer|NOCACHE))}
```

### 1. INCREMENT BY

This statement is used to modify the increment value of the sequence number generator, which follows the same rules as [CREATE SEQUENCE](CREATE SEQUENCE). After modification, the starting value of the sequence will not be retroactive.

### 2. MAXVALUE|NOMAXVALUE

This statement is used to modify the maximum value of the sequence number generator, which follows the same rules as [CREATE SEQUENCE](CREATE SEQUENCE).

### 3. MINVALUE|NOMINVALUE

This statement is used to modify the minimum value of the sequence number generator, which follows the same rules as [CREATE SEQUENCE](CREATE SEQUENCE).

### 4. CYCLE|NOCYCLE

This statement is used to modify the cycle switch setting of the sequence number generator, which follows the same rules as [CREATE SEQUENCE](CREATE SEQUENCE).

### 5. ORDER|NOORDER

This statement is used to modify the ORDER attribute of the sequence number generator, which follows the same rules as [CREATE SEQUENCE](CREATE SEQUENCE).

### 6. CACHE|NOCACHE

This statement is used to modify the number of pre-allocated sequence numbers, which follows the same rules as [CREATE SEQUENCE](CREATE SEQUENCE).