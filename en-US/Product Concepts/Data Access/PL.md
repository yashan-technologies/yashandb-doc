## Introduction to PL
PL language is a highly programmable language that provides a variety of data types (various standard data types within the scope of SQL capabilities, user-defined data types), variable declarations, variable assignments, multiple types of expressions, control statements, loop statements, jump statements, static SQL, dynamic SQL, exception handling, and many other flexible programming elements. Simultaneously, PL exists in various forms of objects within the database kernel, including stored procedures, UDFs, external UDFs, triggers, etc. Each type of object has similar compilation and execution principles, but their lifecycle and triggering mechanisms differ.

## PL Language Structure

The basic prototype of PL language structure is a stack, following the FILO (First In, Last Out) principle.

The language blocks that constitute PL are generally divided into two main parts: the data area and the statement area.

- Data Area: A region for declaring and defining variables, providing parameter declarations (not available in anonymous blocks) and local variable declarations (variables, constants, cursors, exception variables, etc.).

- Statement Area: A logical line region that provides programming capabilities such as assignment statements, control statements (IF statements, FOR loops, WHILE loops, GOTO statements, etc.), calling procedure or function statements, static SQL statements, dynamic SQL statements, and exception handling statements.

PL language blocks allow PL language blocks to be nested within statement blocks. During the execution of PL, the execution of variables, responses to exceptions, etc., all follow the principle of local precedence.

In PL statement blocks, public variables of package, binding parameters, etc., can be accessed by name. In special objects like triggers, records corresponding to the trigger can also be loaded directly in forms like `:NEW`, `:OLD`, etc.

YashanDB implements the calling of SQL statements in PL language blocks through binding parameter technology, i.e., static SQL statement functionality. During the PL compilation phase, variables appearing in PL are rewritten into the form of binding parameters, and the rewritten statements are then passed to the SQL engine for compilation.

Dynamic SQL statements, unlike static SQL statements, are not limited by the type range of SQL statements and can be freely concatenated to execute SQL statements, providing great programming flexibility in PL.

## PL Execution Mechanism

In common programming languages, each function call maintains an independent stack frame on the call stack to record the call context, formal parameters, and local variables.

The execution principle of PL is similar; each call (e.g., function, control statement, dynamic SQL, etc.) results in a push operation in the PL data area, where the push is for the formal parameters and local variables. After the call ends, a pop operation occurs while completing the assignment from formal parameters to actual parameters.

The statement block of PL has already compiled specific line numbers during the compilation phase. In the execution phase, it selects either to increment the line number or to perform a line number jump based on the calculation results. When a call occurs, context push operations are performed to expand and execute the called statement block. Once execution is complete, a pop operation restores the call stack until all stacks exit.

When an error occurs during statement execution, exception capture is prioritized. If the exception matches successfully, the PL executor switches the line number to the entry statement for exception handling and then resumes normal execution. If an exception cannot be matched, the execution of the current stack's statements will stop, returning the error to the upper stack to confirm whether the upper stack's exception capture is effective, until all stacks exit.

## PL Objects

### Anonymous Block

An anonymous block is a special PL object in the database. It has no name, parameters, or definitions; the data area only includes local variable declarations and will not be persisted. Once created, it runs immediately and cannot be executed by calling.

### Stored Procedure

A stored procedure is a form of database object organized by process in PL language, similar to procedures in Pascal (structured programming language). It can be called by name using EXEC or CALL command or directly in the PL statement block.

When a user creates a stored procedure using the CREATE [OR REPLACE] PROCEDURE statement, the database instance will parse the DDL command containing the procedure name, formal parameters, and procedure statements. The name and formal parameters will serve as the header (HEAD) information for the procedure, while the statements will form the body (BODY) of the procedure. When the header definition meets the requirements, the database instance will store the created procedure object. If an error occurs when compiling the body information, all error lines will be recorded as compilation errors, resulting in a unified error.

