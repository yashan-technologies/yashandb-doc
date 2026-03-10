YashanDB includes three types of collection variables:

- Variable-length array (VARRAY): A variable-length array contains a set of members of the same type, with members being stored in an ordered manner within the array.
- Nested Table (NESTED TABLE): A nested table contains multiple rows of members of the same type, similar to physical table records, with row members stored in an unordered manner. Using a nested table makes it easier to define and manipulate multidimensional arrays.
- Associative Array (INDEX-BY TABLE): An associative array contains multiple key-value pair members, where each key is a unique index. Key-value pair members are stored in an ordered manner by index.

|Comparison |VARRAY |NESTED TABLE |INDEX-BY TABLE |
| ---- |--------------------------------------------------------|--------------------------------------------------------|-----------------------------------------------------------------------------------|
| Member Count Limit    | The maximum length must be declared at definition, while the actual length (number of members) can change dynamically within this range, as resources allow. | The actual length (number of members) can dynamically grow within resource limits, with an upper limit of 1074791424.                          | The actual length (number of members) can dynamically grow within resource limits.                                                        |
| Member Storage Method | Members are stored in an ordered manner.                                       | Row members are stored in an unordered manner.                                    | Key-value pair members are stored in an ordered manner by index.                                                                    |
| Member Data Type | Can be any PL data type except for cursor types. | Can be any PL data type except for cursor types. | * Index: Can be of types INTEGER, PLS_INTEGER, BINARY_INTEGER, VARCHAR. <br/> * Value: Can be any PL data type except for cursor types. |
| Definition Method | * Can be defined within the procedure body. <br/> * Can be defined as [UDT](../../PL Objects/User-Defined Types) outside the procedure body. | * Can be defined within the procedure body. <br/> * Can be defined as [UDT](../../PL Objects/User-Defined Types) outside the procedure body. | Only supports definition within the procedure body.                                                                       |
| Other | Two arrays cannot be compared.                                          | Two nested tables can be compared for equality.                                | For associative arrays, declaration also initializes them; two associative arrays cannot be compared.                                                    |

## Collection Variable Declaration

Before declaring a collection variable, the collection type must be defined. The syntax for defining a collection variable within the procedure body is:

```ebnf+diagram
varray_definition::= 
TYPE varray_type IS (VARRAY|([VARYING] ARRAY)) "(" size_limit ")" OF datatype [[NOT] NULL]

nested_table_definition::= 
TYPE nested_table_type IS TABLE OF datatype [[NOT] NULL]

indexby_table_definition::= 
TYPE indexby_table_type IS TABLE OF datatype [[NOT] NULL] INDEX BY index_datatype [[NOT] NULL]
```

The syntax for declaring collection variables is:

```ebnf+diagram
syntax::= variable_name (varray_type|nested_table_type|indexby_table_type) [[NOT] NULL]";"
```

**size_limit**

When declaring an array type, it is mandatory to specify the upper limit for the number of array members, which can range from [1,1074791424].

**datatype**

The data type of members which can be specified as any type supported by PL or UDT. When the data type is of character type, the Size attribute must also be specified, e.g., VARCHAR(10), while CHAR defaults to CHAR(1); the maximum Size that can be specified is 65534.

**NULL|NOT NULL**

Members in the collection are allowed to be NULL by default. Using the NULL keyword or omitting it indicates that NULL is allowed for collection members, while using the NOT NULL keyword restricts collection members from being NULL.

**index_datatype**

The index data type can be INTEGER, PLS_INTEGER, BINARY_INTEGER, or VARCHAR. When the data type is of character type, the Size attribute must also be specified, e.g., VARCHAR(10); the maximum Size that can be specified is 65534.

## Collection Variable Assignment

Collection variable assignment includes initialization assignment and assignment during processes.

- Members can be assigned values using the following format:

	```text
	variable_name := (varray_type|nested_table_type) (expr1,expr2,...);

	variable_name := indexby_table_type(key1=>value1,key2=>value2,...);

	variable_name(index) := expr;
	```

	The expr must be a YashanDB recognized [general expression](../../../SQL Reference Manual/General SQL Syntax/expr) when the member type is a regular scalar type; otherwise, it must follow the assignment format specified for that member type.

