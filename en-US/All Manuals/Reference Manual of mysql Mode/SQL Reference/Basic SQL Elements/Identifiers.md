
YashanDB includes two types of identifiers:

- As syntax keywords, they are used to delimit the positions of the SQL syntax tree; as system [reserved syntax keywords](../../../Reference Manual/Reserved Keywords), they cannot be used as names for database objects.
- As names for database objects, they are used for table names, column names, aliases, etc., but must comply with the following naming conventions.


When naming database objects, the following rules and constraints must be followed:

*   Supports all case-sensitive letters and digits, and is case insensitive.
*   Cannot contain special characters such as '\\0', ',', ' ', '+', '-', '\*', '/', '|', '(', '%', ':', '?', '.', '\\t', '\\r', '\\n', '=', '\\\\', '!', '>', '<', ';', '&', '^', '"', '~', ''', '{', '}', '[', ']', etc.
*   System reserved syntax keywords cannot be used as names (case insensitive).
*   When the ANSI_QUOTES configuration option is enabled in [SQL_MODE](../SQL_MODE), both double quotes and backticks will be interpreted as identifier quotation symbols, while single quotes are used for string quotation symbols; when ANSI_QUOTES is not enabled, double quotes will be interpreted as string quotation symbols, and backticks will be interpreted as identifier quotation symbols.

