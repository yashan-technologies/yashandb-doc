```ebnf
convert = CONVERT "(" expr "," dest_char_set ["," source_char_set] ")".
```
The CONVERT function is used to convert the expression [expr](../General SQL Syntax/expr) from the source_char_set character set to the dest_char_set character set.

This function follows the rules below:

- This function is only applicable to HEAP tables.

- This function involves character set conversion. The result is accurate when used with lengthb; however, when nested with other functions (such as length, substr, etc.), it may cause garbled text or conversion errors, resulting in inaccurate outcomes.

**expr**

Original data, general expression.

- If expr is of CLOB type, the return value is of CLOB type. If expr is of NCLOB type, the return value is of NCLOB type. If expr is of NCHAR/NVARCHAR type, the return value is of NVARCHAR type. In other cases, the return value is of VARCHAR type.

- If expr is NULL, the function returns NULL.

**dest_char_set**

Target character set, general expression.

- It must be a character set supported by YashanDB [Character Set Configuration](../../../Database Administration/Instance Management/Database Character Set Configuration); otherwise, an error will be raised.

- If dest_char_set is NULL, the function returns NULL.

**source_char_set**

Initial character set, general expression.

- It must be a character set supported by YashanDB [Character Set Configuration](../../../Database Administration/Instance Management/Database Character Set Configuration); otherwise, an error will be raised.

- If source_char_set is omitted, it defaults to the current database character set.

- If source_char_set is NULL, the function returns NULL.

***Example*** for Heap tables

```plsql
set serveroutput on;
declare
a varchar(1000) := '——';
begin
dbms_output.put_line(lengthb(a));
a := convert(a,'zhs16gbk','al32utf8');
dbms_output.put_line(lengthb(a));
end;
/

--result
6
4
```
