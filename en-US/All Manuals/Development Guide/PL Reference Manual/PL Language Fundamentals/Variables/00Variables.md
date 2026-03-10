A variable is a container used to store data and pass data values in a program. Variables used in the PL procedure body must be declared first, and certain types of variables can be assigned an initial value at the time of declaration.

## Variable Declaration

A declaration is used to define an object, specify its data type, allocate storage space, and name the storage location for later reference. Declarations must appear in the declaration section of a block, subprogram, or package.

Variables can be explicitly declared and implicitly declared:

- **Explicitly Declared Variables**

    Local variables used in procedure bodies, they must be explicitly declared before the BEGIN keyword.

- **Implicitly Declared Variables**

    For variables in the PL [FOR Statement](../../PL Statements/FOR Statement), the counter variable is an implicitly declared local variable and can be used directly without explicit declaration before the BEGIN keyword.

## Variable Initialization and Assignment

When declaring a variable, it can be initialized at the same time, for example: `var INT := 0;`.

### Assignment Statements

Assign constants/variables or expressions resulting from the arithmetic operations of constants/variables to a variable using `:=`.

***Example***

```plsql
DECLARE
    a INT;
BEGIN
    a := 1;
END;
/
```

### SQL Assignment

Assign the result set of SQL (including static SQL and dynamic SQL) to a variable using INTO. Please refer to the descriptions in [DML Statement](../../PL Statements/DML Statement) and [EXECUTE Statement](../../PL Statements/EXECUTE Statement).

### Cursor Assignment

Use the FETCH statement to retrieve values from a cursor and assign them to a variable. For details, please refer to the [FETCH Statement](../../PL Statements/FETCH Statement).

### OUT Parameter Assignment

Assign a value to a variable by passing it as an OUT or IN OUT parameter to a stored procedure or UDF. For specifics, refer to OUT parameters and IN OUT parameters in [Formal and Actual Parameters](../../Parameters/Formal and Actual Parameters).

## Variable Types

When implicitly declaring a variable, its type is known and specified by the system. For explicit declarations, the variable type must be explicitly specified. The format for declaring different types of variables varies. The variable types included in YashanDB PL are:

- **Regular Variables**:

The declaration format for regular variables is: "variable_name [CONSTANT] data_type[[NOT] NULL] [:=initial_value];".

Here, the data type is the same as the [regular scalar data types](../../../SQL Reference Manual/Data Types/00Data Types) defined in SQL.

When the data type is character type, its Size attribute must also be specified, for example VARCHAR(10). However, entering CHAR defaults to CHAR(1). The maximum Size that can be specified for CHAR and VARCHAR is 65534. The optional length units CHAR and BYTE can be specified after Size, representing character length units and byte length units, respectively.

- For VARCHAR (SIZE[CHAR|BYTE]), the initial value must not exceed 65534 bytes. If no length unit is specified, BYTE is used by default.
- For CHAR (SIZE[CHAR|BYTE]), using CHAR length units will prioritize space padding according to the number of characters, up to a maximum of 65534 bytes; using BYTE length units will pad based on byte count, also up to a maximum of 65534 bytes. If no length unit is specified, BYTE is used by default.

When specifying the NOT NULL attribute, a non-NULL initial value must also be provided for the variable. 

When specifying the CONSTANT attribute, it indicates that the declared variable is a constant, which must be assigned a value at the time of declaration and cannot be assigned again after that. For more details, see the [Constants](Constants) section.

- **Cursor**:

A cursor is a special PL variable, which can be categorized into explicit and dynamic types. Its declaration and usage are detailed in the [Cursors](Cursors) documentation.

- **RECORD**: 

A RECORD consists of a set of data items, each with its own name and data type. For example, multiple column items of a table can form a RECORD. Its declaration and usage are explained in the [RECORD](RECORD) documentation.

- **VARRAY**: 

[Collection Variables](Collection Variables), used to define arrays, which can also be composite (nested) arrays.

- **NESTED TABLE**: 

[Collection Variables](Collection Variables), used to define nested tables.

- **%TYPE**: 

[Reference Variables](Reference Variables), used for automatically referencing the type of a certain variable or column field, ensuring that the data type of the declared variable is the same as that of the variable or column field.

- **%ROWTYPE**: 

[Reference Variables](Reference Variables), used for automatically referencing the structure type of a certain table or view, making the declared variable a RECORD that includes all column items of that table or view.

- **EXCEPTION**: 

[User-Defined Exceptions](User-Defined Exceptions) variable, which can only be used in PL's [exception handling](../../PL Exception Handling) mechanism.