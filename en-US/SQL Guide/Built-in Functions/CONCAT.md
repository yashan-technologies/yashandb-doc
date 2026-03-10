```ebnf+diagram
concat::= CONCAT "(" expr "," (expr) {"," (expr)} ")"
```

The CONCAT function concatenates the values of multiple [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) into a single string, equivalent to `expr||expr||……`.

This function follows these rules:

- The function's parameters must be between [2,65535].

- In the vectorized execution engine, expr cannot be LOB type data stored out of line.

- For non-character type/CLOB type expr values, the function will first convert them to VARCHAR type (conversion failure will return an error).

- When expr value is NULL, the function will convert it to a VARCHAR string of length 0.

- The data type of the return value is as follows:

  - When there are no CLOB/NCLOB types in expr values:

    - If only CHAR/NCHAR type data is present, the function returns a CHAR/NCHAR type string.

    - If only CHAR and VARCHAR type data is present, the function returns a VARCHAR type string.

    - If only CHAR and NCHAR type data is present, the function returns an NCHAR type string.

    - If NVARCHAR type data is present, the function returns an NVARCHAR type string.

    - If NVARCHAR type data is not present but VARCHAR and NCHAR types exist, the function returns an NVARCHAR type string.
  
    - If expr contains BFILE type data, it will be implicitly converted to VARCHAR type, and then the function will determine the return type according to the above rules.

  - When expr values contain CLOB or NCLOB, the return data type is determined by the following three rules:

    - Rule 1: When there is only one CLOB in the parameter list and no NCHAR or NVARCHAR data before it, return CLOB; otherwise, return NCLOB.

    - Rule 2: When there is only one NCLOB in the parameter list, if there is NCHAR or NVARCHAR data before it or no other parameters, return NCLOB; otherwise, return CLOB.

    - Rule 3: When there are multiple CLOB or NCLOB, take the first CLOB or NCLOB from left to right in the parameter list to apply Rule 1 or Rule 2.

***Example***

```sql
SELECT CONCAT(employee_name,' has joined us for ',CEIL(SYSDATE-entry_date),' days.') res FROM employees;
RES                                                               
----------------------------------------------------------------
Mask has joined us for 1001 days.                              
John has joined us for 2001 days.                              
Anna has joined us for 301 days.                               
Jack has joined us for 701 days.                               
Jim has joined us for 201 days.
```
