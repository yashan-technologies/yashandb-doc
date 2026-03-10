This view displays the information of built-in functions that are additionally provided by the current system in mysql mode. For the detailed list, please refer to the development manual [Built-in Functions](../../../Reference Manual of mysql Mode/SQL Reference/Built-in Functions/00Built-in Functions).

In mysql mode, the built-in functions displayed in this view will override the functions with the same name in the V$FUNCTION view.

|Field |Type |Description |
| --- | --- |---------------------------|
| ID    | SMALLINT | Function ID                        |
| NAME  | VARCHAR(64) | Function name                    |
| ISAGGR| VARCHAR(1) | Is it an aggregate function <br>\* Y: Yes<br>\* N: No |