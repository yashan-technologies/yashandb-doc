General Description
----

CREATE TRIGGER is used to create or replace a database trigger, which the system will automatically execute when specified conditions occur.

A user must have the CREATE TRIGGER system privilege to create a trigger on objects in their own schema.

A user must have the CREATE ANY TRIGGER system privilege to create a trigger on objects in any schema.

Triggers are PL objects, and their creation must be implemented using procedural language. It must comply with all the specifications and constraints of the PL implemented by YashanDB (including variables, parameters, statements, etc.). For specific descriptions, please refer to the [PL Reference Manual](../../All Manuals/Development Guide/PL Reference Manual/00PL Reference Manual).

For a detailed description of the syntax of CREATE TRIGGER, please refer to the [Triggers](../../All Manuals/Development Guide/PL Reference Manual/PL Objects/Triggers) section of the PL Reference Manual.