Successfully compiled stored procedures generate a binary buffer in the database instance for use during the execution phase, thereby reducing compilation overhead when the stored procedure is repeatedly called. Due to buffer pool size limitations, stored procedures that have not been used for a long time or have become invalid due to changes in dependent objects may have their binary buffer released. After being released, invoking the corresponding stored procedure again using the EXEC command requires recompilation.

The calling logic of a stored procedure is as follows:

- At the entry of the stored procedure, complete the stack frame of the data area first, including preparing formal parameters and declaring variables.

- The statement area completes the calculation of the execution logic and adjusts the program's operation trajectory according to real-time conditions until the procedure body execution is finished.

    If an error occurs during the execution of the procedure body and the exception handling module does not capture this error, it will lead to a runtime error for the stored procedure. The procedure will ultimately assign all output parameters before exiting the stack frame.

### UDF

UDF is a form of database object organized by function in PL language, similar to functions in Pascal (structured programming language). UDF can be directly invoked in the position of calling built-in functions within SQL statements.

When a user creates a UDF using the CREATE [OR REPLACE] FUNCTION statement, the behavior is fundamentally similar to creating a stored procedure but with the addition of return value information.

The data block and statement block capabilities of UDF are equivalent to those of stored procedures.

When calling a UDF directly in SQL statements, the return value will participate in the SQL computation process. In this case, it is not allowed for UDF to carry output parameters, nor is it allowed to have behaviors that affect the main transaction where the SQL resides within the function body.

**External UDF**

External UDF is a special form of UDF that includes external C and Java language forms, referred to as external UDF or Ext-UDF. In some functionalities that are difficult to implement with PL, external UDF can leverage the features and advantages of other high-level statements, thus expanding the applicability of the database.

C language UDF needs to be packaged into a library file using a dynamic library (SO dynamic library), and the database then loads that dynamic library to obtain function information. When calling the C language UDF, the system uses dynamic loading interfaces to implement dynamic library loading and function symbol lookup.

Java language UDF requires a JAR package and CLASS files to load the CLASS into the JVM using a custom class loader. When calling Java UDF, JNI technology is utilized to enable C to call Java.

To mitigate unpredictable risks posed by dynamic libraries to the database, YashanDB employs SAND BOX technology to isolate the YEX_SERVER process, using UDS protocol for communication between the database and YEX_SERVER, ensuring that exceptions occurring during the execution of external UDF do not impact the normal operation of the database.

![](./image/extFunc.png)

### Package

Package is a PL object in the database, which is a collection of related procedures, functions, variables, cursors, and types.

YashanDB has built-in many advanced packages that extend database functionalities, such as DBMS_STATS that provides statistics collection capability, UTL_FILE that provides users the ability to create/delete/read/write file systems, and DBMS_AWR that generates performance reports.

Package allows users to further extend the database capabilities based on their needs. Advanced packages can create specific namespaces to isolate public and private types of variables, procedures, and functions, thus achieving encapsulated, secure, and high-performance package functionality.

Users must define the package in the following two parts:


- Define Package head
    
    Package head is used to declare public (PUBLIC attribute) members, which can include variables, types, cursors, and subprograms (stored procedures, UDFs). After package creation is successful, public members can be referenced by other external programs.

- Define Package body
    
    Package body is used to define subprograms in public members and to declare and define private (PRIVATE attribute) members. Private members can include variables, types, cursors, and subprograms, and private members can only be used within that package body.

Package head and body are defined through different syntax, and YashanDB does not strictly require the creation order of the same package head and body. Even if the body is defined before the head, creation can still succeed, but the package will throw compilation errors due to the absence of the package head, and will remain unable to execute and call until the package head creation is completed.



Once a package is first called, a global variable area dedicated to that advanced package will be created in the session information. During the same session, invoking the same advanced package allows direct use of the results from the previous execution.

### Trigger

