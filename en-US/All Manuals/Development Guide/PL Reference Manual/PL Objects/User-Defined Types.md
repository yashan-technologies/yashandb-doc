UDT is a type of PL object in the database, abbreviated as UDT.

UDT is not suitable for ISC Distributed Cluster Deployment.

The types of data structures that UDT can define include:

- Object Structure: An abstract data type (Abstract Data Type) that follows object-oriented concepts, containing attributes and methods. UDTs can inherit from or nest within each other. It requires the creation of both a type and a type body, and the type must be created before the type body.
- Array Structure (Varray): A collection type that requires a maximum number to be defined and contains multiple elements of variable length. Besides being created as a standalone UDT object, it can also be directly declared as an array variable in the body of a procedure as part of a [collection variable](../PL Language Fundamentals/Variables/Collection Variables). Only the type part needs to be created.
- Nested Table Structure: A collection type that does not define a maximum number and contains multiple elements of variable length. Besides being created as a standalone UDT object, it can also be directly declared as a nested table variable of a [collection variable](../PL Language Fundamentals/Variables/Collection Variables) in the body of a procedure. Only the type part needs to be created.

**Initialization of UDT**

The system automatically creates a construct function with the same name for each UDT, which initializes when the object is instantiated.

**Instantiation of UDT**

When a data type is declared as a variable of UDT, it becomes an instance of that UDT. Initializing the variable with a value is the instantiation of that UDT.

**Dependency of UDT**

When a UDT is referenced by a table or another UDT, it becomes a dependent object and cannot be rebuilt, modified, or deleted (unless using the FORCE option).

For inheritance dependencies, if the parent type is forcibly modified or rebuilt, the child type will also be automatically rebuilt.

For nested dependencies, if the dependent UDT is forcibly modified, the dependent UDT will automatically become invalid.

**Privilege of UDT**

Owning the CREATE TYPE privilege allows creating a UDT in one's own schema. The CREATE ANY TYPE privilege is needed to create a UDT in other schemas.

When creating a UDT in other schemas, the user who owns the UDT must directly possess the relevant privileges, and the privileges inherited from roles will not be effective.

When using UDTs from other schemas, such as accessing UDT columns in SQL or defining variables in PL, one must possess EXECUTE ANY TYPE privilege or the object-level privilege of the UDT.

## Creating UDT Type

A UDT type is created using the [CREATE TYPE](../../SQL Reference Manual/SQL Statements/CREATE TYPE) statement, which includes the definition and compilation of the UDT. If the UDT compilation fails, it will still be created, but an error will be reported during usage.

