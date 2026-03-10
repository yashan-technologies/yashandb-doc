The data types and their specifications that can be used in the mysql mode of YashanDB are shown in the table below.

|Data Type |Type Length |Value Range |Description |
|-----------------------| ------------- |------------------------------------------------------------------------------------------------------------------------------|-------------------------------------|
| TINYINT    | 1Bytes            | -128 ~ 127                                                                                                                   | Signed integer type of 1B.          |
| TINYINT UNSIGNED      | 1Bytes            | 0 ~ 255                                                                                                                      | Unsigned integer type of 1B.        |
| SMALLINT   | 2Bytes            | -2<sup>15</sup> (-32,768)  ~ 2<sup>15</sup> - 1 (32,767)                                                                     | Signed integer type of 2B.          |
| SMALLINT UNSIGNED  | 2Bytes            | 0  ~ 2<sup>16</sup>  (65,535)                                                                                                | Unsigned integer type of 2B.        |
| MEDIUMINT  | 4Bytes            | -2<sup>23</sup> (-8,388,608)  ~ 2<sup>23</sup> - 1 (8,388,607)                                                               | 3B signed integer type unique to MYSQL. |
| INTEGER    | 4Bytes            | -2<sup>31</sup> (-2,147,483,648) ~ 2<sup>31</sup> - 1 (2,147,483,647)                                                        | Signed integer type of 4B.          |
| INTEGER UNSIGNED   | 4Bytes            | 0 ~ 2<sup>32</sup> - 1 (4,294,967,295)                                                                                       | Unsigned integer type of 4B.        |
| BIGINT     | 8Bytes            | -2<sup>63</sup> (-9,223,372,036,854,775,808) ~ 2<sup>63</sup>-1 (9,223,372,036,854,775,807)                                  | Signed integer type of 8B.          |
| BIGINT UNSIGNED    | 8Bytes            | 0  ~ 2<sup>64</sup> - 1 (18,446,744,073,709,551,615)                                                                         | Unsigned integer type of 8B.        |
| FLOAT      | 4Bytes            | 32-bit single precision floating point<br>Negative: -3.402823E38 ~ -1.401298E-45<br>Positive: 1.401298E-45 ~ 3.402823E38, 0 | Represents single precision floating point. The maximum number of digits is specified in parentheses, and the maximum number of digits to the right of the decimal point is specified in the d parameter.<br>BINARY_FLOAT/REAL is an alias for FLOAT, and behaves exactly the same as FLOAT. |
| DOUBLE     | 8Bytes            | 64-bit double precision floating point<br>Negative: -1.797693134862315807E308 ~ -4.94065645841247E-324<br>Positive: 4.94065645841247E-324 ~ 1.797693134862315807E308, 0 | Represents double precision floating point.<br/>BINARY_DOUBLE is an alias for DOUBLE, and behaves exactly the same as DOUBLE.         |
| DECIMAL    | 20Bytes           | NUMBER(p,s)<br>p: 1 ~ 38<br>s: 0 ~ 30, and s must be less than or equal to p                                                     | Used for calculations requiring very high precision, stored in binary format. Therefore, the actual storage length and the expressed range of data need to be converted.<br><br>DECIMAL/NUMERIC is an alias for NUMBER, and behaves exactly the same as NUMBER.          |
| CHAR(SIZE)            | SIZE*maximum character width<br />Maximum value cannot exceed 255 | None                                                                                                                       | CHARACTER is an alias for CHAR, and behaves exactly the same as CHAR.           |
| VARCHAR(SIZE)         | SIZE*maximum character width<br>Maximum value cannot exceed 65534  | None                                                                                                                            | CHARACTER VARYING is an alias for VARCHAR, and behaves exactly the same as VARCHAR.         |
| TINYTEXT     | 0-255 bytes         | None            | Comparison for this data type is currently not supported.   |
| TEXT       | 0-65535 bytes       | None      | Comparison for this data type is currently not supported. |
| MEDIUMTEXT | 0-16777215 bytes    | None        | Comparison for this data type is currently not supported. |
| LONGTEXT     | 0-4294967295 bytes  | None       | Comparison for this data type is currently not supported.  |
| TINYBLOB     | 0-255 bytes         | None         | None                |
| BLOB       | 0-65535 bytes       | None      | None                |
| MEDIUMBLOB | 0-16777215 bytes    | None      | None                |
| LONGBLOB     | 0-4294967295 bytes  | None        | None                |
| DATE       | 8Bytes            | 1-1-1 ~ 9999-12-31       | YYYY-MM-DD     |
| TIME       | 8Bytes            | -838:59:59.999999 ~ 838:59:59.999999       | Hours, minutes, seconds, microseconds           |
| TIMESTAMP  | 8Bytes            | 1-1-1 00:00:00.000000 ~ 9999-12-31 23:59:59.999999         | YYYY-MM-DD HH24:MI:SS.FF<br>Microsecond precision range is [0,6], default precision is 0. |
| BINARY(SIZE)          | Number of bytes<br />Maximum value cannot exceed 255 | None        | Stores binary format data, and displays the binary format data as a string when printed; when inserting data, if the length is less than SIZE, the data will be padded with 0x00 to reach SIZE length.                |
| VARBINARY(SIZE)       | Number of bytes<br />Maximum value cannot exceed 65534 | None         | Stores binary format data, and displays the binary format data as a string when printed; during data insertion, no 0x00 padding is used.                |