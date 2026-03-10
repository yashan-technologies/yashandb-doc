This view displays method information of OBJECT type in all UDTs (User Defined Types).

|Field |Type |Description |
| --- | --- |-------------------------------------------------------------|
| OWNER        | VARCHAR(64) | The username to which the UDT belongs                       |
| TYPE_NAME    | VARCHAR(64) | The name of the UDT                                         |
| METHOD_NAME  | VARCHAR(68) | The name of the method                                      |
| METHOD_NO    | INTEGER     | The ordinal number of the method, starting from 1         |
| METHOD_TYPE  | VARCHAR(6)  | The type of the method<br>  * MAP: mapping function <br>  * ORDER: sorting function <br>  * PUBLIC: public function  |
| PARAMETERS    | INTEGER     | The number of parameters for the method                     |
| RESULTS      | INTEGER     | The number of return values for the method                  |
| FINAL        | VARCHAR(3)  | Whether the method is final                                  |
| INSTANTIABLE | VARCHAR(3)  | Whether the method is instantiable                           |
| OVERRIDING   | VARCHAR(3)  | Whether the method is overriding                              |
| INHERITED    | VARCHAR(3)  | Whether the method is inherited                              |