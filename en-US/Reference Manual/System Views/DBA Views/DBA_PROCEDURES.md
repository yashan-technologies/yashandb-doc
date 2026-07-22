PL view, showing information related to procedures.

|Field |Type |Description |
| --- |-------------| --- |
| OWNER            | VARCHAR(64)   | User to which the object belongs  |
| OBJECT_NAME      | VARCHAR(64)   | Object name                       |
| PROCEDURE_NAME   | VARCHAR(68)   | Name of the subordinate procedure  |
| OBJECT_ID        | BIGINT        | Object ID                         |
| SUBPROGRAM_ID    | INTEGER       | ID of the object in the parent package |
| OBJECT_TYPE      | VARCHAR(17)   | Object type                       |
| AGGREGATE        | VARCHAR(1)    | Whether the object is an aggregate function |
| PIPELINED        | VARCHAR(1)    | Whether the object is a pipelined function |
| IMPLTYPEOWNER    | VARCHAR(1)    | User of the implementation type <sup>*</sup> |
| IMPLTYPENAME     | VARCHAR(1)    | Name of the implementation type <sup>*</sup> |
| PARALLEL         | CHAR(1)       | Whether it can be executed in parallel <sup>*</sup> |
| INTERFACE        | CHAR(1)       | Whether it is a function implemented using an interface <sup>*</sup> |
| DETERMINISTIC    | VARCHAR(1)    | Whether it is a deterministic function |
| AUTHID           | VARCHAR(12)   | Function privilege checking mode   |
| RESULT_CACHE     | CHAR(1)       | Whether it is a result buffer function <sup>*</sup> |
| ORIGIN_CON_ID    | VARCHAR(1)    | Original row number of the container <sup>*</sup> |
| POLYMORPHIC      | VARCHAR(1)    | Whether it is a polymorphic table function <sup>*</sup> |

The columns marked with an asterisk (*) are reserved columns, and their internal values are always empty or N.