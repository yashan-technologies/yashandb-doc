General Description
----

CREATE PACKAGE is used to create a package. A package is a collection of multiple PL objects, encapsulated in a package according to certain business logic, facilitating internal management and providing a simplified external interface.

A package is also a persistent PL object, consisting of two parts:

- PACKAGE HEAD: Created using the CREATE PACKAGE syntax, it includes declarations of the PL objects contained in the collection.
- PACKAGE BODY: Created through CREATE PACKAGE BODY syntax, containing procedure body definitions corresponding to each PL object declared in the PACKAGE HEAD, and can also declare and define private procedure body objects for that PACKAGE BODY.

The creation of a package must be implemented in procedural language and must comply with all the specifications and constraints of the PL implemented by YashanDB (including variables, parameters, statements, etc.). For specific details, please refer to the [PL Reference Manual](../../PL参考手册/00PL参考手册).

For the syntax description of CREATE PACKAGE, please refer to [Package](../../PL参考手册/PL对象/自定义高级包).
