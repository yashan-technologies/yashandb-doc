YashanDB (mysql mode) enables the capability of adjusting system runtime configuration by modifying configuration parameters. During the installation of YashanDB, all configuration parameters are set to their default values. Later, adjustments can be made according to business and performance needs during actual usage.

**Alphabetical Index**

[A](#apara)		[C](#cpara)		[D](#dpara)		[E](#epara)		[F](#fpara)		[G](#gpara)		[I](#ipara)		[L](#lpara)		[M](#mpara)		[N](#npara)		[P](#ppara)		[Q](#qpara)		[S](#spara)		[T](#tpara)		[V](#vpara)		[W](#wpara)	

<span id="apara" name="apara"></span>

### AUTOCOMMIT

*   Parameter type: Boolean

*   Default value: 1

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Whether transactions are automatically committed.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

### AUTO_INCREMENT_INCREMENT

*   Parameter type: Numeric

*   Default value: 1

*   Value range/format: [1,65535]

*   Parameter description: Controls the interval (step) between consecutive values in an auto-increment column. 

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="cpara" name="cpara"></span>

### CHARACTER_SET_CLIENT

*   Parameter type: String

*   Default value: utf8mb4

*   Value range/format: [ASCII|GB18030|GBK|LATIN1|UTF8|UTF8MB3|UTF8MB4]

*   Parameter description: Character set of statements sent by the client.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

### CHARACTER_SET_CONNECTION

*   Parameter type: String  

*   Default value: utf8mb4

*   Value range/format: [ASCII|GB18030|GBK|LATIN1|UTF16|UTF8|UTF8MB3|UTF8MB4]

*   Parameter description: Character set used for transmission after the client connects to the database.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

### CHARACTER_SET_RESULTS

*   Parameter type: String

*   Default value: utf8mb4

*   Value range/format: [ASCII|GB18030|GBK|LATIN1|UTF16|UTF8|UTF8MB3|UTF8MB4]

*   Parameter description: Character set used for result sets returned to the client by the server.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

### CHARACTER_SET_DATABASE

*   Parameter type: String

*   Default value: utf8mb4

*   Value range/format: [ASCII|GB18030|GBK|LATIN1|UTF8|UTF8MB3|UTF8MB4]

*   Parameter description: Character set used by the default database.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### CHARACTER_SET_SERVER

*   Parameter type: String

*   Default value: utf8mb4

*   Value range/format: [ASCII|GB18030|GBK|LATIN1|UTF8|UTF8MB3|UTF8MB4]

*   Parameter description: Server default character set.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### COLLATION_CONNECTION

*   Parameter type: String

*   Default value: utf8mb4_general_ci

*   Value range/format: Collation corresponding to the character set

*   Parameter description: Collation for the connection character set.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### COLLATION_DATABASE

*   Parameter type: String

*   Default value: utf8mb4_general_ci

*   Value range/format: Collation corresponding to the character set

*   Parameter description: Collation for the default database.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### COLLATION_SERVER

*   Parameter type: String

*   Default value: utf8mb4_general_ci

*   Value range/format: Collation corresponding to the character set

*   Parameter description: Server default collation.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="dpara" name="dpara"></span>

### DATADIR

*   Parameter type: String

*   Default value: YASDB_DATA

*   Value range/format: Valid path

*   Parameter description: Data directory path.

*   Scope: Global

*   Read-only parameter: Yes

*   Modification method: Can't be modified

*   Introduced version: v23.3

### DEFAULT_WEEK_FORMAT

*   Parameter type: Numeric

*   Default value: 0

*   Value range/format: [0,7]

*   Parameter description: Default value of the mode parameter in the [WEEK](../SQL Reference/Built-in Functions/WEEK) function.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="epara" name="epara"></span>

### EXPLICIT_DEFAULTS_FOR_TIMESTAMP

*   Parameter type: Boolean

*   Default value: 0  

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Used to control the null value, default value and auto updating constraints for TIMESTAMP data types. The default variable value is OFF, indicating that this field cannot be NULL. If no default value is specified for the field when creating the table, the current system time will be inserted when inserting data, which is consistent with specifying the column attribute `DEFAULT CURRENT_TIMESTAMP`. If multiple TIMESTAMP columns are specified in the same table, only the first TIMESTAMP column will be written with the system time, and subsequent columns will automatically be assigned DEFAULT '0000-00-00 00:00:00'; at the same time, only the first TIMESTAMP column will be added the `ON UPDATE` constraint. Modifying the variable value to ON indicates that the TIMESTAMP type has no automatic generation properties, and all attributes must be explicitly specified.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="fpara" name="fpara"></span>

### FOREIGN_KEY_CHECKS

*   Parameter type: Boolean

*   Default value: 1

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Whether to enable foreign key constraint checks.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="gpara" name="gpara"></span>

### GTID_MODE

*   Parameter type: Enumeration

*   Default value: OFF

*   Value range/format: [OFF|ON]

*   Parameter description: Whether to enable GTID (Global Transaction Identifier) mode.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="ipara" name="ipara"></span>

### INIT_CONNECT

*   Parameter type: String  

*   Default value: Not displayed

*   Value range/format: [0,MAX_ALLOWED_PACKET]

*   Parameter description: String executed for each client connection, composed of one or more SQL statements. Please refer to [MAX_ALLOWED_PACKET](#MAX_ALLOWED_PACKET) for MAX_ALLOWED_PACKET.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

### INNODB_VERSION

*   Parameter type: String  

*   Default value: 5.7.44

*   Default value: NA

*   Parameter description: YashanDB has achieved compatibility with MySQL 5.7.44, and this variable is displayed as ​​5.7.44.

*   Scope: Global, Session

*   Read-only parameter: Yes

*   Modification method: Can't be modified

*   Introduced version: v23.4

### INTERACTIVE_TIMEOUT

*   Parameter type: String  

*   Default value: 28800

*   Value range/format: [1,31536000]

*   Parameter description: Time in seconds that the server waits for activity before closing interactive connections.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="lpara" name="lpara"></span>

### LICENSE

*   Parameter type: String

*   Default value: COMMERCIAL

*   Value range/format: NA

*   Parameter description: Database license type.

*   Scope: Global

*   Read-only parameter: Yes

*   Modification method: Can't be modified

*   Introduced version: v23.4

### LOWER_CASE_FILE_SYSTEM

*   Parameter type: Boolean

*   Default value: FALSE

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Whether the file system is case-sensitive.

*   Scope: Global

*   Read-only parameter: Yes

*   Modification method: Can't be modified

*   Introduced version: v23.4

### LOWER_CASE_TABLE_NAMES

*   Parameter type: Numeric

*   Default value: 1

*   Value range/format: [0|1]

*   Parameter description: Whether table names are case-sensitive. 0 means case-sensitive, 1 means case-insensitive.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="mpara" name="mpara"></span>
<span id="MAX_ALLOWED_PACKET" name="MAX_ALLOWED_PACKET"></span>

### MAX_ALLOWED_PACKET

*   Parameter type: Numeric

*   Default value: 1073741824

*   Value range/format: [1024,1073741824]

*   Parameter description: The initial byte size of the data packet message cache. This value should be set to an integer multiple of 1024; non-integer multiples of 1024 will be rounded down to the nearest value.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="npara" name="npara"></span>

### NET_BUFFER_LENGTH

*   Parameter type: Numeric

*   Default value: 16384

*   Value range/format: [1024,1048576]

*   Parameter description: Network buffer length.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

### NET_READ_TIMEOUT

*   Parameter type: Numeric

*   Default value: 30

*   Value range/format: [1,31536000]

*   Parameter description: Time in seconds that the server waits for reading before aborting.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

### NET_WRITE_TIMEOUT

*   Parameter type: Numeric

*   Default value: 60  

*   Value range/format: [1,31536000]

*   Parameter description: Time in seconds that the server waits before aborting a write operation on the block write connection.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="ppara" name="ppara"></span>

### PERFORMANCE_SCHEMA

*   Parameter type: Boolean

*   Default value: 1

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Whether to enable performance schema.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### PROFILING

*   Parameter type: Boolean

*   Default value: 0  

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: This parameter is used to enable the feature of SQL performance tuning.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="qpara" name="qpara"></span>

### QUERY_CACHE_SIZE

*   Parameter type: Numeric

*   Default value: 1048576

*   Value range/format: [0,1073741824]

*   Parameter description: Query cache size.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### QUERY_CACHE_TYPE

*   Parameter type: Enumeration

*   Default value: 0

*   Value range/format: [0|1|2]

*   Parameter description: Query cache type. 0 means off, 1 means on, 2 means on demand.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="spara" name="spara"></span>

### SQL_AUTO_IS_NULL

*   Parameter type: Boolean

*   Default value: 0

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Whether to allow finding the last inserted value of auto-increment columns through IS NULL condition.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### SQL_LOG_BIN

*   Parameter type: Boolean

*   Default value: 0

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Whether to enable binary logging.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### SQL_QUOTE_SHOW_CREATE

*   Parameter type: Boolean

*   Default value: 1

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Whether to quote table and column names in SHOW CREATE TABLE statements.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### SQL_SELECT_LIMIT

*   Parameter type: Numeric

*   Default value: 9223372036854775807

*   Value range/format: [0,9223372036854775807]

*   Parameter description: Maximum number of rows returned by SELECT statements.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="sql_mode" name="sql_mode"></span>

### SQL_MODE

*   Parameter type: String  

*   Default value: "ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL"

*   Value range/format: A string of subsets of {ANSI_QUOTES, REAL_AS_FLOAT, PIPES_AS_CONCAT, ALLOW_INVALID_DATES, ERROR_FOR_DIVISION_BY_ZERO, HIGH_NOT_PRECEDENCE, IGNORE_SPACE, NO_AUTO_CREATE_USER, NO_AUTO_VALUE_ON_ZERO, NO_BACKSLASH_ESCAPES, NO_DIR_IN_CREATE, NO_ENGINE_SUBSTITUTION, NO_UNSIGNED_SUBTRACTION, NO_ZERO_DATE, NO_ZERO_IN_DATE, ONLY_FULL_GROUP_BY, PAD_CHAR_TO_FULL_LENGTH, STRICT_ALL_TABLES, STRICT_TRANS_TABLES, TIME_TRUNCATE_FRACTIONAL} connected by commas.

*   Parameter description: Set the SQL mode.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="tpara" name="tpara"></span>

### TIME_ZONE

*   Parameter type: String

*   Default value: SYSTEM

*   Value range/format: Valid time zone value

*   Parameter description: Server time zone.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### TRANSACTION_ISOLATION

*   Parameter type: Enumeration

*   Default value: READ-COMMITTED

*   Value range/format: [READ-UNCOMMITTED|READ-COMMITTED|REPEATABLE-READ|SERIALIZABLE]

*   Parameter description: Transaction isolation level.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### TRANSACTION_READ_ONLY

*   Parameter type: Boolean

*   Default value: 0

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Whether the transaction is read-only.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### TX_ISOLATION

*   Parameter type: Enumeration

*   Default value: READ-COMMITTED

*   Value range/format: [READ-UNCOMMITTED|READ-COMMITTED|REPEATABLE-READ|SERIALIZABLE]

*   Parameter description: Transaction isolation level (alias of TRANSACTION_ISOLATION).

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

### TX_READ_ONLY

*   Parameter type: Boolean

*   Default value: 0

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Whether the transaction is read-only (alias of TRANSACTION_READ_ONLY).

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.4

<span id="vpara" name="vpara"></span>

### VALIDATE_PASSWORD_CHECK_USER_NAME

*   Parameter type: Boolean

*   Default value: TRUE

*   Value range/format: [1|0|TRUE|FALSE|ON|OFF]

*   Parameter description: Whether to compare the password with the username part of valid users in the current session; if they match, it is rejected.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="VALIDATE_PASSWORD_DICTIONARY_FILE" name="VALIDATE_PASSWORD_DICTIONARY_FILE"></span>

### VALIDATE_PASSWORD_DICTIONARY_FILE

*   Parameter type: String  

*   Default value: Empty

*   Value range/format: Valid file path, starting with ? or ., set to YASDB_DATA directory, and the path cannot contain ../

*   Parameter description: Specify the path of the dictionary file that records banned password words, which is a list of words not allowed as part of the password.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="validate_password_length" name="validate_password_length"></span>

### VALIDATE_PASSWORD_LENGTH

*   Parameter type: Numeric

*   Default value: 8

*   Value range/format: [0,2147483647]

*   Parameter description: Specify the minimum length of the password.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="VALIDATE_PASSWORD_MIXED_CASE_COUNT" name="VALIDATE_PASSWORD_MIXED_CASE_COUNT"></span>

### VALIDATE_PASSWORD_MIXED_CASE_COUNT

*   Parameter type: Numeric

*   Default value: 1

*   Value range/format: [0,2147483647]

*   Parameter description: Specify the minimum number of uppercase and lowercase letters required in the password.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="VALIDATE_PASSWORD_NUMBER_COUNT" name="VALIDATE_PASSWORD_NUMBER_COUNT"></span>

### VALIDATE_PASSWORD_NUMBER_COUNT

*   Parameter type: Numeric

*   Default value: 1

*   Value range/format: [0,2147483647]

*   Parameter description: Specify the minimum number of digits required in the password.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="VALIDATE_PASSWORD_POLICY" name="VALIDATE_PASSWORD_POLICY"></span>

### VALIDATE_PASSWORD_POLICY

*   Parameter type: Numeric

*   Default value: 1

*   Value range/format: [0|1|2]

*   Parameter description: Specify the password policy executed during password validation:

    * 0: Only checks password length requirements, with a default specification that passwords must be at least 8 characters long. To adjust this specification, modify the value of [VALIDATE_PASSWORD_LENGTH](#validate_password_length).

    * 1: Based on the 0/LOW policy, it adds password character requirements, with a default requirement that passwords must contain 1 digit character, 1 lowercase letter, 1 uppercase letter, and 1 special (non-digit) character. To adjust this requirement, modify the values of [VALIDATE_PASSWORD_NUMBER_COUNT](#VALIDATE_PASSWORD_NUMBER_COUNT), [VALIDATE_PASSWORD_MIXED_CASE_COUNT](#VALIDATE_PASSWORD_MIXED_CASE_COUNT), and [VALIDATE_PASSWORD_SPECIAL_CHAR_COUNT](#VALIDATE_PASSWORD_SPECIAL_CHAR_COUNT).

    * 2: Based on the 1/MEDIUM policy, it adds checks for matches between password substrings and dictionary files. Password substrings of length 4 or longer must not match words in the dictionary file. To specify the dictionary file, modify [VALIDATE_PASSWORD_DICTIONARY_FILE](#VALIDATE_PASSWORD_DICTIONARY_FILE).

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="VALIDATE_PASSWORD_SPECIAL_CHAR_COUNT" name="VALIDATE_PASSWORD_SPECIAL_CHAR_COUNT"></span>

### VALIDATE_PASSWORD_SPECIAL_CHAR_COUNT

*   Parameter type: Numeric

*   Default value: 1

*   Value range/format: [0,2147483647]

*   Parameter description: Specify the minimum number of special characters required in the password.

*   Scope: Global

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3

<span id="vpara" name="vpara"></span>

### VERSION

*   Parameter type: String

*   Default value: 5.7.44

*   Value range/format: NA

*   Parameter description: Database version.

*   Scope: Global

*   Read-only parameter: Yes

*   Modification method: Can't be modified

*   Introduced version: v23.4

### VERSION_COMMENT

*   Parameter type: String

*   Default value: Enterprise Edition Release 23.5.2.100 x86_64

*   Value range/format: NA

*   Parameter description: Database version comment.

*   Scope: Global

*   Read-only parameter: Yes

*   Modification method: Can't be modified

*   Introduced version: v23.4

### SYSTEM_TIME_ZONE

*   Parameter type: String

*   Default value: CST

*   Value range/format: Valid time zone value

*   Parameter description: System time zone.

*   Scope: Global

*   Read-only parameter: Yes

*   Modification method: Can't be modified

*   Introduced version: v23.4

<span id="wpara" name="wpara"></span>

### WAIT_TIMEOUT

*   Parameter type: Numeric  

*   Default value: 28800

*   Value range/format: [1,31536000]

*   Parameter description: Time in seconds that the server waits for activity before closing non-interactive connections.

*   Scope: Global, Session

*   Read-only parameter: No

*   Modification method: SET statement, File

*   Introduced version: v23.3