- An initialized collection variable can be assigned to another collection variable.
- Assignment to the array can also be performed using built-in functions related to arrays, such as [STRING_TO_ARRAY](../../../SQL Reference Manual/Built-in Functions/STRING_TO_ARRAY), [ARRAY_APPEND](../../../SQL Reference Manual/Built-in Functions/ARRAY_APPEND), [ARRAY_REMOVE](../../../SQL Reference Manual/Built-in Functions/ARRAY_REMOVE), [ARRAY_REPLACE](../../../SQL Reference Manual/Built-in Functions/ARRAY_REPLACE), etc.

## Collection Variable Access

A valued collection variable can be accessed using the following syntax:

```ebnf+diagram
syntax::= (varray_type|nested_table_type|indexby_table_type) "(" expr ")"
```

expr must be a YashanDB recognized [general expression](../../../SQL Reference Manual/General SQL Syntax/expr); for arrays and nested tables, the result must be a number which represents the index within the array, starting at position 1; for associative arrays, the resulting data type must comply with the declared index data type, otherwise, implicit type conversion will occur.

***Example***

```plsql
CREATE OR REPLACE TYPE udt_object FORCE AS OBJECT (
	area_no CHAR(2),
    MAP MEMBER FUNCTION showArea RETURN VARCHAR
) NOT FINAL;
/
CREATE OR REPLACE TYPE BODY udt_object AS
	MAP MEMBER FUNCTION showArea RETURN VARCHAR AS
    BEGIN
		RETURN self.area_no;
	END;
END;
/ 

--Define a one-dimensional + nested array through VARRAY and access it
DECLARE
	TYPE char_array IS ARRAY(5) OF CHAR;
	v_char1 char_array := char_array('1',TO_CHAR(2),'');
	v_char2 char_array := v_char1;
	TYPE array_array IS ARRAY(3) OF char_array;
	v_array array_array;
	TYPE udt_array IS ARRAY(3) OF udt_object;
	v_udt udt_array;
	TYPE rec IS RECORD(area udt_object, branch CHAR(4));
	TYPE rec_array IS ARRAY(3) OF rec;
	v_rec rec_array;
BEGIN
	DBMS_OUTPUT.PUT_LINE('char array:'||v_char2(1)||v_char2(TO_NUMBER('2'))||v_char2(3));
	v_array := array_array(v_char1,v_char2);
	DBMS_OUTPUT.PUT_LINE('array array:'||v_array(1)(1)||v_array(1)(2)||v_array(2)(1)||v_array(2)(2));
	v_udt := udt_array(udt_object('01'),udt_object('02'));
	DBMS_OUTPUT.PUT_LINE('udt array:'||v_udt(1).area_no||' '||v_udt(2).area_no);
	v_rec := rec_array(rec(udt_object('03'),'0301'),rec(udt_object('04'),'0401'));
	DBMS_OUTPUT.PUT_LINE('record array:'||v_rec(1).area.area_no||'.'||v_rec(1).branch||
    ' '||v_rec(2).area.area_no||'.'||v_rec(2).branch);
END;
/
--result
char array:12
array array:1212
udt array:01 02
record array:03.0301 04.0401  

--Define a multidimensional array through NESTED TABLE and access it
DECLARE
	TYPE char_array IS ARRAY(5) OF CHAR;
	TYPE char_table IS TABLE OF char_array;
	v_table char_table;
BEGIN
	v_table := char_table(char_array('1',TO_CHAR(2),''),char_array('1',TO_CHAR(2),''));
	DBMS_OUTPUT.PUT_LINE('nested table: '||v_table(1)(1)||v_table(1)(2)||' '||v_table(2)(1)||v_table(2)(2));
END;
/
--result
nested table: 12 12

--Define an indexed associative array with VARCHAR type and access it
DECLARE
    TYPE char_table IS TABLE OF INT INDEX BY VARCHAR(1);
	v_table char_table;
BEGIN
    v_table('a') := 1;
	DBMS_OUTPUT.PUT_LINE('index-by table: '||v_table('a'));
END;
/
--result
index-by table: 1
```

