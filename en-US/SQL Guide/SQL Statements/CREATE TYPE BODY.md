## General Description

CREATE TYPE BODY is used to create the body of a user-defined type (UDT), which is the specific implementation of the methods declared in CREATE TYPE for an Object type UDT.

The implementation of methods in the type body must correspond one-to-one with the method declarations in the type.

The creation of the type body must be implemented using procedural language and must comply with all the specifications and constraints of the PL implemented by YashanDB (including variables, parameters, statements, etc.). For specific details, please refer to the [PL Reference Manual](../../All Manuals/Development Guide/PL Reference Manual/00PL Reference Manual).

For a detailed syntax description of CREATE TYPE BODY, please see the [UDT](../../All Manuals/Development Guide/PL Reference Manual/PL Objects/User-Defined Types) section of the PL Reference Manual.