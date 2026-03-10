```ebnf+diagram
listagg::= LISTAGG "(" [ALL|DISTINCT] expr ["," separator] [listagg_overflow_clause] ")"
  [WITHIN GROUP order_by_clause] [OVER query_partition_clause]
```

The LISTAGG function concatenates multiple rows of [expr](../General SQL Syntax/expr) and separates them with a delimiter, returning a single VARCHAR/RAW type string.

**expr**

expr cannot be of type BIT, BOOLEAN, timezone types, or UDT types.

If expr is of RAW type, the function returns RAW type; otherwise, the function returns VARCHAR type.

**DISTINCT**

Filters out duplicate rows within the same group when calculating the final concatenated result.


DISTINCT does not support LOB data.


**ALL**

The default value, indicating that duplicate rows are not filtered and all rows are concatenated.

**separator**

Defines the delimiter. If the separator is not specified, the default delimiter is NULL.

The separator can be a constant (including constant expressions) or a variable expression bound by parameters, and its data type cannot be BOOLEAN, BIT, CLOB, BLOB, JSON, UDT, or NCLOB.

When expr is of RAW type, the separator must be of RAW type (or implicitly convertible to RAW).

If expr is NULL, that row will be ignored.

**listagg_overflow_clause**

```ebnf+diagram
listagg_overflow_clause::= (on overflow error | on overflow truncate ["'" text "'"] [(with | without) count])
```

When the length of the terminator or separator exceeds 8000, or (length of the separator) + (length of the terminator) + (with count 26 bytes) exceeds 8000, an error is reported directly.

When the concatenation result overflows (i.e., concatenated expr + separator > 8000), special handling is performed:

-  on overflow error: the default option, an error is reported directly when overflow occurs.
- on overflow truncate(text): text is the specified terminator (i.e., the separator), and when overflow occurs, space must be reserved for the terminator. The default terminator is '...', which occupies 3 bytes.
  - with count: when overflow occurs and truncate is used, count will be output by default. After an overflow occurs, 26 bytes must be reserved (24 characters for count and 2 characters for parentheses). The number of already concatenated rows will be rolled back to allow space for "(count)" and the terminator, and the final number of overflowed rows will be assigned to count. For example, on overflow truncate '*' with count.
  - without count: the number of truncated rows will not be displayed after truncation; when overflow occurs, space must be reserved for the terminator, and no space needs to be reserved for "(count)". For example, on overflow truncate '*' without count.

The situation where the specified terminator degrades to the default terminator:

-  If it is without count, when the length of the terminator or separator exceeds 4000, or the length of the terminator + separator exceeds 4000, the terminator degrades to '...' and the separator will not be displayed; if the above conditions are not met, the number of displayable rows + separator + terminator will be shown (the number of displayable rows may be 0).
- If it is with count or default, when the length of the terminator or separator exceeds 4000 - 2 - 24 = 3974, or the length of the terminator + separator exceeds 3974, count will be displayed, the terminator degrades to '...', and the separator will not be displayed; if the above conditions are not met, the number of displayable rows + separator + terminator + count will be shown (the number of displayable rows may be 0).

**within group order_by_clause**

Sorts the data to be concatenated within the group before concatenation.

As a non-window function, this keyword is only applicable to HEAP tables.

***Example*** for Heap tables

```sql
--Create the exprs table and insert data
DROP TABLE IF EXISTS exprs;
CREATE TABLE exprs(id INT , name VARCHAR(8000) , class RAW(300));
INSERT INTO exprs VALUES(1,'carrot','012');
INSERT INTO exprs VALUES(3,'rabbit','0ea');
INSERT INTO exprs VALUES(3,'carrot','66734');
INSERT INTO exprs VALUES(2,'clion','012');
INSERT INTO exprs VALUES(3,'apple','12900');
COMMIT;

--Concatenate data normally
SELECT LISTAGG(name) res FROM exprs;
RES
----------------------------------------------------------------
carrotrabbitcarrotclionapple                                    

SELECT LISTAGG(class,'00a') res FROM exprs;
RES
----------------------------------------------------------------
0012000A00EA000A066734000A0012000A012900          

--Use DISTINCT keyword to eliminate duplicates
SELECT LISTAGG(DISTINCT name ,' ;') res FROM exprs;
RES
----------------------------------------------------------------
apple ;carrot ;clion ;rabbit
    
--Use within group (order by) for in-row sorting
SELECT LISTAGG(name,';') within GROUP(ORDER BY name) FROM exprs;
NAME
----------------------------------------------------------------
apple;carrot;carrot;clion;rabbit

--Use within group and partition for in-window sorting
SELECT id,LISTAGG(name,' ') within GROUP(ORDER BY name) FROM exprs GROUP BY id;
          ID NAME
------------ ----------------------------------------------------------------
           1 carrot
           3 apple carrot rabbit
           2 clion

           3 carrot carrot
SELECT id,LISTAGG(name,' ') within GROUP(ORDER BY name) OVER (PARTITION BY name) name FROM exprs ORDER BY 1,2;
          ID NAME
------------ ----------------------------------------------------------------
           1 carrot carrot
           2 clion
           3 apple
           3 carrot carrot
           3 rabbit

--Insert a few more rows to enable overflow situations
DELETE exprs;
INSERT INTO exprs(name) VALUES(LPAD('as',4000,'cad'));
INSERT INTO exprs(name) VALUES(LPAD('dj',3000,'cad'));
INSERT INTO exprs(name) VALUES(LPAD('sd',500,'cdua'));
INSERT INTO exprs(name) VALUES(LPAD('sss',200,'cpd'));
INSERT INTO exprs(name) VALUES(LPAD('asiufs',100,'cod'));
INSERT INTO exprs(name) VALUES(LPAD('affs',300,'cdd'));

--Operation 1 when overflow occurs: direct error
SELECT LISTAGG(name,';' ON overflow error) FROM exprs;
YAS-02511 result of string concatenation value exceeds maximum length of 8000 characters

--Operation 2 when overflow occurs: truncation
SELECT LISTAGG(name,';' ON overflow TRUNCATE) res FROM exprs;
--Truncated output
......ss;codcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcasiufs;...(1)
```