A trigger (TRIGGER) is a PL object in the database. Creating a trigger is equivalent to creating an executable procedure body, but triggers cannot accept parameters and cannot be explicitly called by users. A trigger must be activated by an event, meaning that it runs automatically and implicitly when a specific event occurs; running a trigger is referred to as firing.

A trigger includes the following elements:

- Trigger Operation: The content executed by the trigger, which is a procedure body.

- Trigger Event: An event that can be determined by the system to execute the trigger procedure body, typically DML operations such as INSERT/UPDATE/DELETE on a table. A single trigger event can be defined or a combination of multiple trigger events can be defined (OR logical combination).

- Trigger Timing: The point in time when the trigger procedure body is executed, divided into BEFORE (execute before the trigger event occurs) and AFTER (execute after the trigger event occurs).

- Trigger Object: The object on which the trigger event is based, i.e., a specific table.

- Trigger Type: There are two types: statement-level trigger (executes once when the trigger event occurs) and row-level trigger (executes once for each affected row when the trigger event occurs).

- Trigger Condition: For row-level triggers, a condition expression can be specified using the WHEN statement. The procedure body will only execute if the trigger event occurs and the condition expression evaluates to TRUE.

Defining a trigger on a table allows timely error interception, operation recording, or business logic processing during DML operations on that table. Compared to stored procedures, triggers are more real-time and can capture data information at that moment. However, triggers can have associated impacts on DML operations, such as execution efficiency or DML operation failures due to trigger issues.

In addition to triggers, another functionality that is triggered during DML operations on a table is constraints. Constraints are field-level data checks executed to ensure data integrity, and compared to constraints, the procedural statements used in triggers can achieve more complex data processing.

When executing DML operations on a table that has both constraints and triggers defined, the processing sequence is as follows:

1. Execute BEFORE statement-level trigger.

2. For each affected row of the DML operation:

    1. Execute BEFORE row-level trigger.

    2. Execute DML operation while checking non-FOREIGN KEY constraint items.

    3. Execute AFTER row-level trigger.

    4. Execute FOREIGN KEY check.

3. Execute AFTER statement-level trigger.

### UDT

UDT (User Defined Type) is a user-defined data type used to model real-world entities as objects in the database, which can be used for column definitions in tables and variable type definitions in PL. UDT is similar to object-oriented programming concepts, containing a set of attributes and methods. Users can create new UDT based on the built-in basic types and other UDT types in the database.

UDT includes the following types:

- Object (OBJECT) is a UDT containing attributes and methods (also referred to as Abstract Data Type (ADT) in YashanDB). It is a composite record form, analogous to structures in C language.

- Varying Array (VARRAY) is a collection of elements of the same data type with a size specified at creation. It is comparable to array structures in C language. Each member's data type can be a database built-in type or nested UDT.

- Nested Table (NESTED TABLE) is also a collection, similar to VARRAY in variable declaration, function construction, member declaration, and function calls, but its size does not need to be specified at creation, and it can be associated with the main table.

Variables defined with UDT can be implemented among various PL objects via formal parameter or variable declarations, and can also be inherited through %TYPE.

**Inheritance Type**

In certain usage scenarios, users need to define a variable to receive data but do not care about the data source's type. In such cases, an inheritance type can be used to directly generate a variable of the same type as the data source, without concern for internal details. In PL, %TYPE is mainly used to inherit the data type of a variable, and %ROWTYPE is used to inherit the data type of a record.

### Scheduled Jobs

A scheduled job (JOB) is a background task triggered to execute at specified times.

A scheduled job includes the following basic elements:

- Unique identifier for the JOB
- The task to be executed by the JOB
- The execution time and frequency of the JOB

Based on these three elements, a series of PL language procedures can be used to directly manage a JOB.

After calling an advanced package to create or manage scheduled jobs and various operations, the execution status and various properties of the scheduled jobs can be monitored through configuration parameters or system views.