## Collection Variable Operation Methods

In addition to direct access to collection members using indices, YashanDB also includes a series of built-in methods for performing specified operations on collection variables within the procedure body.

Except for EXISTS, other methods can only be operated on after the collection variable has been initialized.

### LIMIT

```ebnf+diagram
syntax::= (varray_type|nested_table_type|indexby_table_type) "." "limit" ["()"]
```

LIMIT is a function used to obtain the member count limit of the collection; for nested tables and associative arrays, the function returns NULL.

***Example***

```plsql
DECLARE
	TYPE char_array IS ARRAY(5) OF CHAR;
	TYPE char_table IS TABLE OF char_array;
	TYPE idx_table IS TABLE OF char_array INDEX BY INT;
	v_char char_array := char_array('1','2','');
	v_t_char char_table := char_table(v_char, v_char, v_char);
	v_idxt_char idx_table;
BEGIN
	DBMS_OUTPUT.PUT_LINE('array limit: '||v_char.LIMIT);
	DBMS_OUTPUT.PUT_LINE('nested table limit: '||v_t_char.LIMIT);
	DBMS_OUTPUT.PUT_LINE('index-by table limit: '||v_idxt_char.LIMIT);
END;
/

--result
array limit: 5
nested table limit: 
index-by table limit: 
```

### COUNT

```ebnf+diagram
syntax::= (varray_type|nested_table_type|indexby_table_type) "." "count" ["()"]
```

COUNT is a function used to obtain the current number of existing members in the collection.

***Example***

```plsql
DECLARE
	TYPE char_array IS ARRAY(5) OF CHAR;
	TYPE char_table IS TABLE OF char_array;
	TYPE idx_table IS TABLE OF char_array INDEX BY INT;
	v_char char_array := char_array('1','2','');
	v_t_char char_table := char_table(v_char, v_char, v_char);
	v_idxt_char idx_table := idx_table(1=>v_char);
BEGIN
	DBMS_OUTPUT.PUT_LINE('array count: '||v_char.count());
	DBMS_OUTPUT.PUT_LINE('nested table count: '||v_t_char.count());
	DBMS_OUTPUT.PUT_LINE('index-by table count: '||v_idxt_char.count());
END;
/

--result
array count: 3
nested table count: 3
index-by table count: 1
```

### FIRST

```ebnf+diagram
syntax::= (varray_type|nested_table_type|indexby_table_type) "." "first" ["()"]
```

FIRST is a function used to obtain the index of the first member currently existing in the collection.

***Example***

```plsql
DECLARE
	TYPE char_array IS ARRAY(5) OF CHAR;
	TYPE char_table IS TABLE OF char_array;
	TYPE idx_table IS TABLE OF char_array INDEX BY INT;
	v_char char_array := char_array('1','2','');
	v_t_char char_table := char_table(v_char, v_char, v_char);
	v_idxt_char idx_table := idx_table(-10=>v_char, 100=>v_char);
BEGIN
	DBMS_OUTPUT.PUT_LINE('array first: '||v_char.first());
	DBMS_OUTPUT.PUT_LINE('nested table first: '||v_t_char.first());
	DBMS_OUTPUT.PUT_LINE('index-by table first: '||v_idxt_char.first());
END;
/

--result
array first: 1
nested table first: 1
index-by table first: -10
```

### LAST

```ebnf+diagram
syntax::= (varray_type|nested_table_type|indexby_table_type) "." "last" ["()"]
```

LAST is a function used to obtain the index of the last member currently existing in the collection.

***Example***

