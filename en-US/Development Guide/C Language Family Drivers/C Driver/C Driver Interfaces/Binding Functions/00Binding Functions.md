In the C driver, when connecting to the database using the database driver, parameters can be passed to the SQL statement through the binding parameters functionality, which not only reduces the risk of SQL injection but also improves the execution efficiency of the SQL statement.

Binding parameters refer to the process of binding variables in the program to placeholders in the SQL statement, and then passing the values of these variables to the SQL statement for execution. To use the binding parameters functionality, placeholders in the SQL statement must be defined first, and then the program variables are bound to the placeholders.

|Interface Name |Interface Description |
|--------------------|--------------------------------------|
| [yacBindColumn](yacBindColumn)      | Binds and assigns attributes to the specified column on the client side        |
| [yacBindParameter](yacBindParameter)   | Binds and assigns attributes to the specified parameter on the client side              |
| [yacBindParameterByName](yacBindParameterByName)   | Binds and assigns attributes to the specified parameter by name on the client side              |