***Example*** for TAC tables and LSC tables

```sql
-- Create the exprs table and insert data
DROP TABLE IF EXISTS exprs;
CREATE TABLE exprs(id INT , name VARCHAR(8000) , class RAW(300));
INSERT INTO exprs VALUES(1,'carrot','012');
INSERT INTO exprs VALUES(3,'rabbit','0ea');
INSERT INTO exprs VALUES(3,'carrot','66734');
INSERT INTO exprs VALUES(2,'clion','012');
INSERT INTO exprs VALUES(3,'apple','12900');
COMMIT;

-- Normal data concatenation
SELECT LISTAGG(name) res FROM exprs;
RES
----------------------------------------------------------------
carrotrabbitcarrotclionapple                                    

SELECT LISTAGG(class,'00a') res FROM exprs;
RES
----------------------------------------------------------------
0012000A00EA000A066734000A0012000A012900          

-- Use DISTINCT keyword to eliminate duplicates
SELECT LISTAGG(DISTINCT name ,' ;') res FROM exprs;
RES
----------------------------------------------------------------
apple ;carrot ;clion ;rabbit

--Use within group + partition to sort data within the window
SELECT id,LISTAGG(name,' ') within GROUP(ORDER BY name) OVER (PARTITION BY name) name FROM exprs ORDER BY 1,2;
          ID NAME
------------ ----------------------------------------------------------------
           1 carrot carrot
           2 clion
           3 apple
           3 carrot carrot
           3 rabbit

--Insert a few more rows to enable overflow situations
DELETE exprs;
INSERT INTO exprs(name) VALUES(LPAD('as',4000,'cad'));
INSERT INTO exprs(name) VALUES(LPAD('dj',3000,'cad'));
INSERT INTO exprs(name) VALUES(LPAD('sd',500,'cdua'));
INSERT INTO exprs(name) VALUES(LPAD('sss',200,'cpd'));
INSERT INTO exprs(name) VALUES(LPAD('asiufs',100,'cod'));
INSERT INTO exprs(name) VALUES(LPAD('affs',300,'cdd'));

-- Operation 1 when overflow occurs: direct error
SELECT LISTAGG(name,';' ON overflow error) FROM exprs;
YAS-05005 result of string concatenation value exceeds maximum length of 8000 characters

-- Operation 2 when overflow occurs: truncation
SELECT LISTAGG(name,';' ON overflow TRUNCATE) res FROM exprs;
-- Truncated output
......ss;codcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcasiufs;...(1)
```

**OVER**

When the OVER keyword is specified, LISTAGG acts as a [window function](00Built-in Functions.html#WindowFunction), concatenating rows within each group window, where the concatenation result of each row in the group window is the same.

**query_partition_clause**

The general syntax for window functions.

***Example***

```sql
-- Create the exprs table and insert data
DROP TABLE IF EXISTS exprs;
CREATE TABLE exprs(id INT , name VARCHAR(8000) , class RAW(300));
INSERT INTO exprs VALUES(1,'carrot','012');
INSERT INTO exprs VALUES(3,'rabbit','0ea');
INSERT INTO exprs VALUES(3,'carrot','66734');
INSERT INTO exprs VALUES(2,'clion','012');
INSERT INTO exprs VALUES(3,'apple','12900');
COMMIT;

-- Query and concatenate
SELECT id,name,
LISTAGG(name, ',') OVER (PARTITION BY id) c1,
MAX(name) OVER (PARTITION BY id ORDER BY name) c2
FROM exprs;
 ID NAME       C1                     C2        
--- ---------- ---------------------- --------- 
  1 carrot     carrot                 carrot   
  2 clion      clion                  clion    
  3 apple      rabbit,carrot,apple    apple    
  3 carrot     rabbit,carrot,apple    carrot   
  3 rabbit     rabbit,carrot,apple    rabbit  
```
