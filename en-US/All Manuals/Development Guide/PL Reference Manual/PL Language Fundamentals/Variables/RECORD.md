Before declaring the RECORD type, it is necessary to define the structure of the RECORD type, including user-defined and system implicit definitions:

- User-defined: The structure member information of the RECORD is explicitly defined using the TYPE ... IS RECORD syntax.
- %TYPE: When declaring a variable as [reference variable](Reference Variables)%TYPE type, if the referenced variable is a RECORD or cursor, the system automatically inherits the structure definition of the referenced variable, and the declared variable is a RECORD collection variable.
- %ROWTYPE: When declaring a variable as [reference variable](Reference Variables)%ROWTYPE type, the system automatically inherits the row structure definition of the referenced object, and the declared variable is a RECORD collection variable.

## User-defined RECORD

User-defined RECORD requires explicit specification of the RECORD type name, member names, and member data type information. The data type cannot be defined as a cursor type.

The definition syntax is as follows:

```ebnf+diagram
syntax::= TYPE record_type IS RECORD "(" (member_name (dataType|recodeType) [[NOT] NULL] [(":="|DEFAULT) default_value])
{"," (member_name (dataType|recodeType) [[NOT] NULL] [(":="|DEFAULT) default_value])} ")" ";"
```

The RECORD variable declaration syntax is as follows:

```ebnf+diagram
syntax::= variable_name record_type [[NOT] NULL]";"
```

**dataType**

User-defined types defined in PL [UDT](../../PL Objects/User-Defined Types) and ordinary scalar data types defined in SQL [Data Types](../../../SQL Reference Manual/Data Types (yashan Mode)/00Data Types (yashan Mode)). When the data type is a character type, its Size attribute must also be specified, for example, VARCHAR(10), but input CHAR defaults to CHAR(1); the maximum Size that can be specified is 65534.

**variableName**

The name of the RECORD to be created, which cannot be omitted and must comply with the [naming conventions](../../../SQL Reference Manual/Basic SQL Elements/Identifiers) of YashanDB.

- When defining `rowid` as a RECORD type, variables declared with `rowid` before the definition are of built-in type, while those declared with `rowid` after the definition are of RECORD type.

**recodeType**

A RECORD can nest another RECORD structure, and YashanDB supports a maximum of 16 layers of nesting (if a label is used before the user-defined RECORD, the maximum nesting is 15 layers). The nested RECORD cannot be defined as a reference cursor %TYPE type.

**default_value**

Define a default value for member variables.

## RECORD Assignment

### Assigning values through member variables

Access the member of the RECORD variable using the "." operator to assign values to the members separately, or use object initialization to assign values to the members in bulk.

***Example***

```plsql
DECLARE
TYPE name IS RECORD (
first VARCHAR(20),
last VARCHAR(20) := 'Smith'
);
name1 name;
name2 name;
name3 name;
BEGIN
name1.first := 'Jane'; 
name1.last := 'Smith';
DBMS_OUTPUT.PUT_LINE('name1: ' || name1.first || ' ' || name1.last);
DBMS_OUTPUT.PUT_LINE('name2: ' || name2.first || ' ' || name2.last);
-- Initialization method
name3 := name('Lisa','Stone');
DBMS_OUTPUT.PUT_LINE('name3: ' || name3.first || ' ' || name3.last);
END;
/

--result
name1: Jane Smith
name2:  Smith
name3: Lisa Stone
```

### Assigning values between RECORD variables

The assignment rules between two RECORD variables are as follows:

- When both source and target are user-defined RECORD types: assignment can only occur between variables declared as the same recode_type.
- When one of the source or target is a system implicitly defined RECORD type, assignment is only possible when the number and data types of members on both sides are consistent. Here, the consistency of data types means:
  - Explicit consistency, meaning the system does not perform implicit data type conversion.
  - Precision, length, and other attributes do not need to be consistent.

***Example***

```plsql
-- The following cannot be assigned
DECLARE
TYPE name_1 IS RECORD (
first VARCHAR(20),
last VARCHAR(20) := 'Smith'
);
TYPE name_2 IS RECORD (
first VARCHAR(20),
last VARCHAR(20) := 'Smith'
);
name1 name_1;
name2 name_2;
BEGIN
name1 := name2;
END;
/
YAS-04253 PL/SQL compiling errors:
[13:10] YAS-00014 illegal conversion from NAME_2 to NAME_1

CREATE TABLE name_1 (c1 NUMBER, c2 CHAR(4));
-- The following can be assigned
DECLARE
TYPE name_1 IS RECORD (
first NUMBER(6),
last VARCHAR(40)
);
name1 name_1;
name2 name_1%ROWTYPE;
BEGIN
name1 := name2;
END;
/
-- The following cannot be assigned
DECLARE
TYPE name_1 IS RECORD (
first INT,
last CHAR(4)
);
name1 name_1;
name2 name_1%ROWTYPE;
BEGIN
name1 := name2;
END;
/
YAS-04253 PL/SQL compiling errors:
[9:10] YAS-05286 illegal conversion
```

### Assigning values through SQL statement

When executing a RECORD variable assignment operation through an SQL statement (as source or target), it is required that the structure members of the source and target are consistent, and the data types are compatible, meaning they can meet the system's implicit data type conversion.

#### INSERT statement

Use the INSERT INTO statement to insert a RECORD variable into a table; at this point, only all column items of the table can be inserted as a whole, and it is not possible to insert column items separately.

***Example***

```plsql
DECLARE
TYPE area_type IS RECORD (
area_no VARCHAR(5),
area_name VARCHAR(10),
dhq VARCHAR(5)
);
a area_type;
BEGIN
a.area_no := '09';
a.area_name := 'example';
a.dhq := 'exa';
INSERT INTO area VALUES a;
END;
/
```

#### SELECT statement

Use SELECT...INTO statement to assign the query result to a RECORD variable; at this point, only the SELECT result columns can be assigned to one RECORD variable as a whole, and separate assignments are not allowed, and the query result must be a single row record.

***Example***

```plsql
DECLARE
TYPE area_type IS RECORD (
area_no NUMBER,
area_name VARCHAR(10)
);
a area_type;
BEGIN
SELECT area_no,area_name INTO a
FROM area 
WHERE area_no = '09';
DBMS_OUTPUT.PUT_LINE(a.area_no || ',' || a.area_name);
END;
/

--result
9,example
```
