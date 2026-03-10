YashanDB (mysql mode) enables the capability of adjusting system runtime configuration by modifying configuration parameters. During the installation of YashanDB, all configuration parameters are set to their default values. Later, adjustments can be made according to business and performance needs during actual usage.

**Alphabetical Index**

[A](#apara)		[C](#cpara)		[E](#epara)		[I](#ipara)		[M](#mpara)		[N](#npara)		[P](#ppara)		[S](#spara)		[W](#wpara)	

<span id="apara" name="apara" class="yaslink"></span>

### AUTOCOMMIT

*   Parameter type: Boolean

*   Default value: 1

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Whether transactions are automatically committed.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="cpara" name="cpara" class="yaslink"></span>

### CHARACTER\_SET\_CLIENT

*   Parameter type: String

*   Default value: utf8mb4

*   Value range/format: [ASCII|GB18030|GBK|LATIN1|UTF8|UTF8MB3|UTF8MB4]

*   Parameter description: Character set of statements sent by the client.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

### CHARACTER\_SET\_CONNECTION

*   Parameter type: String  

*   Default value: utf8mb4

*   Value range/format: [ASCII|GB18030|GBK|LATIN1|UTF16|UTF8|UTF8MB3|UTF8MB4]

*   Parameter description: Character set used for transmission after the client connects to the database.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

### CHARACTER\_SET\_RESULTS

*   Parameter type: String  

*   Default value: utf8mb4

*   Value range/format: [ASCII|GB18030|GBK|LATIN1|UTF16|UTF8|UTF8MB3|UTF8MB4]

*   Parameter description: Character set used for result sets returned to the client by the server.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="dpara" name="dpara" class="yaslink"></span>

### DEFAULT\_WEEK\_FORMAT

*   Parameter type: Numeric

*   Default value: 0

*   Value range/format: [0,7]

*   Parameter description: Default value of the mode parameter in the [WEEK](../SQL Reference/Built-in Functions/WEEK) function.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="epara" name="epara" class="yaslink"></span>

### EXPLICIT\_DEFAULTS\_FOR\_TIMESTAMP

*   Parameter type: Boolean

*   Default value: OFF  

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Used to control the null value, default value and auto updating constraints for TIMESTAMP data types. The default variable value is OFF, indicating that this field cannot be NULL. If no default value is specified for the field when creating the table, the current system time will be inserted when inserting data, which is consistent with specifying the column attribute `DEFAULT CURRENT_TIMESTAMP`. If multiple TIMESTAMP columns are specified in the same table, only the first TIMESTAMP column will be written with the system time, and subsequent columns will automatically be assigned DEFAULT '0000-00-00 00:00:00'; at the same time, only the first TIMESTAMP column will be added the `ON UPDATE` constraint. Modifying the variable value to ON indicates that the TIMESTAMP type has no automatic generation properties, and all attributes must be explicitly specified.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="ipara" name="ipara" class="yaslink"></span>

### INIT\_CONNECT

*   Parameter type: String  

*   Default value: Not displayed

*   Value range/format: [0,MAX_ALLOWED_PACKET]

*   Parameter description: String executed for each client connection, composed of one or more SQL statements. Please refer to [MAX_ALLOWED_PACKET](#MAX_ALLOWED_PACKET) for MAX_ALLOWED_PACKET.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

### INNODB\_VERSION

*   Parameter type: String  

*   Default value: 5.7.42

*   Default value: NA

*   Parameter description: YashanDB has achieved compatibility with MySQL 5.7.42, and this variable is displayed as ​​5.7.42.

*   Scope: Global, Session

*   Read-only parameter: Yes

*   Modification method: Can't be modified

*   Introduced version: v23.4

### INTERACTIVE\_TIMEOUT

*   Parameter type: String  

*   Default value: 28800

*   Value range/format: [1,31536000]

*   Parameter description: Time in seconds that the server waits for activity before closing interactive connections.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="mpara" name="mpara" class="yaslink"></span>
<span id="MAX_ALLOWED_PACKET" name="MAX_ALLOWED_PACKET" class="yaslink"></span>

### MAX\_ALLOWED\_PACKET

*   Parameter type: Numeric

*   Default value: 4194304  

*   Value range/format: [1024,1073741824]

*   Parameter description: The initial byte size of the data packet message cache. This value should be set to an integer multiple of 1024; non-integer multiples of 1024 will be rounded down to the nearest value.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="npara" name="npara" class="yaslink"></span>

### NET\_WRITE\_TIMEOUT

*   Parameter type: Numeric

*   Default value: 60  

*   Value range/format: [1,31536000]

*   Parameter description: Time in seconds that the server waits before aborting a write operation on the block write connection.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="ppara" name="ppara" class="yaslink"></span>
### PROFILING

*   Parameter type: Boolean

*   Default value: 0  

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: This parameter is used to enable the feature of SQL performance tuning.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4


<span id="spara" name="spara" class="yaslink"></span>

<span id="sql_mode" name="sql_mode" class="yaslink"></span>

### SQL\_MODE

*   Parameter type: String  

*   Default value: "ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_BACKSLASH_ESCAPES,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL"

*   Value range/format: A string of subsets of {ANSI_QUOTES, REAL_AS_FLOAT, PIPES_AS_CONCAT, ALLOW_INVALID_DATES, ERROR_FOR_DIVISION_BY_ZERO, HIGH_NOT_PRECEDENCE, IGNORE_SPACE, NO_AUTO_CREATE_USER, NO_AUTO_VALUE_ON_ZERO, NO_BACKSLASH_ESCAPES, NO_DIR_IN_CREATE, NO_ENGINE_SUBSTITUTION, NO_UNSIGNED_SUBTRACTION, NO_ZERO_DATE, NO_ZERO_IN_DATE, ONLY_FULL_GROUP_BY, PAD_CHAR_TO_FULL_LENGTH, STRICT_ALL_TABLES, STRICT_TRANS_TABLES, TIME_TRUNCATE_FRACTIONAL} connected by commas.

*   Parameter description: Set the SQL mode.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="vpara" name="vpara" class="yaslink"></span>

### VALIDATE\_PASSWORD\_CHECK\_USER\_NAME

*   Parameter type: Boolean  

*   Default value: OFF

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Whether to compare the password with the username part of valid users in the current session; if they match, it is rejected.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="VALIDATE_PASSWORD_DICTIONARY_FILE" name="VALIDATE_PASSWORD_DICTIONARY_FILE" class="yaslink"></span>

### VALIDATE\_PASSWORD\_DICTIONARY\_FILE

*   Parameter type: String  

*   Default value: Empty

*   Value range/format: Valid file path, starting with ? or ., set to YASDB_DATA directory, and the path cannot contain ../

*   Parameter description: Specify the path of the dictionary file that records banned password words, which is a list of words not allowed as part of the password.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="validate_password_length" name="validate_password_length" class="yaslink"></span>

### VALIDATE\_PASSWORD\_LENGTH

*   Parameter type: Numeric

*   Default value: 8

*   Value range/format: [0,2147483647]

*   Parameter description: Specify the minimum length of the password.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="VALIDATE_PASSWORD_MIXED_CASE_COUNT" name="VALIDATE_PASSWORD_MIXED_CASE_COUNT" class="yaslink"></span>

### VALIDATE\_PASSWORD\_MIXED\_CASE\_COUNT

*   Parameter type: Numeric

*   Default value: 1

*   Value range/format: [0,2147483647]

*   Parameter description: Specify the minimum number of uppercase and lowercase letters required in the password.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="VALIDATE_PASSWORD_NUMBER_COUNT" name="VALIDATE_PASSWORD_NUMBER_COUNT" class="yaslink"></span>

### VALIDATE\_PASSWORD\_NUMBER\_COUNT

*   Parameter type: Numeric

*   Default value: 1

*   Value range/format: [0,2147483647]

*   Parameter description: Specify the minimum number of digits required in the password.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="VALIDATE_PASSWORD_POLICY" name="VALIDATE_PASSWORD_POLICY" class="yaslink"></span>

### VALIDATE\_PASSWORD\_POLICY

*   Parameter type: Numeric

*   Default value: 1

*   Value range/format: [0|1|2]

*   Parameter description: Specify the password policy executed during password validation:

    * 0: Only checks password length requirements, with a default specification that passwords must be at least 8 characters long. To adjust this specification, modify the value of [VALIDATE_PASSWORD_LENGTH](#validate_password_length).

    * 1: Based on the 0/LOW policy, it adds password character requirements, with a default requirement that passwords must contain 1 digit character, 1 lowercase letter, 1 uppercase letter, and 1 special (non-digit) character. To adjust this requirement, modify the values of [VALIDATE_PASSWORD_NUMBER_COUNT](#VALIDATE_PASSWORD_NUMBER_COUNT), [VALIDATE_PASSWORD_MIXED_CASE_COUNT](#VALIDATE_PASSWORD_MIXED_CASE_COUNT), and [VALIDATE_PASSWORD_SPECAIL_CHAR_COUNT](#VALIDATE_PASSWORD_SPECAIL_CHAR_COUNT).

    * 2: Based on the 1/MEDIUM policy, it adds checks for matches between password substrings and dictionary files. Password substrings of length 4 or longer must not match words in the dictionary file. To specify the dictionary file, modify [VALIDATE_PASSWORD_DICTIONARY_FILE](#VALIDATE_PASSWORD_DICTIONARY_FILE).

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="VALIDATE_PASSWORD_SPECAIL_CHAR_COUNT" name="VALIDATE_PASSWORD_SPECAIL_CHAR_COUNT" class="yaslink"></span>

### VALIDATE\_PASSWORD\_SPECAIL\_CHAR\_COUNT

*   Parameter type: Numeric 

*   Default value: 1

*   Value range/format: [0,2147483647]

*   Parameter description: Specify the minimum number of special characters required in the password.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="wpara" name="wpara" class="yaslink"></span>

### WAIT\_TIMEOUT

*   Parameter type: Numeric  

*   Default value: 28800

*   Value range/format: [1,31536000]

*   Parameter description: Time in seconds that the server waits for activity before closing non-interactive connections.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3