```plsql
DECLARE
	TYPE char_array IS ARRAY(5) OF CHAR;
	TYPE char_table IS TABLE OF char_array;
	TYPE idx_table IS TABLE OF char_array INDEX BY INT;
	v_char char_array := char_array('1','2','');
	v_t_char char_table := char_table(v_char, v_char, v_char);
	v_idxt_char idx_table := idx_table(-10=>v_char, 100=>v_char);
BEGIN
	DBMS_OUTPUT.PUT_LINE('array last: '||v_char.last());
	DBMS_OUTPUT.PUT_LINE('nested table last: '||v_t_char.last());
	DBMS_OUTPUT.PUT_LINE('index-by table last: '||v_idxt_char.last());
END;
/

--result
array last: 3
nested table last: 3
index-by table last: 100
```

### NEXT

```ebnf+diagram
syntax::= (varray_type|nested_table_type|indexby_table_type) "." "next" "(" expr ")" 
```

NEXT is a function used to obtain the next index existing in the collection following the input index. The input index can be a non-existent index, in which case the function returns the next closest index within the collection. When the input index is NULL or there is no next index, the function returns NULL.

expr must be a YashanDB recognized [general expression](../../../SQL Reference Manual/General SQL Syntax/expr) with a result data type compliant with the collection's index data type, otherwise, implicit type conversion will occur.

***Example***

```plsql
DECLARE
	TYPE char_array IS ARRAY(5) OF CHAR;
	TYPE char_table IS TABLE OF char_array;
	TYPE idxby_table IS TABLE OF INT INDEX BY VARCHAR(1);
	v_char char_array := char_array('1','2','');
	v_t_char char_table := char_table(v_char, v_char, v_char);
	v_idxtable idxby_table := idxby_table('b'=>1,'a'=>2,'e'=>3);
BEGIN
	DBMS_OUTPUT.PUT_LINE('array next0: '||v_char.NEXT(0));
	DBMS_OUTPUT.PUT_LINE('array next-9: '||v_char.NEXT(-9));
	DBMS_OUTPUT.PUT_LINE('array next3: '||v_char.NEXT(3));
	DBMS_OUTPUT.PUT_LINE('nested table next0: '||v_t_char.NEXT(0));
	DBMS_OUTPUT.PUT_LINE('nested table next-9: '||v_t_char.NEXT(-9));
	DBMS_OUTPUT.PUT_LINE('nested table next3: '||v_t_char.NEXT(3));
	DBMS_OUTPUT.PUT_LINE('index-by table next b: '||v_idxtable.NEXT('b'));
END;
/

--result
array next0: 1
array next-9: 1
array next3: 
nested table next0: 1
nested table next-9: 1
nested table next3: 
index-by table next b: e
```

### PRIOR

```ebnf+diagram
syntax::= (varray_type|nested_table_type|indexby_table_type) "." "prior" "(" expr ")" 
```

PRIOR is a function used to obtain the previous index existing in the collection before the input index. The input index can be a non-existent index, in which case the function returns the closest previous index within the collection. When the input index is NULL or there is no previous index, the function returns NULL.

expr must be a YashanDB recognized [general expression](../../../SQL Reference Manual/General SQL Syntax/expr) with a result data type compliant with the collection's index data type, otherwise, implicit type conversion will occur.

***Example***

```plsql
DECLARE
	TYPE char_array IS ARRAY(5) OF CHAR;
	TYPE char_table IS TABLE OF char_array;
	TYPE idxby_table IS TABLE OF INT INDEX BY VARCHAR(1);
	v_char char_array := char_array('1','2','');
	v_t_char char_table := char_table(v_char, v_char, v_char);
	v_idxtable idxby_table := idxby_table('b'=>1,'a'=>2,'e'=>3);
BEGIN
	DBMS_OUTPUT.PUT_LINE('array prior-9: '||v_char.PRIOR(-9));
	DBMS_OUTPUT.PUT_LINE('array prior1: '||v_char.PRIOR(1));
	DBMS_OUTPUT.PUT_LINE('array prior4: '||v_char.PRIOR(4));
	DBMS_OUTPUT.PUT_LINE('nested table prior-9: '||v_t_char.PRIOR(-9));
	DBMS_OUTPUT.PUT_LINE('nested table prior1: '||v_t_char.PRIOR(1));
	DBMS_OUTPUT.PUT_LINE('nested table prior4: '||v_t_char.PRIOR(4));
	DBMS_OUTPUT.PUT_LINE('index-by table prior b: '||v_idxtable.PRIOR('b'));
END;
/

--result
array prior-9: 
array prior1: 
array prior4: 3
nested table prior-9: 
nested table prior1: 
nested table prior4: 3
index-by table prior b: a
```

