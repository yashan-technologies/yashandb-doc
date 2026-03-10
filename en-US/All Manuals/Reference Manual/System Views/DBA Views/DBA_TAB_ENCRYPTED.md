This view displays the encryption information of the table.

|Field |Type |Description |
| --- | --- | --- |
| OWNER       | VARCHAR(64)| Username of the object that owns the encrypted table |
| TABLE_NAME  | VARCHAR(64)| Table name                            |
| TABLE_TYPE  | VARCHAR(8) | Type of the table<br>* HEAP<br>* TAC<br>* LSC |
| ENC_ALG     | VARCHAR(6) | Table encryption algorithm            |
| ENC_KEYVER  | INTEGER    | Table encryption key version          |