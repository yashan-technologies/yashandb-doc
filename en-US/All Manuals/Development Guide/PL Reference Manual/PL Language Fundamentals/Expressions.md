An expression is a combination of one or more values, operators, and SQL functions that results in a single value.

An expression always returns a unique value.

Operands can be variables, constants, literals, operator calls, function calls, placeholders, or other expressions. Therefore, expressions can be arbitrarily complex.

The data type of the operands determines the data type of the expression. Each time an expression is evaluated, a single value of that data type is produced. The data type of the result is the data type of the expression.

Expressions may be used in various places within a procedure body, such as in the actual parameters of stored procedures/functions, variable assignments, SQL statements, etc. These expressions adhere to the [General Expression](../../SQL Reference Manual/General SQL Syntax/expr) syntax and rules of YashanDB, while also imposing the following restrictions:

- When used as a column field, an expression cannot use * to represent all column fields.
- Window functions cannot be used within an expression.
- Expressions in non-SELECT DML statements cannot use pseudocolumns such as ROWNUM, ROWID, ROWSCN, nor can they use aggregate functions.