**[create type](#createtype)::=**

```ebnf+diagram
syntax::=  CREATE [OR REPLACE] [EDITIONABLE|NONEDITIONABLE] TYPE [schema "."] type_name
[FORCE] [invoker_rights_clause]
(object_base_type_def | object_subtype_def)
```

**[invoker_rights_clause](#invokerrightsclause)::=**

```ebnf+diagram
syntax::= AUTHID (CURRENT_USER|DEFINER)
```

**[object\_base\_type\_def](#objectbasetypedef)::=**

```ebnf+diagram
syntax::= ( AS | IS ) (object_type_def | varray_type_spec | nested_table_type_def) 
```

**[object\_type\_def](#objecttypedef)::=**

```ebnf+diagram
syntax::= OBJECT  [ "(" (attribute datatype [NULL]) { ","  (attribute datatype [NULL])}
[ "," (element_spec) {"," (element_spec)} ] ")" ] [ [NOT]  FINAL ] 
```

**[element\_spec](#elementspec)::=**

```ebnf+diagram
syntax::= ( constructor_spec | subprogram_spec | map_order_function_spec )
```

**[constructor\_spec](#constructorspec)::=**

```ebnf+diagram
syntax::= CONSTRUCTOR FUNCTION type_name [ "(" [SELF IN OUT datatype ","] (parameter datatype) { "," (parameter datatype)}  ")" ] RETURN SELF AS RESULT
```

**[subprogram\_spec](#subprogramspec)::=**

```ebnf+diagram
syntax::= ( MEMBER | STATIC ) ( procedure_spec | function_spec )
```

**[map\_order\_function\_spec](#maporderfunctionspec)::=**

```ebnf+diagram
syntax::=  ( MAP | ORDER ) MEMBER function_spec
```

**[varray\_type\_spec](#varraytypespec)::=**

```ebnf+diagram
syntax::= (VARRAY | ( [VARYING] ARRAY ) ) "(" size_limit ")" OF datatype [ [NOT] NULL ]
```

**[nested_table_type_def](#nestedtabletypedef)::=**

```ebnf+diagram
syntax::= TABLE OF "(" datatype [ [NOT] NULL ] ")"
```

**[object\_subtype\_def](#objectsubtypedef)::=**

```ebnf+diagram
syntax::= UNDER [ schema "." ] supertype [ "(" (attribute datatype) { ","  (attribute datatype)} [ "," element_spec ] ")" ] [ [NOT]  FINAL ] 
```

<span id="createtype" name="createtype" class="yaslink"></span>

### 1. OR REPLACE 

When the UDT to be created already exists, it will be rebuilt.

### 2. EDITIONABLE | NONEDITIONABLE 

Used for syntax compatibility, with no actual meaning.

### 3. schema 

Includes the schema name of the UDT, which can be omitted, and defaults to the schema of the currently logged-in user.

### 4. type\_name 

The name of the UDT to be created, which cannot have the same name as another UDT in the same schema. It cannot be omitted and must comply with YashanDB's [object naming conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers).

### 5. FORCE 

When the type name exists and is depended on by other objects, the REPLACE FORCE option can forcibly replace the UDT's definition. 

If the type is depended on by a table, it cannot be replaced using REPLACE FORCE.

<span id="invokerrightsclause" name="invokerrightsclause" class="yaslink"></span>

### 6. invoker\_rights\_clause

For details, please refer to the relevant description of [invoker_rights_clause](00PL Objects.html#invoker).

<span id="objectbasetypedef" name="objectbasetypedef" class="yaslink"></span>

### 7. object\_base\_type\_def

Creating a base UDT.

<span id="objectsubtypedef" name="objectsubtypedef" class="yaslink"></span>

#### 7.1. object\_type\_def

Specifying the OBJECT keyword creates an Object type UDT.

The member variables that make up an Object are called attributes, and member subprograms that define Object behavior are called methods.

A UDT must contain at least one attribute and may not contain any methods.

##### 7.1.1. attribute

The name of the attribute contained in the Object. Each Object type UDT must include at least one attribute, and the names of attributes must be unique within that UDT.

##### 7.1.2. datatype

The data type of the attribute, which may be a system-built data type or a previously defined UDT (this situation is called UDT nesting, and the current type will form a dependency on that UDT).

<span id="elementspec" name="elementspec" class="yaslink"></span>

##### 7.1.3. element\_spec

Specifies the methods contained in the Object, which can be constructor methods, static methods, and member methods.

* The default maximum length for method parameter values is 8000.
* The current type cannot be used in the default values of method parameters.

<span id="constructorspec" name="constructorspec" class="yaslink"></span>

**Constructor Method**

Each UDT will have a default constructor function with the same name created by the system for initialization tasks.

Additionally, users may also define a custom constructor function with the same name as the type. When the number, type of parameters/attributes of the custom constructor function exactly match the default one, the system will call the custom constructor function first.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
CREATE OR REPLACE TYPE udt_object AS OBJECT (
	area_no CHAR(2),
    CONSTRUCTOR FUNCTION udt_object(SELF IN OUT udt_object,a VARCHAR) RETURN SELF AS RESULT
);
/

CREATE OR REPLACE TYPE BODY udt_object AS
	CONSTRUCTOR FUNCTION udt_object(SELF IN OUT udt_object,a VARCHAR) RETURN SELF AS RESULT AS
    BEGIN
		self.area_no := '0'||a;
		RETURN;
	END;
END;
/

DECLARE
	obj1 udt_object;
BEGIN
	obj1 := udt_object(3);
	DBMS_OUTPUT.PUT_LINE('area no is: '||obj1.area_no);
END;
/

--result
area no is: 03
```

<span id="subprogramspec" name="subprogramspec" class="yaslink"></span>

**Static Method**

Static methods are global methods of the UDT and do not have a self parameter, meaning they cannot be called by object instances (declared as that UDT type).

They can be declared as a static stored procedure or static function.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
CREATE OR REPLACE TYPE udt_object_static AS OBJECT (
	area_no CHAR(2),
    STATIC FUNCTION showAreaStatic(a VARCHAR) RETURN VARCHAR
);
/
CREATE OR REPLACE TYPE BODY udt_object_static AS
	STATIC FUNCTION showAreaStatic(a VARCHAR) RETURN VARCHAR IS
    BEGIN
		RETURN '0'||a;
	END;
END;
/

DECLARE
	obj udt_object_static;
BEGIN
	obj := udt_object_static(3);
	DBMS_OUTPUT.PUT_LINE('area_no is: '||udt_object_static.showAreaStatic(obj.area_no));
END;
/

--result
area_no is: 03
```

**Member Method**

Member methods are callable methods of object instances, with a default self parameter (function as IN, procedure as IN OUT).

They can be declared as a member stored procedure or member function. When declared as a member function, they can be of the following two special types:

<span id="maporderfunctionspec" name="maporderfunctionspec" class="yaslink"></span>

- MAP function: Used to map object instances to scalar values. MAP functions cannot have parameters, and their return type cannot be LOB type.
- ORDER function: Used to compare two object instances. ORDER functions must declare a parameter of the UDT type they belong to, and it may only have one such parameter. Their return type must be INT.

In a UDT, MAP functions and ORDER functions cannot exist simultaneously, and at most only one can exist.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
--map function 
CREATE OR REPLACE TYPE udt_object_member FORCE AS OBJECT (
	area_no CHAR(2),
    MAP MEMBER FUNCTION showArea RETURN VARCHAR
) NOT FINAL;
/
CREATE OR REPLACE TYPE BODY udt_object_member AS
	MAP MEMBER FUNCTION showArea RETURN VARCHAR AS
    BEGIN
		RETURN self.area_no;
	END;
END;
/

DECLARE
	obj1 udt_object_member;
	obj2 udt_object_member;
BEGIN
	obj1 := udt_object_member(3);
	obj2 := udt_object_member('a');
	IF obj1.showArea > obj2.showArea THEN
		DBMS_OUTPUT.PUT_LINE('compare result: '||obj1.area_no);
	ELSE
		DBMS_OUTPUT.PUT_LINE('compare result: '||obj2.area_no);
	END IF;
END;
/

--result
compare result: a 

--order function 
CREATE OR REPLACE TYPE udt_object_order AS OBJECT (
	area_no CHAR(2),
    ORDER MEMBER FUNCTION orderArea(a udt_object_order) RETURN INT
);
/
CREATE OR REPLACE TYPE BODY udt_object_order AS
	ORDER MEMBER FUNCTION orderArea(a udt_object_order) RETURN INT AS
    BEGIN
		CASE 
		WHEN self.area_no = a.area_no THEN RETURN 0;
		WHEN self.area_no > a.area_no THEN RETURN 1;
		WHEN self.area_no < a.area_no THEN RETURN -1;
		END CASE;
	END;
END;
/

DECLARE
	obj1 udt_object_order;
	obj2 udt_object_order;
BEGIN
	obj1 := udt_object_order(3);
	obj2 := udt_object_order('a');
	DBMS_OUTPUT.PUT_LINE('compare result: '||obj1.orderArea(obj2));
END;
/

--result
compare result: -1
```

##### 7.1.4. [NOT] FINAL 

Specifying NOT FINAL allows the creation of subtypes for this type. The default is FINAL, meaning no subtypes can be created for this type.

<span id="varraytypespec" name="varraytypespec" class="yaslink"></span>

#### 7.2. varray\_type\_spec

Specifying the VARRAY or [VARYING] ARRAY keyword creates an array type UDT. An array is a collection of ordered elements with a specified length, where each element is of the datatype specified.

Besides the datatype which may be [ordinary scalar data types](../../SQL Reference Manual/Data Types/00Data Types) and defined UDTs, the definition, use, and operations for array type UDTs are consistent with that of the array types declared in the body of the [collection variable types](../PL Language Fundamentals/Variables/Collection Variables).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Create an array type, where the elements of this type are udt_object from the previous example, with a maximum of 10 elements.
CREATE OR REPLACE TYPE udt_varray AS VARRAY(10) OF udt_object;
/

-- Assign values to the udt_varray type variable through the default constructor function.
DECLARE
    arr udt_varray;
BEGIN
	arr := udt_varray(udt_object(1),udt_object(2),udt_object(3));
    DBMS_OUTPUT.PUT_LINE('attribute of varray element:' || arr(1).area_no);
END;
/

--result
attribute of varray element:1
```

<span id="nestedtabletypedef" name="nestedtabletypedef" class="yaslink"></span>

#### 7.3. nested\_table\_type\_def

Specifying the TABLE keyword creates a nested table type UDT. A nested table is a collection of multiple row elements of undefined length, where each element is of the datatype specified.

Besides the datatype which may be [ordinary scalar data types](../../SQL Reference Manual/Data Types/00Data Types) and defined UDTs, the definition, use, and operations for nested table type UDTs are consistent with that of the nested table types declared in the body of the [collection variable types](../PL Language Fundamentals/Variables/Collection Variables).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Create a nested table type, where the elements of this type are udt_varray from the previous example.
CREATE OR REPLACE TYPE udt_nested_table AS TABLE OF udt_varray NOT NULL;
/

-- Assign values to the udt_nested_table type variable with the default constructor function, and assign it to another variable.
DECLARE
    nested_table udt_nested_table;
    nested_table2 udt_nested_table;
BEGIN
	nested_table := udt_nested_table(udt_varray(udt_object(1),udt_object(2),udt_object(3)),udt_varray(NULL));
    nested_table2 := nested_table;
    DBMS_OUTPUT.PUT_LINE('Records of nested_table2 is: '||nested_table2.count);
END;
/

--result
Records of nested_table is: 2
```

<span id="objecttypedef" name="objecttypedef" class="yaslink"></span>

### 8. object\_subtype\_def

Creates a subtype of an already existing type (this situation is referred to as UDT inheritance, where the subtype inherits the properties and methods of the parent type).

The subtype can have attributes with names that do not match any attributes and methods declared in the parent type; otherwise, the other syntax remains the same as that for creating the parent type.

#### 8.1. supertype 

The name of an already existing parent type, which must be a NOT FINAL Object UDT.

If a member function of the subclass needs to call a member function of the parent class, a parent class object must be separately constructed before calling.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Create a subtype of the previous udt_object_member
CREATE OR REPLACE TYPE udt_object_child UNDER udt_object_member(branch_no CHAR(4));
/

DECLARE
	obj udt_object_child;
BEGIN
	obj := udt_object_child('02','0201');
	DBMS_OUTPUT.PUT_LINE('area is:'||obj.showArea||' branch is:'||obj.branch_no);
END;
/

--result
area is:02 branch is:0201
```

## Creating a UDT Type Body 

For methods defined in an Object type, corresponding implementations must be provided in the type body for them to be usable.
The UDT type body is created using the [CREATE TYPE BODY](../../SQL Reference Manual/SQL Statements/CREATE TYPE BODY) statement. The name of the type body must be the same as that of the already created UDT; otherwise, the type body is in an invalid state.

**create type body::=**

```ebnf+diagram
syntax::= CREATE [ OR REPLACE ] [ EDITIONABLE | NONEDITIONABLE ] TYPE BODY [ schema "." ] type_name ( IS | AS ) element_spec element_spec_body  { " " element_spec element_spec_body} END ";"
```

When creating a UDT type body, if element_spec is a function, it supports the syntax of [PARALLEL_ENABLE](User-Defined Functions.html#parallel_enable) and [DETERMINISTIC](User-Defined Functions.html#deterministic), but PARALLEL_ENABLE and DETERMINISTIC have no actual effect and are only for syntax compatibility.

**element_spec_body::=**

```ebnf+diagram
syntax::= (IS|AS) [variable_declare] BEGIN pl_statements END ";"
```

### 1. OR REPLACE 

When the UDT body to be created already exists, it will be rebuilt.

### 2. EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility, with no actual meaning.

### 3. schema 

Includes the schema name of the UDT body, which can be omitted, and defaults to the schema of the currently logged-in user.

### 4. type\_name 

The name of the UDT body to be created, which cannot have the same name as another UDT body in the same schema. It cannot be omitted and must comply with YashanDB's [object naming conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers).

### 5. element\_spec

In the UDT, the [methods](#elementspec) already declared.

### 6. element\_spec\_body

The implementation of the methods, with syntax descriptions compatible with those in the [stored procedures](Stored Procedures) and [UDF](User-Defined Functions) chapters.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Create an OBJECT type UDT udt_object with 4 attributes, 2 methods, one member procedure, and one static function.
CREATE OR REPLACE TYPE udt_object FORCE IS OBJECT (
    branch_no CHAR(4),
    branch_name VARCHAR2(200),
    area_no CHAR(2),
    address VARCHAR2(200),
    MEMBER PROCEDURE showBranch,
    STATIC FUNCTION showObjectType(p1 VARCHAR2) RETURN VARCHAR2);
/

CREATE OR REPLACE TYPE BODY udt_object AS
MEMBER PROCEDURE showBranch IS
BEGIN
    DBMS_OUTPUT.PUT_LINE('branch_name:  ' || branch_name);
    DBMS_OUTPUT.PUT_LINE('address:      ' || SELF.address);
END;
STATIC FUNCTION showObjectType(p1 VARCHAR2) RETURN VARCHAR2 IS
v1 VARCHAR2(64);
BEGIN
    v1 := p1;
    DBMS_OUTPUT.PUT_LINE('This is STATIC METHOD of udt_object TYPE.');
    RETURN v1;
END;
END;
/

-- Assign values to the udt_object type variable through the default constructor function, call the member procedure of the variable, and call the static function of the udt_object type.
DECLARE
    obj1 udt_object;
BEGIN
    obj1 := udt_object('0101','Beijing','01','North Street');
    obj1.showBranch();
    DBMS_OUTPUT.PUT_LINE(udt_object.showObjectType('Shenzhen'));
END;
/

-- Create an array type, where the elements of this type are UDT udt_object and the maximum number of elements is 10.
CREATE OR REPLACE TYPE udt_varray_element AS VARRAY(10) OF udt_object;
/

-- Assign values to the udt_varray_element type variable through the default constructor function.
DECLARE
    arr1 udt_varray_element;
BEGIN
    arr1 := udt_varray_element(udt_object('0101','Beijing','11','North Street'), udt_object('0102','Shanghai','22','Pedestrian street of Nanjing Road'));
    DBMS_OUTPUT.PUT_LINE('attribute of varray element:' || arr1(1).address);
    -- method OF varray element
    arr1(1).showBranch();
    DBMS_OUTPUT.PUT_LINE('varray element count(INIT):' || arr1.COUNT);
    arr1.TRIM();
    DBMS_OUTPUT.PUT_LINE('varray element count(TRIM):' || arr1.COUNT);
    arr1.EXTEND(2, 1);
    DBMS_OUTPUT.PUT_LINE('varray element count(EXTEND):' || arr1.COUNT);
    DBMS_OUTPUT.PUT_LINE('attribute of varray element(copyed):' || arr1(3).address);
END;
/
```

## Overloading Object UDT Methods

Within an Object type UDT, it is allowed to define multiple methods with the same name but different [parameter](../参数/形参和实参) names, quantities, orders, or [data types](../../SQL参考手册/数据类型/00数据类型). PL can use overloading to filter and call such methods with the same name, following the same rules as [package overloading calls to subprocedures](./自定义高级包.html#overload).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
CREATE OR REPLACE TYPE udt_overload AS OBJECT (
    attr NUMBER,
    CONSTRUCTOR FUNCTION udt_overload(val NUMBER) RETURN self AS RESULT,
    CONSTRUCTOR FUNCTION udt_overload(val FLOAT) RETURN self AS RESULT,
    MEMBER FUNCTION func_overload(p1 NUMBER) RETURN NUMBER,
    MEMBER FUNCTION func_overload(p1 NUMBER, p2 VARCHAR) RETURN NUMBER
);
/
CREATE OR REPLACE TYPE BODY udt_overload AS
    CONSTRUCTOR FUNCTION udt_overload(val NUMBER) RETURN self AS RESULT AS
    BEGIN
        self.attr := val + 1;
        RETURN;
    END;
    CONSTRUCTOR FUNCTION udt_overload(val FLOAT) RETURN self AS RESULT AS
    BEGIN
        self.attr := val + 10;
        RETURN;
    END;
    MEMBER FUNCTION func_overload(p1 NUMBER) RETURN NUMBER IS
    BEGIN
        RETURN self.attr + p1 + 100;
    END;
    MEMBER FUNCTION func_overload(p1 NUMBER, p2 VARCHAR) RETURN NUMBER IS
    BEGIN
        DBMS_OUTPUT.PUT_LINE('func_overload ' || p2);
        RETURN self.attr + p1 + 1000;
    END;
END;
/

DECLARE
    olsubp udt_overload := udt_overload(3.123);
BEGIN
    DBMS_OUTPUT.PUT_LINE('udt_overload ' || olsubp.func_overload(10));
END;
/
```

## Deleting UDT

A UDT type body can be deleted using the [DROP TYPE BODY](../../SQL Reference Manual/SQL Statements/DROP TYPE BODY).

A UDT type can be deleted with [DROP TYPE](../../SQL Reference Manual/SQL Statements/DROP TYPE), which will also remove its type body.

## Modifying UDT

A UDT type can be modified with [ALTER TYPE](../../SQL Reference Manual/SQL Statements/ALTER TYPE).