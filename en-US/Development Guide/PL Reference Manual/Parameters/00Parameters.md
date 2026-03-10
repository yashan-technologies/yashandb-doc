In programming languages, parameters are used to pass values or variables from the outside to a program block. Parameters in YashanDB PL are applied in the following program blocks:

- **Stored procedures/functions**, including standalone stored procedures/functions or those defined in higher-level packages, UDTs, and other PL objects.

    When creating or recreating stored procedures/functions, errors in the parameters will still create or recreate the object, but subsequent calls will return errors.

    Stored procedures/functions do not necessarily require parameters; using parameters allows controlling their content during the call to achieve specific goals.

    In such cases, parameters must be declared (formal parameters) when defining the stored procedure/function, and values or variables must be passed (actual parameters) when calling the stored procedure/function.

- **Dynamic SQL** executed in an [EXECUTE Statement](../PL Statements/EXECUTE Statement) or an [OPEN Statement](../PL Statements/OPEN Statement).

    Dynamic SQL is SQL statements constructed at runtime. Using parameters allows the constructed SQL statement to be templated, simplifying PL programming, and enables the optimizer to generate execution plans based on SQL statement templates (combined with variable peeking), avoiding hard parsing for each run, thus significantly improving SQL execution performance.

    In such cases, parameters need to be bound using placeholders in the SQL statement (equivalent to formal parameters in stored procedures/functions) and values or variables must be passed using the USING syntax (equivalent to actual parameters in stored procedures/functions).

- **Cursor definitions**.

    When defining a cursor, parameter declarations (formal parameters) can be made, and when using the cursor in an [OPEN Statement](../PL Statements/OPEN Statement), values or variables can be passed (actual parameters).

> **Note**: 
>
> If there are no special instructions regarding the rules, not providing parameters or providing NULL in PL statements indicates that no processing will be done for this parameter.