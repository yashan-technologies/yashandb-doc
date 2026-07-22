The PL name resolution will introduce how the PL engine resolves ambiguous references to identifiers.

## Qualified Names and Dot Notation

When a name item belongs to another naming item, you can (and sometimes must) use dot notation to qualify the name of the child item with the name of the parent item. For example:

|Reference |Name Qualification |Syntax |
|------------------|--------------------|----------------------------|
| Field of a Record | Record Name        | record_name.field_name     |
| Package Variable   | Package Name       | package_name.var_name      |

If an identifier is declared within a named PL unit, its name can be qualified by using the name of that unit with the following syntax:

*unit_name.simple_identifier_name*

If the identifier is not visible, its name must be qualified. For specifics on identifier visibility, please refer to [Identifiers](Identifiers).

If the identifier belongs to another schema, its name must be qualified with the schema name. For example:

*schema_name.package_name*
