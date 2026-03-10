Generic Description
----

CREATE TYPE is used to create or replace a User-Defined Type (UDT) in the database. Compared to the built-in data types of the database, UDT is a data type defined by the user.

In YashanDB, users can define the following types of UDT:

- Object: An Abstract Data Type, this type of UDT consists of attributes and methods. To create this type of UDT, the [CREATE TYPE BODY](CREATE TYPE BODY) statement must be used to implement the methods.
- Varray: An array type, this type of UDT is a collection of elements of the same data type.

Users must have the CREATE TYPE system privilege to create UDTs in their own schema.

Users must have the CREATE ANY TYPE system privilege to create UDTs in any schema.

The creation of UDT must be implemented using procedural language and must adhere to all specifications and constraints of the PL implemented by YashanDB (including variables, parameters, statements, etc.). For detailed descriptions, please refer to the [PL Reference Manual](../../All Manuals/Development Guide/PL Reference Manual/00PL Reference Manual).

For a detailed description of the syntax of CREATE TYPE, please refer to the [UDT](../../All Manuals/Development Guide/PL Reference Manual/PL Objects/User-Defined Types) section of the PL Reference Manual.