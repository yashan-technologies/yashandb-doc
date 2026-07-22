In the fault tolerance mechanism of *yasql*, for errors that need to be promptly recognized by users, a set of unified error message codes is provided, formatted as follows:

Error Number: Consists of 'YASQL-' followed by five digits.

Error Message: Describes the error information, with the system using variables to transmit specific information about the error context in different error scenarios.

## COMMON

### YASQL-00001: ASQLE_ALLOC_MEMORY

**Message**: can't allocate %s bytes for %s

**Action**: Memory allocation failed, please check the reason or reduce the allocation size.

### YASQL-00002: ASQLE_OPEN_FILE

**Message**: 

can't create the spool file \"%s.%s\"

can't open the file %s error: %d

**Action**: Failed to open or create the file, please check the reason and reopen the file.

### YASQL-00003: ASQLE_SQLTEXT_TOO_LONG

**Message**: the length of sql text/param name exceed limit

**Action**: Length exceeds maximum limit, please shorten the length and re-execute.

### YASQL-00005: ASQLE_NOT_CONNECTED

**Message**: connection is not established

**Action**: No connection established, please connect to the server first and re-execute.

### YASQL-00007: ASQLE_CONNECTION_ERROR

**Message**: Subject to the actual output error message.

**Action**: An error occurred during connection, please check the reason and reconnect.

### YASQL-00008: ASQLE_SET_OPTION

**Message**: Subject to the actual output error message.

**Action**: An error occurred while setting options, please check the reason and reset.

### YASQL-00009: ASQLE_EXEC_SHELL

**Message**: failed to execute shell command

**Action**: Failed to execute shell command, please check the reason and re-execute.

### YASQL-00010: ASQLE_SHOW_OPTION

**Message**: unknown SHOW option

**Action**: Unknown SHOW option, please review the supported parameter options for the SHOW command and re-execute.

### YASQL-00011: ASQLE_NOTHING

**Message**: Nothing in SQL buffer to run.

**Action**: No content to run in SQL Pool, please check the reason and re-enter content.

### YASQL-00012: ASQLE_EXIT

**Message**: EXIT variable \"%s\" was non-numeric

**Action**: The EXIT variable is not numeric, please re-enter.

### YASQL-00013: ASQLE_PARSE_ERROR

**Message**: Subject to the actual output error message.

**Action**: An error occurred during parsing, please check the reason and re-execute the command.

### YASQL-00014: ASQLE_HELP

**Message**: this topic was not found.

**Action**: This topic cannot be found, please review the supported parameter options for the HELP command and re-execute.

### YASQL-00015: ASQLE_NULL_PASSWD

**Message**: null password given; logon denied

**Action**: The given password is empty; login denied, please enter the correct password and re-login.

### YASQL-00016: ASQLE_INVALID_LIB

**Message**: the linking library INFRA/CLI is invalid

**Action**: The linking library INFRA/CLI is invalid, please check the environment configuration and try again.

### YASQL-00017: ASQLE_SET_ARG

**Message**: SET command requires an argument.

**Action**: The SET command requires an argument, please enter the argument and re-execute.

### YASQL-00018: ASQLE_SPOOL_ARG

**Message**: Illegal SPOOL command

**Action**: Illegal SPOOL command, please enter the correct command and re-execute.

### YASQL-00019: ASQLE_SUBVAR_LEN

**Message**: line overflow during variable substitution (>%d byte at line %d)

**Action**: Line overflow during variable substitution, please check the reason or try entering a shorter line.

### YASQL-00020: ASQLE_VAR_NOT_DECLARED

**Message**: Bind variable \"%s\" not declared.

**Action**: Bind variable \"%s\" is not declared, please declare the bind variable first and retry.

### YASQL-00021: ASQLE_INPUT_LEN

**Message**: Subject to the actual output error message.

**Action**: Input line overflow, please check the reason or try entering a shorter line.

### YASQL-00022: ASQLE_INVALID_PARAM

**Message**: Subject to the actual output error message.

**Action**: The input parameter is invalid, please modify.

### YASQL-00023: ASQLE_WHENEVER_SQLEEROR

**Message**: unknown command

**Action**: The command entered cannot be recognized, please enter a correct command.

### YASQL-00024: ASQLE_COL_UNDEFINED

**Message**: * COLUMN not defined

**Action**: The COLUMN is not defined, please define the COLUMN and retry.

### YASQL-00025: ASQLE_COL_SINGLE_UNDEFINED

**Message**: COLUMN '%s' not defined

**Action**: The COLUMN is not defined, please define the COLUMN and retry.

### YASQL-00026: ASQLE_COL_OPTION

**Message**: unknown COLUMN option

**Action**: Unknown COLUMN option, please review the supported parameter options for the COLUMN command and re-execute.

### YASQL-00027: ASQLE_COL_FORMAT

**Message**: illegal FORMAT string '%s'

**Action**: Invalid FORMAT string, please enter a compliant string.

### YASQL-00028: ASQLE_CLEAR_OPTION

**Message**: unknown CLEAR option

**Action**: Unknown CLEAR option, please review the supported parameter options for the CLEAR command and re-execute.

### YASQL-00029: ASQLE_INVALID_INPUT_STRING

**Message**: invalid input string, charset may not be %s

**Action**: Invalid input string, the character set may not match, please check the reason and enter a correct string.