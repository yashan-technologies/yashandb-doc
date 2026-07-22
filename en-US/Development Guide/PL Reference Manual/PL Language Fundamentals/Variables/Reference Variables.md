This document describes two reference variable types: %TYPE and %ROWTYPE.

## %TYPE

When declaring a variable, specifying %TYPE allows the variable's data type to be the same as that of another variable or column field. When a variable is used to store the value of another variable or column field, using %TYPE means there is no need to know the other party's data type during declaration. Furthermore, if the other party's data type changes, no change is required in the declaration.

In addition to being used for variable declaration, %TYPE can also be used as a parameter for a PROCEDURE, a parameter for a FUNCTION, or a return value.

When the object referenced by %TYPE is a column field, it will only reference the data type of that column field; other properties like NOT NULL constraints will not be inherited, and any DEFAULT values defined on the column field will also not be inherited.

When the object referenced by %TYPE is another variable, the NOT NULL attribute of that variable will be inherited, but the initial value will not be inherited. Based on this rule, if the referenced variable is specified as NOT NULL, an initial value must be defined for the %TYPE variable, and a NULL constraint must not be specified for that variable.

**Variable Types**

Variables that can be referenced by %TYPE include:

- Ordinary variables
- Cursors
- RECORD
- Members of RECORD, in the form of record_name.member_name
- The combination of label and the above variables, in the form of label.variable_name

The format for declaring a %TYPE variable is as follows:

```plsql
variable_name (referenced_variable_name|table_name.column_name)%TYPE [[NOT] NULL][(:=|DEFAULT) initial_value];
```

**Matching Rules**

For the three literal forms label.variable_name, record_name.member_name, and table_name.column_name mentioned above, the system defines the following matching rules to determine the referenced object of %TYPE:

- Matching is prioritized in the order of record_name.member_name > label.variable_name > table_name.column_name.
- When the item before the "." matches, the previous priority rule is no longer valid. Instead, matching is only performed for that form. If the item after the "." does not match, the system will return an error.

***Example***

```plsql
DROP TABLE IF EXISTS table_type;
CREATE TABLE table_type(a VARCHAR(10));
INSERT INTO table_type VALUES('helloworld');
COMMIT;

-- tb_type matches tb_record, but a does not match aa; an error occurs at this point, and label.variable_name matching is not executed
<<tb_type>>
DECLARE
TYPE tb_record IS RECORD (aa CHAR(12));
a VARCHAR(11);
tb_type tb_record;
b tb_type.a%type;
BEGIN
b := 'helloworld';
DBMS_OUTPUT.PUT_LINE(b);
END;
/
YAS-04253 PL/SQL compiling errors:
[7:3] YAS-05246 invalid tb_type.a%type
[9:1] YAS-04243 invalid identifier "B"
[10:22] YAS-04243 invalid identifier "B"
```

## %ROWTYPE

A variable declared by %ROWTYPE is a RECORD, and its members come from the members of the object that %ROWTYPE references, including member names and types.

Objects that can be referenced by %ROWTYPE include:

- Tables/views, which can be referenced by alias
- Cursors

When referencing a cursor, it means referencing the return value RECORD of that cursor, including explicitly defined return values, as well as implicitly generated return values when no return value is explicitly defined (i.e., the column items of the SQL statement bound to the cursor).

The members of the RECORD include all columns or members owned by the referenced object, and partial references are not allowed.

***Example***

```plsql
DECLARE
a area%ROWTYPE;
TYPE cursor IS REF CURSOR RETURN area%ROWTYPE;
cur cursor;
ano_cur cur%ROWTYPE;
BEGIN
a.area_no := '88';
a.area_name := 'ref column';
DBMS_OUTPUT.PUT_LINE('first record:' || a.area_no || ',' || a.area_name);
OPEN cur FOR SELECT * FROM area;
FETCH cur INTO ano_cur;
DBMS_OUTPUT.PUT_LINE('third record:' || ano_cur.area_no || ',' || ano_cur.area_name);
CLOSE cur;
END;
/

--output
first record:88,ref column
third record:01,EastChina
```
