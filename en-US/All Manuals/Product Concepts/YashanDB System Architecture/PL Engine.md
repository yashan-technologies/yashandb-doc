The PL engine provides users with a platform for procedural language programming, extending procedural capabilities based on SQL, including conditional branches and loops, and implementing custom procedures, functions, etc., offering powerful programming capabilities.

The main advantages of PL are:

- Performance Improvement: When application processing logic is closer to the data, it can greatly reduce the number of interactions between the client and server, significantly lowering execution response time.

- Development Efficiency Improvement: During application development, data processing logic can be encapsulated into highly cohesive and loosely coupled stored procedures or function interfaces to be shared among multiple modules.

- Easier Tuning: As data volume grows, performance issues in the database may gradually emerge or explode suddenly. If the logic related to data access and processing is implemented in stored procedures, DBAs can quickly identify issues and immediately intervene by optimizing PL code.

## Procedure Body

The procedure body is the main carrier of procedural language. Currently supported types of procedure bodies include:

- Anonymous Block

  An anonymous block is a special PL object in the database that is not persisted and runs immediately after creation. It has no object name and cannot be called directly by object name.

- Stored Procedure

  A stored procedure is a form of database object organized by PL language as a procedure, similar to procedures in Pascal (structured programming language).

- UDF

  UDF stands for User Defined Function, which is a form of database object organized by PL language as a function, similar to functions in Pascal (structured programming language). UDF includes PL language UDF, external JAVA language UDF, and external C language UDF.

- Trigger

  A trigger (TRIGGER) is a PL object in the database. Creating a trigger creates an executable procedure body, but the trigger procedure body cannot be explicitly called by the user; it can only be triggered to run by an event. When a certain event occurs, the trigger runs automatically and implicitly.

- Package

  A package (PACKAGE) is a type of PL object in the database, representing a collection of related procedures, functions, variables, cursors, and types.

- UDT

  UDT (User Defined Type) is a user-defined data type, compared to built-in types in the database, used to model real-world entities as objects in the database. UDT is similar to object-oriented programming concepts, containing a set of properties and methods. Users can create new UDTs based on built-in basic types and other UDT types.

- Scheduled Task

  A scheduled task (JOB) is a background task that is triggered to execute based on time. A scheduled task includes three basic elements: unique JOB identifier, tasks to be executed, and execution time and frequency. Based on these three elements, a series of PL language procedures are provided for the direct operational management of a JOB.

## Compilation and Execution

The use of PL involves two phases: compilation and execution.

- Compilation Phase: The PL text sent by the client is lexically analyzed and syntactically analyzed to obtain semantics, followed by basic validity checks and some necessary optimizations. Additionally, the SQL statement part of PL needs to call the SQL engine's interface for parsing, validation, and optimization, ultimately obtaining executable PL objects, which are buffered in the PL pool (an anonymous block is an exception as it buffers in the SQL pool).

- Execution Phase: In the execution phase, if an executable PL object is found in the PL pool, the object will be executed directly, and the result will be returned.