### EXISTS

```ebnf+diagram
syntax::= (varray_type|nested_table_type|indexby_table_type) "." "exists" "(" expr ")" 
```

EXISTS is a function used to determine whether a member exists at the input index position. If the input index exists, the function returns TRUE; if the input index does not exist, it returns FALSE; and if the input index is NULL, the function returns NULL.

expr must be a YashanDB recognized [general expression](../../../SQL Reference Manual/General SQL Syntax/expr) with a result data type compliant with the collection's index data type, otherwise, implicit type conversion will occur.

***Example***

```plsql
DECLARE
	TYPE char_array IS ARRAY(5) OF CHAR;
	TYPE char_table IS TABLE OF char_array;
	TYPE idxby_table IS TABLE OF INT INDEX BY VARCHAR(1);
	v_char char_array := char_array('1','2','');
	v_t_char char_table := char_table(v_char, v_char, v_char);
	v_idxtable idxby_table := idxby_table('b'=>1,'a'=>2,'e'=>3);
BEGIN
	DBMS_OUTPUT.PUT_LINE('array exists-9: '||v_char.EXISTS(-9));
	DBMS_OUTPUT.PUT_LINE('array exists1: '||v_char.EXISTS(1));
	DBMS_OUTPUT.PUT_LINE('array exists4: '||v_char.EXISTS(4));
	DBMS_OUTPUT.PUT_LINE('nested table exists-9: '||v_t_char.EXISTS(-9));
	DBMS_OUTPUT.PUT_LINE('nested table exists1: '||v_t_char.EXISTS(1));
	DBMS_OUTPUT.PUT_LINE('nested table exists4: '||v_t_char.EXISTS(4));
	DBMS_OUTPUT.PUT_LINE('index-by table exists a: '||v_idxtable.EXISTS('a'));
END;
/

--result
array exists-9: false
array exists1: true
array exists4: false
nested table exists-9: false
nested table exists1: true
nested table exists4: false
index-by table exists a: true
```

### EXTEND

```ebnf+diagram
syntax::= (varray_type|nested_table_type) "." "extend" ["(" [expr1 ["," expr2]] ")"] 
```

EXTEND is a procedure used to expand the current number of members in the collection, with the expansion value being the member value at expr2 (the member must exist) and the expansion count being expr1 (for arrays, the total number of members after expansion cannot exceed the array limit).

Associative arrays cannot use EXTEND.

expr1/expr2 must be a YashanDB recognized [general expression](../../../SQL Reference Manual/General SQL Syntax/expr) with results being a non-negative number (NULL indicates extend(0)). If expr2 is omitted, NULL values are extended (EXTEND does not check the NOT NULL constraint of array members, but assigning a NULL value to a variable or array member with a NOT NULL constraint is not allowed). If expr1 is omitted, it indicates extending 1 member.

***Example***

```plsql
DECLARE
	TYPE char_array IS ARRAY(10) OF CHAR;
	TYPE char_table IS TABLE OF char_array;
	v_char char_array := char_array('1','2','3');
	v_t_char char_table := char_table(v_char, v_char, v_char);
BEGIN
	v_char.EXTEND();
	v_char.EXTEND(1);
	v_char.EXTEND(1,1);
	v_t_char.EXTEND();
	v_t_char.EXTEND(1);
	v_t_char.EXTEND(1,1);
	DBMS_OUTPUT.PUT_LINE('array count: '||v_char.count());
	DBMS_OUTPUT.PUT_LINE('nested table count: '||v_t_char.count());
END;
/

--result
array count: 6
nested table count: 6
```

