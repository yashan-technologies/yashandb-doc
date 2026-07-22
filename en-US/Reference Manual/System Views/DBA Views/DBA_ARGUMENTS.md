PL view, showing parameter information of the procedure body.

|Field |Type |Description |
|----------------------|--------------|------------------------------------------------------------------|
| OWNER                 | VARCHAR(64)   | Username of the parameter owner                                       |
| OBJECT_NAME            | VARCHAR(64)   | Name of the procedure body or function, or package name            |
| SUBPROGRAM_NAME        | VARCHAR(68)   | Name of the sub-procedure or function in the package the parameter belongs to<sup>*</sup> |
| PACKAGE_NAME           | VARCHAR(64)   | Name of the package the parameter belongs to (for UDT OBJECT method, this value is the UDT name) |
| OBJECT_ID              | BIGINT        | Object ID of the parameter                                           |
| OVERLOAD              | VARCHAR(1)    | Overload identifier for the parameter when there are overloaded names<sup>*</sup> |
| SUBPROGRAM_ID         | INTEGER       | ID of the function the parameter belongs to in the associated advanced package<sup>*</sup> |
| ARGUMENT_NAME         | VARCHAR(68)   | Name of the parameter                                                |
| POSITION              | INTEGER       | The position of the parameter in the parameter list, returns 0 for return values, non-return value parameters start from 1 |
| SEQUENCE              | BIGINT        | The sequence of the parameter in the parameter list, starting from 1 (including return values) |
| DATA_LEVEL            | INTEGER       | Nested depth of composite type parameters<sup>(1)</sup>            |
| DATA_TYPE             | VARCHAR(64)   | Data type of the parameter                                          |
| DEFAULTED             | VARCHAR(1)    | Indicates whether the parameter has a default value                  |
| DEFAULT_VALUE         | CLOB          | The default value of the parameter                                   |
| DEFAULT_LENGTH        | BIGINT        | Length of the default value (character length)                      |
| IN_OUT                | VARCHAR(6)    | Direction of the parameter                                          |
| DATA_LENGTH           | INTEGER       | Length of the parameter (character length)                           |
| DATA_PRECISION        | INTEGER       | Precision of NUMBER type data                                       |
| DATA_SCALE            | INTEGER       | Scale of NUMBER type data                                           |
| RADIX                 | VARCHAR(11)   | Radix, returns 10 for TINYINT/SMALLINT/INTEGER/BIGINT/NUMBER types, returns 2 for BIT type, otherwise returns empty |
| CHARACTER_SET_NAME    | VARCHAR(9)    | Character set used by the parameter                                  |
| TYPE_OWNER            | VARCHAR(68)   | Owner user of the parameter type<sup>*</sup>                       |
| TYPE_NAME             | VARCHAR(68)   | Name of the parameter type. If the type is a package local type (i.e., declared in the package specification), this column shows the name of the package<sup>*</sup> |
| TYPE_SUBNAME          | VARCHAR(1)    | When the package is a local type, this column shows the type name<sup>*</sup> |
| TYPE_LINK             | VARCHAR(1)    | When the package indicated in the TYPE_NAME column is a remote package, this column shows the database link used to reference the remote package, only relevant for package local types<sup>*</sup> |
| TYPE_OBJECT_TYPE      | VARCHAR(1)    | Displays the name of the type described by TYPE_NAME, TYPE_SUBNAME<sup>*</sup> |
| PLS_TYPE              | VARCHAR(64)   | PL type of the parameter<sup>(2)</sup>                              |
| CHAR_LENGTH           | VARCHAR(1)    | When related to string types, shows the length limit of this type<sup>*</sup> |
| CHAR_USED             | VARCHAR(1)    | Indicates whether the string is displayed in bytes or character limit<sup>*</sup> |
| ORIGIN_CON_ID         | VARCHAR(1)    | Original line number of the container<sup>*</sup><sup>(3)</sup>    |

Columns with a * are always empty or N, used for functionality reservation.

(1) This column is always 0.

(2) The PL types supported by YashanDB are consistent with its own held types.

(3) This column is the same as DBA_PROCEDURES.