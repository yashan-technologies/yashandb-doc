The PL name resolution will introduce how the PL engine resolves ambiguous references to identifiers.

## Qualified Names and Dot Notation

When a name item belongs to another naming item, you can use dot notation to qualify the name of the child item with the name of the parent item. 

|Common Usage Scenarios |Syntax |Usage Instructions |
|------------------|----------------------------|--------------------|
| Accessing members of a RECORD | record.member | It is limited by the record name. <br />One or more whitespace characters are allowed before and after the dot symbol. Whitespace characters include half - width/full - width spaces, tab character `\t`, carriage return character `\r`, line feed character `\n`, and null character `\0`. For example, `record .member` |
| Accessing members of an array element | array(i).member | It is limited by the array name. <br />One or more whitespace characters are allowed before and after the dot symbol. Whitespace characters include half - width/full - width spaces, tab character `\t`, carriage return character `\r`, line feed character `\n`, and null character `\0`. |
| Accessing members of a RECORD returned by a function | func(arg).member | It is limited by the record name. <br />One or more whitespace characters are allowed before and after the dot symbol. Whitespace characters include half - width/full - width spaces, tab character `\t`, carriage return character `\r`, line feed character `\n`, and null character `\0`. |
| Accessing members of a multi - dimensional array | array(i)(j).member | It is limited by the array name. <br />One or more whitespace characters are between an identifier and parentheses, between parentheses, as well as before and after the dot symbol. Whitespace characters include half - width/full - width spaces, tab character `\t`, carriage return character `\r`, line feed character `\n`, and null character `\0`. For example, `array  (  i  )  (  j  ) . member` |
| Package Variable   | package_name.var_name      | It is limited by the package name. |

If an identifier is declared within a named PL unit, its name can be qualified by using the name of that unit with the following syntax:

*unit_name.simple_identifier_name*

If the identifier is not visible, its name must be qualified. For specifics on identifier visibility, please refer to [Identifiers](Identifiers).

If the identifier belongs to another schema, its name must be qualified with the schema name. For example:

*schema_name.package_name*