### TRIM

```ebnf+diagram
syntax::= (varray_type|nested_table_type) "." "trim" ["(" [expr] ")"] 
```

TRIM is a procedure used to delete expr number of members from the end of the collection (including members whose indices have been deleted); the deletion count cannot exceed the total number of members in the collection.

Associative arrays cannot use TRIM.

expr must be a YashanDB recognized [general expression](../../../SQL Reference Manual/General SQL Syntax/expr) with a result being a non-negative number (NULL indicates trim(0)). If expr is omitted, it indicates deleting 1 member.

***Example***

```plsql
DECLARE
	TYPE char_array IS ARRAY(10) OF CHAR;
	TYPE char_table IS TABLE OF char_array;
	v_char char_array := char_array('1','2','3');
	v_t_char char_table := char_table(v_char, v_char, v_char);
BEGIN
	v_char.trim('');
	v_char.trim;
	v_char.trim(2);
	v_t_char.trim('');
	v_t_char.trim;
	v_t_char.trim(2);
	DBMS_OUTPUT.PUT_LINE('array count: '||v_char.count());
	DBMS_OUTPUT.PUT_LINE('nested table count: '||v_t_char.count());
END;
/

--result
array count: 0
nested table count: 0
```

### DELETE

```ebnf+diagram
delete_clear::= 
(varray_type|nested_table_type|indexby_table_type) "." "delete" ["()"]

delete_index::= 
(nested_table_type|indexby_table_type) "." "delete" "(" expr1 ["," expr2] ")"
```

DELETE is a procedure used to clear all members in the collection or delete specified members from the collection. When given 0 parameters, it clears all members in the collection. For nested tables and associative arrays, one parameter expr1 can be given to delete the member indexed by expr1; or two parameters expr1 and expr2 can be given to delete members within the range of [expr1,expr2]. expr1 and expr2 can be NULL or nonexistent indices, and the specified index range can be out-of-bounds. After the members within the specified index or index range are deleted, they can be reassigned.

expr1/expr2 must be a YashanDB recognized [general expression](../../../SQL Reference Manual/General SQL Syntax/expr) with results that comply with the collection's index data type, otherwise, implicit type conversion will occur.

***Example***

```plsql
DECLARE
	TYPE char_array IS ARRAY(10) OF CHAR;
	TYPE char_table IS TABLE OF char_array;
	TYPE idxby_table IS TABLE OF INT INDEX BY VARCHAR(1);
	v_char char_array := char_array('1','2','3');
	v_t_char char_table := char_table(v_char, v_char, v_char);
	v_idxtable idxby_table := idxby_table('b'=>1,'a'=>2,'e'=>3);
BEGIN
	v_char.DELETE;
	v_t_char.DELETE;
	v_idxtable.DELETE;
	DBMS_OUTPUT.PUT_LINE('array count: '||v_char.count());
	DBMS_OUTPUT.PUT_LINE('nested table count: '||v_t_char.count());
	DBMS_OUTPUT.PUT_LINE('index-by table count: '||v_idxtable.count());
END;
/

--result
array count: 0
nested table count: 0
index-by table count: 0

DECLARE
	TYPE nest_table IS TABLE OF INT;
	TYPE idxby_table IS TABLE OF INT INDEX BY VARCHAR(1);
	v_table nest_table := nest_table(2, 1, 3);
	v_idxtable idxby_table := idxby_table('b'=>1,'a'=>2,'e'=>3);
BEGIN
	v_table.DELETE(2);
	DBMS_OUTPUT.PUT_LINE('nested table count: '||v_table.count());
    v_table(2) := 4;
	DBMS_OUTPUT.PUT_LINE('nested table count: '||v_table.count());
    DBMS_OUTPUT.PUT_LINE('nested table 2: '||v_table(2));
	v_idxtable.DELETE('c','g');
	DBMS_OUTPUT.PUT_LINE('index-by table count: '||v_idxtable.count());
END;
/

--result
nested table count: 2
nested table count: 3
nested table 2: 4
index-by table count: 2
```
