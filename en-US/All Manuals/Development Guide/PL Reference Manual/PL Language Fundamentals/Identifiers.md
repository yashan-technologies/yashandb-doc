The PL identifier is used to identify PL syntax units, which include:

- literal
- cursor
- exception
- keyword
- label
- package
- reserved keyword
- procedure body object
- type
- variable

For details, see [Identifiers](../../SQL Reference Manual/Basic SQL Elements/Identifiers).

## Quoted Identifiers

When quoting an identifier, the name used can be the defined identifier name and may also need to include the name of its storage location (such as package name) and schema name.

## Scope and Visibility of Identifiers

The area in which an identifier can be referenced within a PL unit is called the scope of the identifier. The area within a PL unit where an identifier can be referenced without qualification is called the visibility of the identifier.

For a PL unit that declares an identifier, that identifier is a local identifier. If the unit has sub-units, then the identifier is a global identifier for the sub-units. If a sub-unit declares a local identifier with the same name, both identifiers are within their scope in the sub-unit, but only the local identifier is visible without qualification. If the global identifier needs to be referenced, it must be qualified with the name of the declaring unit.

Identifiers declared in other units at the same level are not within their scope and cannot be referenced.

The same identifier cannot be declared twice within the same PL unit. Identifiers can be declared with the same name in two different PL units as independent objects.