In YashanDB's fault tolerance mechanism, a unified set of error message codes is provided for errors that users need to be aware of in a timely manner. The format is as follows:

Error Code Number (Error Number): Composed of 'YAS-' followed by five digits, with the five digits partitioned based on different modules.

Error Code Message (Error Message): Description of the error information. In different error scenarios, the system uses variables to convey specific information about the error.

**Error Number Index**:

Below 2000:

[00001](#errno1)		[00501](#errno501)		[01001](#errno01001)		[01501](#errno1501)

2000-4000: 

[02001](#errno2001)		[02501](#errno2501)		[03001](#errno3001)		[03501](#errno3501)

4000-6000: 

[04001](#errno4001)		[04501](#errno4501)		[05001](#errno5001)		[05501](#errno5501)

Above 6000:

[06001](#errno6001)		[06501](#errno6501)		[07001](#errno7001)		[08001](#errno8001)		[09001](#errno9001)

Above 10000:

[10001](#errno10001)

Above 100000:

[100001](#errno100001)

## COMMON

<span id="errno1" name="errno1"></span>

### YAS-00001:ERR_CMM_WARNING

**Message**: warning: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00002:ERR_CMM_INTERNAL_ERROR

**Message**: internal error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00003:ERR_CMM_INVALID_PARAMETER

**Message**: invalid parameter, reason: %s

**Action**: Please check the parameter based on the specific information.

### YAS-00004:ERR_CMM_NOT_IMPLEMENTED

**Message**: feature "%s" has not been implemented yet

**Action**: YashanDB does not currently support this feature, or the *yasql* client version is too low to recognize this feature. It is recommended to upgrade the version or consult our technical support.

### YAS-00005:ERR_CMM_UNKNOWN_TYPE

**Message**: invalid %s type: %u

**Action**: Invalid type, please modify the input type.

### YAS-00006:ERR_CMM_POINTER_NULL

**Message**: null pointer

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00007:ERR_CMM_NO_OPERATOR_METHOD

**Message**: no %s method for %s %s

**Action**: Operations between the current types are not supported, please modify the input type.

### YAS-00008:ERR_CMM_TYPECONV_ERROR

**Message**: type convert error : %s

**Action**: Current type conversion is not supported, please modify the input type.

### YAS-00009:ERR_CMM_TYPECOMP_ERROR

**Message**: invalid comparison between %s and %s

**Action**: Current type comparison is not supported, please modify the input type.

### YAS-00010:ERR_CMM_CONCAT_STRING

**Message**: result of string concatenation is too long

**Action**: The result of string concatenation is too long, please shorten the input string.

### YAS-00011:ERR_CMM_DIVISION_BY_ZERO

**Message**: divided by zero

**Action**: The divisor cannot be 0, please modify.

### YAS-00012:ERR_CMM_NUMERIC_OVERFLOW

**Message**: numeric overflow

**Action**: The input value is too large, please modify.

### YAS-00013:ERR_CMM_NUMERIC_TOO_LARGE

**Message**: value is larger than %s allowed

**Action**: The input value is too large, please modify.

### YAS-00014:ERR_CMM_ILLEGAL_TYPECONV

**Message**: illegal conversion from %s to %s

**Action**: Current type conversion is not supported, please refer to [Data Type Conversion for yashan mode](../Development Guide/SQL Reference Manual/Data Types/Data Type Conversion) for conversion rules. If this problem occurs while using [unsigned numeric types](../Reference Manual of mysql Mode/SQL Reference/Data Types/00Data Types) in mysql mode, it may be due to the *yasql* client version being too low, please check and upgrade the client.

### YAS-00015:ERR_CMM_PARAMETER_INVALID_VALUE

**Message**: the value of parameter %s is invalid, %s

**Action**: The input parameter is invalid, please modify.

### YAS-00016:ERR_CMM_PARAMETER_INVALID_SECTION

**Message**: invalid section %s, expect %s

**Action**: The input parameter is invalid, please modify.

### YAS-00017:ERR_CMM_PARAMETER_INVALID_UNIT

**Message**: invalid unit %s, expect %s 

**Action**: The input parameter is invalid, please modify.

### YAS-00018:ERR_CMM_PARAMETER_INVALID_ID

**Message**: parameter id %d does not match with index %u 

**Action**: The input parameter is invalid, please modify.

### YAS-00019:ERR_CMM_PARAMETER_INI_DUPLICATE_LOAD

**Message**: ini file "%s" is already loaded, please don't load it again

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00020:ERR_CMM_PARAMETER_INI_NOT_LOAD

**Message**: ini file has not been loaded, please load ini file first

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00021:ERR_CMM_PARAMETER_GET_ITEM_BY_NAME

**Message**: failed to get parameter item by name, section "%s", parameter "%s" does not exist

**Action**: The parameter with that name does not exist, please re-enter based on the supported parameters.

### YAS-00022:ERR_CMM_PARAMETER_GET_ITEM_BY_ID

**Message**: failed to get parameter name by id of unit, section "%s", unit "%s", parameter id "%u" does not exist

**Action**: The parameter with that ID does not exist, please re-enter based on the supported parameters.

### YAS-00023:ERR_CMM_PARAMETER_APPERAS_TWICE

**Message**: the format code appears twice

**Action**: The format specifier is only allowed to appear once, please modify and retry.

### YAS-00024:ERR_CMM_NUMERIC_TOO_SMALL

**Message**: value is less than %s allowed

**Action**: The input value is not within the range of the error type, please re-enter according to the corresponding data type range.

### YAS-00025:ERR_CMM_NUMERIC_PREC_TOO_LARGE

**Message**: value is larger than specified precision allowed for this column

**Action**: The input value is not within the range of the error type, please re-enter according to the corresponding data type range.

### YAS-00026:ERR_CMM_YEAR_FORMAT_APPEARS_MORE

**Message**: year may only be specified once

**Action**: The year format specifier is only allowed to appear once, please modify and retry.

### YAS-00027:ERR_CMM_MONTH_FORMAT_APPEARS_MORE

**Message**: month may only be specified once

**Action**: The month format specifier is only allowed to appear once, please modify and retry.

### YAS-00028:ERR_CMM_HOUR_FORMAT_APPEARS_MORE

**Message**: hour may only be specified once  

**Action**: The hour format specifier is only allowed to appear once, please modify and retry.

### YAS-00029:ERR_CMM_AM_PM_REQUIRED

**Message**: AM/A.M. or PM/P.M. is required

**Action**: Please enter time information such as AM or PM.

### YAS-00030:ERR_CMM_HH24_WITH_AM_PM

**Message**: HH24 and AM/PM cannot be used at the same time

**Action**: The 24-hour format cannot be used with AM, please modify and retry.

### YAS-00031:ERR_CMM_EXCEEDING_BIT_RANGE

**Message**: value exceeding bit range allowed

**Action**: The value exceeds the maximum value for BIT type, please reduce the input value.

### YAS-00032:ERR_CMM_SET_SESSION_PARAMETER

**Message**: parameter "%s" cannot be modified on session level

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00033:ERR_CMM_UNSUPPORT_UNICODE_CHAR

**Message**: unsupported unicode character %#X

**Action**: The character is not a unicode character, please modify and retry.

### YAS-00034:ERR_CMM_SET_SYSTEM_PARAMETER

**Message**: parameter "%s" cannot be modified on system level

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00036:ERR_CMM_MD5_FAILED

**Message**: failed to calculate md5 

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00037:ERR_CMM_DAY_WEEK_APPEARS_MORE

**Message**: day of week may only be specified once

**Action**: The day-related format specifier may only appear once, please modify and retry.

### YAS-00038:ERR_CMM_EXPRESS_VERSION

**Message**: feature '%s' has not been supported in this edition

**Action**: Standard edition functionality limitations, please contact our technical support for resolution.

### YAS-00039:ERR_CMM_PARAMETER_REPEAT_SET

**Message**: parameter %s cannot be set repeatedly

**Action**: Please remove duplicate configurations in the configuration file.

### YAS-00040:ERR_CMM_DATA_VALUE_OUT_OF_RANGE

**Message**: data value out of range

**Action**: Please enter a value within the data range.

### YAS-00041:ERR_CMM_TLV_INVALID_DATA

**Message**: invalid TLV data

**Action**: Invalid TLV data, please enter the correct data.

### YAS-00042:ERR_CMM_TLV_WRITE_OUT_OF_BOUNDARY

**Message**: write TLV data out of boundary

**Action**: Writing TLV data outside the boundary, please write within the boundary.

### YAS-00043:ERR_CMM_TLV_READ_OUT_OF_BOUNDARY

**Message**: read TLV data out of boundary

**Action**: Reading TLV data outside the boundary, please read within the boundary.

### YAS-00044:ERR_CMM_NOT_SUPPORTED

**Message**: "%s" has not been supported

**Action**: This operation is currently not supported.

### YAS-00045:ERR_CMM_DATA_FORMAT_ERROR

**Message**: illegal data format

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00046:ERR_CMM_BUFFER_SIZE_TOO_SMALL

**Message**: the %s buffer size is too small

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00047:ERR_CMM_NOT_ALLOWED

**Message**: %s is not allowed

**Action**: This operation is not permitted, please adjust the usage accordingly.

### YAS-00048:ERR_CMM_FAULT_POINT_NOT_HIT

**Message**: fault point: %s is not hit

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00049:ERR_CMM_UNI_PARAM_INVALID

**Message**: '\' must be followed by four hexdecimal characters or another '\'

**Action**: Refer to the corresponding function documentation to check if the specified unicode encoding is correct.

### YAS-00050:ERR_CMM_DATE_FMT_TOO_LONG

**Message**: the date format is too long for the internal buffer

**Action**: The input date format string is too long, please reduce and retry.

### YAS-00051:ERR_CMM_INVALID_ENUM_VALUE

**Message**: invalid enum value %s for %s

**Action**: The value in the enum does not exist, please confirm and retry.

### YAS-00052:ERR_CMM_INVALID_SET_VALUE

**Message**: invalid set value %s for %s

**Action**: The value in the set does not exist, please confirm and retry.

### YAS-00053:ERR_CMM_TIME_ZONE_HOURE_RANGE

**Message**: time zone hour must be between -15 and 15

**Action**: The input value for time zone hour exceeds the range, please re-enter according to the prompt.

### YAS-00054:ERR_CMM_TIME_ZONE_MINUTE_RANGE

**Message**: time zone minute must be between -59 and 59

**Action**: The input value for time zone minute exceeds the range, please re-enter according to the prompt.

### YAS-00055:ERR_CMM_DIR_FILE_OPR_NON_EXIST

**Message**: non-existent directory or file for %s operation

**Action**: Please ensure the file directory exists.

### YAS-00056:ERR_CMM_FILE_LOB_OPR_FAILED

**Message**: file or LOB operation %s failed, %s

**Action**: Please ensure the operation on bfile is valid.

### YAS-00057:ERR_CMM_FILE_LOB_UNOPEN

**Message**: cannot perform %s operation on an unopened file or LOB

**Action**: Please open the bfile locator before performing subsequent operations.

### YAS-00058:ERR_CMM_FILE_LOB_OPEN_EXCEED

**Message**: operation will exceed the maximum number of open FILE or LOB

**Action**: The bfile locator has exceeded the limit, please adjust the parameter SESSION_MAX_OPEN_FILES.

### YAS-00059:ERR_CMM_FILE_LOB_HMEM_NOT_INIT

**Message**: BFILE handle memory is not initialized

**Action**: Please initialize the bfile locator memory first.

### YAS-00060:ERR_CMM_TOO_LANG_DIR_FILENAME

**Message**: The alias of directory or file name is too long

**Action**: The alias or filename of the bfile path is too long, please adjust.

### YAS-00101:ERR_CMM_ALLOC_MEMORY

**Message**: cannot allocate % PRId64  bytes for %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00102:ERR_CMM_CREATE_MEMORY_OBJECT

**Message**: cannot create memory object %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00103:ERR_CMM_ALLOC_MPOOL_BLOCK

**Message**: no free block in %s

**Action**: Insufficient remaining memory, you can pre-allocate more memory by modifying the corresponding POOL's configuration parameters. 

Common scenarios of insufficient pool resources are as follows:

- For insufficient distribute pool, you can adjust the parameter SHARE_POOL_SIZE.
- For insufficient application pool, you can adjust the parameter WORK_AREA_POOL_SIZE.
- For insufficient application area, you can adjust the parameter WORK_AREA_HEAP_SIZE.
- For insufficient share pool, you can adjust the parameter SHARE_POOL_SIZE.
- For insufficient dictionary cache, you can adjust the parameter SHARE_POOL_SIZE.
- For insufficient SQL main pool, you can adjust the parameter SHARE_POOL_SIZE.
- For insufficient SQL PL pool, you can adjust the parameter SHARE_POOL_SIZE.
- For insufficient driver memory pool, you can adjust the parameter YDBC_BUFFER_SIZE in the yex_server.ini configuration file; for specific operations, please refer to [Adjusting Memory Pool Size](../Development Guide/SQL Reference Manual/General SQL Syntax/dblink/yex_server Sandbox Process Management).
- For insufficient stream pool, you can adjust the parameter SHARE_POOL_SIZE.

Other pool shortage scenarios involve internal configurations of YashanDB, please contact our technical support for processing.

### YAS-00104:ERR_CMM_ALLOC_MCTX_MEM

**Message**: allocate unexpected size from memory pool, mem type %s

**Action**: The data is too large, exceeding a block size, please reduce the input.

### YAS-00105:ERR_CMM_OUT_OF_MEMORY

**Message**: out of memory to allocate %s of size = % PRId64 

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00106:ERR_CMM_MEMORY_COPY

**Message**: failed to memory copy of %s, src = %p, size = % PRId64 , dst = %p, size = % PRId64 

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00107:ERR_CMM_MEMORY_MOVE

**Message**: failed to memory move of %s, src = %p, size = % PRId64 , dst = %p, size = % PRId64 

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00109:ERR_CMM_WORKSTACK_OVERFLOW

**Message**: work stack overflow, try to push %d bytes

**Action**: Please modify the value of the parameter WORK_AREA_STACK_SIZE to increase the size of the execution memory area (stack) in the session.

### YAS-00110:ERR_CMM_THREADSTACK_OVERFLOW

**Message**: worker thread stack overflow, size allowed cannot exceed %u bytes

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00111:ERR_CMM_SET_ALLOC_OPTION

**Message**: failed to set allocation option %s

**Action**: Failed to set memory allocator related parameters, please check for usage or environmental issues.

### YAS-00112:ERR_CMM_ATTACH_SHM

**Message**: failed to attach shm

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00113:ERR_CMM_BUDDY_OOM

**Message**: buddy allocator %s is out of memory

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00114:ERR_CMM_BLOCK_SIZE_ILLEGAL

**Message**: illegal block size for %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00115:ERR_CMM_ALLOC_MEM_QUOTA

**Message**: failed to allocate % PRId64  bytes memory quota for %s

**Action**: Memory quota is insufficient, please adjust the quota or memory limits according to the scenario. Common scenarios of insufficient quota are as follows:

- For insufficient init bulkload quota, you can adjust the parameter SESSION_BULKLOAD_MAX_MEM_PERCENT, or lower the concurrency based on the prompt information.
- For insufficient transformer quotator, you can adjust the parameter TRANSFORMER_MAX_MEM_PERCENT.

### YAS-00201:ERR_CMM_STRING_SNPRINTF

**Message**: failed to snprintf, src size = %u, dst size = %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00202:ERR_CMM_STRING_ERROR

**Message**: invalid string, reason: %s

**Action**: Based on the provided prompt, check for syntax errors in the input string.

### YAS-00203:ERR_CMM_CONST_STRING_LIMIT

**Message**: const string length exceeds %u

**Action**: The length of the constant string exceeds the permitted range.

### YAS-00206:ERR_CMM_BYTES_ERROR

**Message**: length in indicators exceeds the binding size

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00207:ERR_CMM_PARSER_ERROR

**Message**: end of text expected but %s found

**Action**: Syntax error in the SQL statement input, please correct and retry.

### YAS-00208:ERR_CMM_SIZE_LIMITED

**Message**: binary string '%s' out of range

**Action**: The input binary string exceeds the allowed range, please adjust and retry.

### YAS-00209:ERR_CMM_INVALID_NUMBER

**Message**: invalid string '%s', reason: %s

**Action**: Please enter a valid string according to the prompt.

### YAS-00210:ERR_CMM_DUPLICATE_OBJECT

**Message**: duplicate %s found

**Action**: Unable to create an object with the same name, please adjust and retry.

### YAS-00211:ERR_CMM_NO_DATA_FOUND

**Message**: no data found

**Action**: Data not found.

### YAS-00212:ERR_CMM_OUT_OF_INDEX

**Message**: index %u is out of [%u, %u]

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00213:ERR_CMM_COPY_DATA

**Message**: source string's length(%u) must less than the buffer size(%u)

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00214:ERR_CMM_ARRAY_OVERFLOW

**Message**: too many type processor array

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00215:ERR_CMM_MEMORY_OVERFLOW

**Message**: length of concat texts exceeds the buffer limit

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00216:ERR_CMM_INVALID_CHARSET_NAME

**Message**: invalid charset name "%s"

**Action**: Please enter a valid charset name.

### YAS-00217:ERR_CMM_BOOL_INVALID_VALUE

**Message**: bool string must be "TRUE" or "FALSE"

**Action**: Please enter a valid bool string.

### YAS-00218:ERR_CMM_CONVERT_STRING

**Message**: string conversion failed

**Action**: The string conversion failed, please enter a valid string.

### YAS-00219:ERR_CMM_FORMAT_STR_OVERFLOW

**Message**: the formatted string length(%u) is longer than the buffer size(%u)

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00220:ERR_CMM_UTF8_SEQUENCE_WRONG

**Message**: utf8 sequence is wrong

**Action**: UTF8 character input error, please correct and retry.

### YAS-00221:ERR_CMM_UTF8_BUFFER_SMALL

**Message**: utf8 character buffer is too small

**Action**: UTF8 character input error, please correct and retry.

### YAS-00222:ERR_CMM_NLSSORT_INVALID

**Message**: nlssort rule %s under charset %s is invalid

**Action**: Unsupported nlssort rule, please check the input syntax.

### YAS-00223:ERR_CMM_INVALID_HEX_NUMBER

**Message**: invalid hex number

**Action**: Please enter a correct hexadecimal number.

### YAS-00224:ERR_CMM_INVALID_CODEPAGE

**Message**: Codepage is Invalid

**Action**: Please configure the correct Codepage file.

### YAS-00225:ERR_CMM_UTF16_SEQUENCE_WRONG

**Message**: utf16 sequence is wrong

**Action**: UTF16 character input error, please correct and retry.

### YAS-00226:ERR_CMM_UTF16_BUFFER_SMALL

**Message**: utf16 character buffer is too small

**Action**: UTF16 character input error, please correct and retry.

### YAS-00227:ERR_CMM_INVALID_NCHARSET_NAME

**Message**: invalid national charset name "%s"

**Action**: Please enter a valid national charset name.

### YAS-00228:ERR_CMM_CHARSET_MISMATCH

**Message**: character set mismatch

**Action**: Character sets do not match, please adjust and retry.

### YAS-00229:ERR_CMM_CHARSET_CONVERT

**Message**: character set conversion failed, exceeds the buffer size(%u)

**Action**: Please check if the string being converted exceeds the operational limit of 65534 bytes.

### YAS-00230:ERR_CMM_GBK_SEQUENCE_WRONG

**Message**: gbk sequence is wrong

**Action**: GBK character input error, please correct and retry.

### YAS-00231:ERR_CMM_GBK_BUFFER_SMALL

**Message**: gbk character buffer is too small

**Action**: GBK character input error, please correct and retry.

### YAS-00232:ERR_CMM_INVALID_COLLATION_NAME

**Message**: invalid collation name "%s"

**Action**: Please enter a valid collation name.

### YAS-00301:ERR_CMM_FILE_OPER

**Message**: file operation "%s" failed, %s

**Action**: Please check if the status of the corresponding file is correct or determine the repair operation based on the operating system error code.

### YAS-00302:ERR_CMM_INVALID_FILENAME

**Message**: file %s is invalid because %s

**Action**: Please check the length of the file name or determine the repair operation based on the operating system error code.

### YAS-00303:ERR_CMM_INIFILE_ERROR

**Message**: ini file %s error, file size is %u, limitation is %u

**Action**: Please determine the repair operation based on the operating system error code.

### YAS-00304:ERR_CMM_DEVICE_ERROR

**Message**: %s is an invalid disk device/file, reason: %s

**Action**: Please check the device name or determine the repair operation based on the operating system error code.

### YAS-00305:ERR_CMM_NO_SPACE

**Message**: out of space on device

**Action**: Please check the disk space or determine the repair operation based on the operating system error code.

### YAS-00306:ERR_CMM_DEVICE_BUSY

**Message**: device is busy

**Action**: The device is busy, please try again later.

### YAS-00307:ERR_CMM_EXEC_INIFILE

**Message**: %s inifile %s error

**Action**: Please determine the repair operation based on the operating system error code.

### YAS-00308:ERR_CMM_INIFILE_LINE

**Message**: error inifile text %s

**Action**: Please determine the repair operation based on the operating system error code.

### YAS-00310:ERR_CMM_INVALID_PATH_ERROR

**Message**: invalid file path %s

**Action**: Please check the file path or determine the repair operation based on the operating system error code.

### YAS-00311:ERR_CMM_CREATE_FILE

**Message**: failed to create file %s, %s

**Action**: Please check if the file already exists, if the disk space is insufficient, or determine the repair operation based on the operating system error code.

### YAS-00312:ERR_CMM_LOCK_FILE

**Message**: failed to lock file %s, %s

**Action**: Please determine the repair operation based on the operating system error code.

### YAS-00313:ERR_CMM_OPEN_FILE

**Message**: failed to open file %s, %s

**Action**: Please check if the status of the corresponding file is correct or determine the repair operation based on the operating system error code.

### YAS-00314:ERR_CMM_REMOVE_FILE

**Message**: failed to remove file %s, %s

**Action**: Please check if the file exists or determine the repair operation based on the operating system error code.

### YAS-00315:ERR_CMM_RENAME_FILE

**Message**: failed to rename file %s to %s, %s

**Action**: Please check if the status of the corresponding file is correct or determine the repair operation based on the operating system error code.

### YAS-00316:ERR_CMM_INIFILE_SAVE_ERROR

**Message**: failed to save file, file name is %s 

**Action**: Please check if the status of the corresponding file is correct or determine the repair operation based on the operating system error code.

### YAS-00317:ERR_CMM_INIFILE_UNLOADED

**Message**: ini file unloaded

**Action**: Please check if the status of the corresponding file is correct or determine the repair operation based on the operating system error code.

### YAS-00318:ERR_CMM_CREATE_DIRECTORY

**Message**: failed to create directory %s, %s

**Action**: Please check if the path is correct or determine the repair operation based on the operating system error code.

### YAS-00319:ERR_CMM_OPEN_DIRECTORY

**Message**: failed to open directory %s, %s

**Action**: Please check if the path is correct or determine the repair operation based on the operating system error code.

### YAS-00320:ERR_CMM_CLOSE_DIRECTORY

**Message**: failed to close directory %s, %s

**Action**: Please check if the path is correct or determine the repair operation based on the operating system error code.

### YAS-00321:ERR_CMM_REMOVE_DIRECTORY

**Message**: failed to remove directory %s, %s

**Action**: Please check if the path is correct or determine the repair operation based on the operating system error code.

### YAS-00322:ERR_CMM_DUMP_TO_FILE_FAILED

**Message**: failed to dump to file

**Action**: This error code appears only in multi-level error codes, please refer to the action of the underlying error code.

### YAS-00323:ERR_CMM_NO_PUNCH_HOLE

**Message**: the file system does not support punch hole

**Action**: Please select a suitable file system, supported file systems can be referenced in [CREATE_TABLESPACE](../Development Guide/SQL Reference Manual/SQL Statements/CREATE TABLESPACE).

### YAS-00324:ERR_CMM_FILE_PAGE_SIZE

**Message**: the page size of file system must be %s

**Action**: Please select a file system that meets the page size requirements, supported file systems can be referenced in [CREATE_TABLESPACE](../Development Guide/SQL Reference Manual/SQL Statements/CREATE TABLESPACE).

### YAS-00325:ERR_CMM_WRITE_FILE

**Message**: write file failed

**Action**: UTL_FILE write file error.

### YAS-00326:ERR_CMM_INVALID_FILETYPE

**Message**: invalid fileType

**Action**: Please check if the input file handle has been opened and is a file handle of the UTL_FILE package.

### YAS-00327:ERR_CMM_INVALID_MODE

**Message**: invalid open mode

**Action**: Please check if the open mode is in the UTL_FILE specified open modes.

### YAS-00328:ERR_CMM_READ_FILE

**Message**: read file failed

**Action**: UTL_FILE read file error.

### YAS-00329:ERR_CMM_INVALID_OFFSET

**Message**: the specified search offset is invalid, %s

**Action**: The specified offset for reading the file needs to be within the valid range of the file, please adjust and retry.

### YAS-00330:ERR_CMM_INVALID_PATH

**Message**: invalid path

**Action**: Invalid file path, please adjust and retry.

### YAS-00331:ERR_CMM_INVALID_FILE

**Message**: invalid file name

**Action**: Invalid file name, please adjust and retry.

### YAS-00332:ERR_CMM_ACCESS_DENIED

**Message**: access denied

**Action**: Please check if you have the privilege to access the path.

### YAS-00333:ERR_CMM_INVALID_MAX_LINESIZE

**Message**: invalid max lineSize

**Action**: A line length supported by UTL_FILE must be between 1 and 32000, please adjust and retry.

### YAS-00334:ERR_CMM_NO_DATA

**Message**: no data found

**Action**: No data could be found starting from the specified offset in the file.

### YAS-00335:ERR_CMM_CLOSE_FILE

**Message**: close file error, because %s

**Action**: Failed to close file handle, please retry.

### YAS-00336:ERR_CMM_OVER_FILES

**Message**: too many open files , exceed %d

**Action**: The number of open files exceeds the set SESSION_MAX_OPEN_FILES limit.

### YAS-00337:ERR_CMM_NOT_SUPPORT_PATH

**Message**: only support directory begin with / or .

**Action**: Unsupported file path format, please adjust and retry.

### YAS-00338:ERR_CMM_S3_REQUEST

**Message**: s3 request failed, uri %s, error no %u, message %s

**Action**: Please investigate based on the corresponding error information regarding s3 service anomalies.

### YAS-00339:ERR_CMM_S3_RESOURCE

**Message**: failed to create s3 %s

**Action**: Please check if the system memory resources are sufficient; if memory resources are sufficient, please contact our technical support for resolution.

### YAS-00340:ERR_CMM_INVALID_DATAFILE_NAME

**Message**: the datafile name must not contain the string %s

**Action**: Please modify the Data File name and retry.

### YAS-00341:ERR_CMM_SCSI_PR_DIRTY

**Message**: scsi device %s has dirty persistent reservation issued by others

**Action**: The device has residual persistent reservation information, please execute the command `sudo ycsrootagent scsi clear -d <device>` to clean after confirming that the operation is safe.

### YAS-00401:ERR_CMM_INIT_TCP_ENV

**Message**: failed to init tcp environment, %s

**Action**: Please adjust the system configuration based on the specific error information.

### YAS-00402:ERR_CMM_SOCKET_ERROR

**Message**: failed to %s, %s  

**Action**: Please adjust the system configuration or database parameters based on the specific error message and system error code.

### YAS-00403:ERR_CMM_TOO_MANY_TCPLSNR

**Message**: tcp listener count %u exceeds %u

**Action**: Please reduce the number of listener addresses set by the parameter LISTEN_ADDR.

### YAS-00404:ERR_CMM_INVALID_IPADDR

**Message**: address %s is an invalid %s URL/Ip address/hostname

**Action**: Please check if the IP address is valid.

### YAS-00405:ERR_CMM_PROTOCOL_CONNECT

**Message**: protocol error, failed to connect to %s, because %s

**Action**: Protocol error, please check if the network or the client version in the deployment environment is compatible, check the connection information, or contact our technical support for resolution.

### YAS-00406:ERR_CMM_CONNECT_CLOSED

**Message**: connection is closed

**Action**: The connection has been closed, please exit and reconnect to the server.

### YAS-00407:ERR_CMM_SEND_FAIL

**Message**: failed to send, %s

**Action**: Please check the network or whether the server process is closed in the deployment environment.

### YAS-00408:ERR_CMM_RECV_FAIL

**Message**: failed to receive, %s

**Action**: Please check the network or whether the peer process is closed in the deployment environment.

### YAS-00409:ERR_CMM_EPOLL_FAIL

**Message**: failed to epoll, %s

**Action**: Please check the network or whether the peer process is closed in the deployment environment.

### YAS-00410:ERR_CMM_UNKNOWN_PROTOCOL

**Message**: protocol error, %s

**Action**: Protocol error, please check the SQL statement according to the information.

### YAS-00411:ERR_CMM_SET_SOCKET

**Message**: failed to set %s, %s

**Action**: Please check the system parameters in the deployment environment or contact our technical support for resolution.

### YAS-00412:ERR_CMM_UNSUPPORT_LINK_TYPE

**Message**: unsupported link type %u

**Action**: Protocol type error, please check if the network or the client version in the deployment environment is compatible, check the connection information, or contact our technical support for resolution.

### YAS-00413:ERR_CMM_SOCKET_TIMEOUT

**Message**: %s timeout

**Action**: Network timeout, please check the network quality in the deployment environment.

### YAS-00414:ERR_CMM_LOCAL_LSNR_FAIL

**Message**: failed to create local listener, path %s, %s

**Action**: Please check the privilege of the instance/yasdb.ipc file in the database HOME directory.

### YAS-00415:ERR_CMM_LSNR_FAIL

**Message**: failed to create listener, host: %s:%u 

**Action**: Please check the database's listener configuration and configure the listener reasonably based on the specific error message and system error code.

### YAS-00416:ERR_CMM_MSG_BUFFER_OVERFLOW

**Message**: %s overflow, target %u limit %u 

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00417:ERR_CMM_INVALID_MSG_SIZE

**Message**: invalid message size %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00418:ERR_CMM_INVALID_DESTINATION

**Message**: invalid destination %u 

**Action**: The node ID has been deleted, please retry.

### YAS-00419:ERR_CMM_CONNECT_NUM_LIMIT

**Message**: number of connections exceeds the limit, current: %u max: %u

**Action**: Internal transient limit, the system will automatically recover, please ignore.

### YAS-00420:ERR_CMM_DISCONTINUOUS_MEMORY

**Message**: discontinuous memory

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00421:ERR_CMM_HANDSHAKE_FAIL

**Message**: failed to handshake, %s

**Action**: Please investigate node configuration based on the log prompts.

### YAS-00422:ERR_CMM_SEND_TIMEOUT

**Message**: send message to node %s with link level %u timeout %ums, %s

**Action**: Sending timeout, please retry.

### YAS-00423:ERR_CMM_ICS_NOT_STARTED

**Message**: ICS manager does not start

**Action**: Internal error, the system will automatically recover, please ignore.

### YAS-00424:ERR_CMM_INVALID_LINK_ID

**Message**: invalid link id %u_%u_%u

**Action**: Invalid communication link ID, please retry.

### YAS-00425:ERR_CMM_NODE_REMOVED

**Message**: node(%u) has been removed

**Action**: This node has been deleted, please retry.

### YAS-00426:ERR_CMM_LINK_IN_USE

**Message**: ICS link %u_%u_%u already in use

**Action**: Internal transient error, the system will automatically recover, please ignore.

### YAS-00427:ERR_CMM_TAKE_LINK

**Message**: take the link %u_%u_%u failed: %s

**Action**: Handle according to the error content:

- If it reports "not reserved", it is an internal error, please contact our technical support for resolution.
- If it reports "take link timeout", it indicates a timeout waiting for a valid link, please retry.

### YAS-00428:ERR_CMM_LSNR_FAIL_V6

**Message**: failed to create listener, ipv6 host: [%s]:%u 

**Action**: Please check the database's listener configuration and configure the listener reasonably based on the specific error message and system error code.

### YAS-00429:ERR_CMM_GET_ADDR_FAIL

**Message**: failed to get link address

**Action**: Failed to obtain the address associated with the socket, please check the network status.

### YAS-00430:ERR_CMM_ICS_NODE_STOPPING

**Message**: ICS node(%u) is stopping

**Action**: The network communication for the corresponding node is being stopped, please ignore.

### YAS-00431:ERR_CMM_IP_UNREACHABLE

**Message**: ip address %s is unreachable

**Action**: This IP address is unreachable, can be ignored.

### YAS-00432:ERR_CMM_INTERFACE_INVALID

**Message**: network interface %s is invalid, reason: %s

**Action**: Abnormal network interface status or unsupported protocol, please check the error based on the specific error message.

### YAS-00433:ERR_CMM_CURL

**Message**: CURL error happened during `%s`: [%d]%s

**Action**: An error occurred in the CURL request. Please investigate based on the specific error message.

### YAS-00434:ERR_CMM_URL_INVALID

**Message**: url format is invalid: %s

**Action**: The URL format is incorrect. Please check.

### YAS-00435:ERR_CMM_ABORT_LINK

**Message**: link aborted, need to disconnect immediately

**Action**: Internal error, please contact our technical support for resolution.

<span id="errno501" name="errno501"></span>

### YAS-00501:ERR_CMM_LOCK_NOT_AVAILABLE

**Message**: spin lock is not available now

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00502:ERR_CMM_LOCK_FAILED

**Message**: failed to lock with error code %d

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00503:ERR_CMM_CREATE_THREAD

**Message**: failed to create thread %s, %s

**Action**: Please check the operating system resources.

### YAS-00504:ERR_CMM_CREATE_SEMAPHORE

**Message**: failed to create semaphore

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00505:ERR_CMM_ENV_ERROR

**Message**: failed to get current environment "%s", %s

**Action**: Please check the operating system error in the system documentation and perform the necessary actions.

### YAS-00506:ERR_CMM_SYSTEM_CALL

**Message**: system call %s error, errno = %d

**Action**: Please check the operating system error in the system documentation and perform the necessary actions.

### YAS-00507:ERR_CMM_DATE_OVERFLOW

**Message**: date/timestamp value overflow

**Action**: Please modify the date to fall within the valid range:

- DATE: 0001-01-01 00:00:00 ~ 9999-12-31 23:59:59
- TIMESTAMP: 0001-01-01 00:00:00.000000 ~ 9999-12-31 23:59:59.999999

### YAS-00508:ERR_CMM_TIME_OVERFLOW

**Message**: time value overflow

**Action**: Please modify the time to fall within the valid range (-838:59:59.999999 ~ 838:59:59.999999).

### YAS-00509:ERR_CMM_LOAD_LIB_FAILED

**Message**: failed to load dynamic library %s, %s

**Action**: Please check the dynamic link library path.

### YAS-00510:ERR_CMM_CLOSE_LIB_FAILED

**Message**: failed to close dynamic library %s

**Action**: Please check the reason for the failure to close the dynamic link library based on the error message.

### YAS-00511:ERR_CMM_GET_SYMS_FAILED

**Message**: failed to get symbol %s, %s

**Action**: Please check the reason for the failure in obtaining the symbol based on the error message.

### YAS-00512:ERR_CMM_INIT_LIB_FAILED

**Message**: failed to init dynamic library %s

**Action**: Please check if the dynamic link library is installed.

### YAS-00513:ERR_CMM_CREATE_MUTEX

**Message**: failed to create mutex, error code %d, %s

**Action**: Please check the reason for the failure based on the system error message.

### YAS-00517:ERR_CMM_WORKER_POOL_CLOSED

**Message**: worker pool is closed

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00518:ERR_CMM_WORKER_POOL_PARAM

**Message**: worker pool parameters are set incorrectly

**Action**: Worker pool settings are incorrect, please check if the parameters are normal.

### YAS-00519:ERR_CMM_CHECK_LIB_FAILED

**Message**: failed to check dynamic library %s, %s

**Action**: Please use a version of the dynamic link library that meets the requirements.

### YAS-00520:ERR_CMM_CHECK_SIGNATURE_FAILED

**Message**: failed to check software signature, %s

**Action**: Software package integrity verification failed, please use an official release legal package.

### YAS-00521:ERR_CMM_PROCESS_PERMISSION

**Message**: process permission is insufficient

**Action**: Please log in as the root user (or sudo) to execute.

### YAS-00601:ERR_CMM_DATA_ENCRYPTION

**Message**: failed to encryption error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00602:ERR_CMM_DATA_ENCRYPTION_KEY

**Message**: failed to check encryption key : %s

**Action**: Please check if the key file is correct during encryption and decryption.

### YAS-00603:ERR_CMM_COMPRESS_ILLEGAL_INPUT

**Message**: input size %u is invalid

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00604:ERR_CMM_COMPRESS_FAILED

**Message**: failed to compress data, %s

**Action**: Data compression failed, please check if the compressed data is valid.

### YAS-00605:ERR_CMM_DECOMPRESS_FAILED

**Message**: failed to decompress data, %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-00606:ERR_CMM_COMPRESS_TYPE_INVALID

**Message**: compression type is invalid

**Action**: Please enter a valid compression algorithm name.

### YAS-00607:ERR_CMM_ENCRYPTIONALGO_INVALID

**Message**: encryption algorithm error, algorithm name '%s', %s

**Action**: Encryption algorithm error, please select a suitable encryption algorithm based on the error content.

### YAS-00608:ERR_CMM_SSL_INIT

**Message**: failed to init ssl error: %s, %s

**Action**: Please configure the correct SSL parameters.

### YAS-00609:ERR_CMM_SSL_INVALID_ECDH

**Message**: invalid ecdh: %s

**Action**: Please configure the correct ECDH parameters.

### YAS-00610:ERR_CMM_SSL_CONNECTION_FAILED

**Message**: failed to connect ssl : %s

**Action**: Please configure the correct certificate and other parameters, then reconnect.

### YAS-00611:ERR_CMM_ENCRYPT_MODE

**Message**: encrypt mode mismatched, expect %s, but actual %s

**Action**: Please configure the encryption library in the environment to be consistent with the encryption method in the Data File.

### YAS-00612:ERR_CMM_GENERATE_GUID

**Message**: failed to generate guid

**Action**: Failed to generate GUID, please regenerate the GUID.

### YAS-00613:ERR_CMM_PKEY_INIT

**Message**: failed to init public key error: %s, %s

**Action**: Failed to load public key, please check the public key file configuration.

### YAS-00614:ERR_CMM_UKEY_FAILED

**Message**: uKey %s failed

**Action**: UKEY operation failed.

### YAS-00615:ERR_CMM_UKEY_TOO_MANY_DEVICES

**Message**: too many uKey devices

**Action**: UKEY device names are duplicated, please adjust and retry.

### YAS-00616:ERR_CMM_UKEY_NO_DEVICE

**Message**: no uKey device

**Action**: No UKEY devices.

### YAS-00617:ERR_CMM_UKEY_VERIFY_SIGN

**Message**: failed to verify ukey sign error: %s, %s

**Action**: Failed to verify UKEY signature.

### YAS-00618:ERR_CMM_SECURITY_OPERATION

**Message**: failed to %s, error: %s

**Action**: Failed to construct the public key.

### YAS-00619:ERR_CMM_TLCP_INIT

**Message**: failed to init tlcp error: %s

**Action**: Please configure the correct TLCP parameters.

### YAS-00620:ERR_CMM_PCIE_ERROR

**Message**: pcie internal failed: %s

**Action**: PCIe related error, please investigate the fault based on the PCIe error message.

### YAS-00621:ERR_CMM_PKCS11_CALLBACK_ERROR

**Message**: pkcs11 operation '%s' failed, %s

**Action**: A PKCS #11 interface error has occurred, please investigate the fault based on the specific error messages and operations that triggered the failure.

### YAS-00622:ERR_CMM_PKCS12_READ_FAILED

**Message**: failed to read pkcs12, %s

**Action**: Failed to read the wallet file pkcs12 file, please investigate the fault based on the specific error message.

### YAS-00623:ERR_CMM_PKCS12_ERROR

**Message**: pkcs12 operation '%s' failed, errMsg %s at %s:%d

**Action**: The PKCS #12 wallet file operation failed, please investigate based on the specific error messages.

### YAS-00624:ERR_CMM_KMS_ERROR

**Message**: Some error happened in KMS, [%s]: %s

**Action**: An error occurred in KMS. Please investigate based on the specific error message.

### YAS-00625:ERR_CMM_PKCS12_ATTR_NOT_FOUND

**Message**: pkcs12 attr not found: %s

**Action**: The specific attribute of the key metadata was not found in the key file. Please confirm whether the wallet file is valid.

### YAS-00701:ERR_CMM_YASON_INVALID_TYPE

**Message**: invalid yason type value: %d

**Action**: Incorrect YASON type, please check if the YASON data is valid.

### YAS-00702:ERR_CMM_YASON_INDEX_OUT_OF_BOUNDS

**Message**: index out of bounds in yason: the len is %d but the index is %d

**Action**: Out of bounds accessing YASON data, please check if the YASON data is valid.

### YAS-00703:ERR_CMM_YASON_UNEXPECTED_TYPE

**Message**: yason type mismatched, expect %s, but actual %s

**Action**: YASON type mismatch, please check if the type is correct.

### YAS-00704:ERR_CMM_YASON_EXCEEDS_MAX_SIZE

**Message**: yason exceeds max size(%d)

**Action**: YASON data is too large, please reduce the input data and retry.

### YAS-00705:ERR_CMM_YASON_STRING_TOO_LONG

**Message**: yason string is too long, length: %d

**Action**: YASON string is too long, please reduce the string length and retry.

### YAS-00706:ERR_CMM_YASON_BINARY_TOO_LONG

**Message**: yason binary is too long, length: %d

**Action**: YASON binary data is too long, please reduce the binary data length and retry.

### YAS-00707:ERR_CMM_YASON_INVALID_VARINT

**Message**: invalid varint in yason

**Action**: Invalid variable-length int encoding, please check if the YASON data is valid.

### YAS-00708:ERR_CMM_YASON_REACHES_ELEMENT_COUNT

**Message**: the number of elements reaches the element count(%d)

**Action**: The number of elements in the array or object is full, no more elements can be input.

### YAS-00709:ERR_CMM_YASON_INCONSISTENT_ELEMENT_COUNT

**Message**: inconsistent element count, expected %d, actual %d

**Action**: The number of elements set in the array or object is inconsistent with the actual number, please check the element count.

### YAS-00710:ERR_CMM_YASON_NESTING_TOO_DEEPLY

**Message**: nesting too deeply

**Action**: YASON nesting occurs too many times, please reduce the nesting of the input data and retry.

### YAS-00711:ERR_CMM_YASON_INNER_UNCOMPLETED

**Message**: inner builder is not finished

**Action**: The inner nested structure of YASON is incomplete, please check and use the builder correctly.

### YAS-00712:ERR_CMM_YASON_JSON_SYNTAX_ERROR

**Message**: JSON syntax error at line %d column %d: %s

**Action**: JSON syntax error, please check the input JSON syntax and retry.

### YAS-00713:ERR_CMM_YASON_TOO_MANY_CHILDREN

**Message**: too many children in the current object or array

**Action**: There are too many elements in the current object or array, the maximum supported elements are 65535.

### YAS-00714:ERR_CMM_YASON_DATA_LENGTH_MISMATCHED

**Message**: yason data length %d mismatch the actual data length %d

**Action**: Please check if the length of the YASON data matches.

### YAS-00715:ERR_CMM_YASON_INVALID_BOOL

**Message**: invalid yason bool data

**Action**: The bool value must be 0 or 1, please check the data.

### YAS-00716:ERR_CMM_YASON_INVALID_DATETIME

**Message**: invalid yason date/time data

**Action**: The time exceeds the range, please check the time range.

### YAS-00717:ERR_CMM_YASON_INVALID_NUMBER

**Message**: invalid yason number data

**Action**: The YASON number type data is limited to a maximum of 20 bytes, please check the length of the number.

### YAS-00718:ERR_CMM_YASON_INVALID_UNICODE

**Message**: invalid yason string data

**Action**: Please check if the YASON data contains invalid UTF-8 unicode characters.

### YAS-00719:ERR_CMM_YASON_INVALID_KEY

**Message**: unordered or duplicated yason object keys

**Action**: The keys of the YASON object data are unordered or duplicated, please check the data.

### YAS-00720:ERR_CMM_YASON_INVALID_UNICODE_ESCAPE

**Message**: input contains invalid unicode escape

**Action**: The JSON or path expression contains an illegal unicode escape, please check and use the escape correctly.

### YAS-00721:ERR_CMM_YASON_PATH_EXPR_SYNTAX_ERROR

**Message**: path expression syntax error

**Action**: Path expression syntax error, please check the input path expression syntax and retry.

### YAS-00722:ERR_CMM_YASON_PATH_EXPR_NO_RESULT

**Message**: found no result

**Action**: No values found in the path expression query result.

### YAS-00723:ERR_CMM_YASON_PATH_EXPR_MANY_RESULTS

**Message**: found multiple results

**Action**: The path expression query obtained multiple values.

### YAS-00724:ERR_CMM_YASON_PATH_EXPR_FUNC_NEEDS_WRAP

**Message**: function step requires wrapping

**Action**: The function step used in the path expression must be wrapped with WITH [ARRAY] WRAPPER.

### YAS-00725:ERR_CMM_YASON_EXCEEDS_MAX_KEY_SIZE

**Message**: yason exceeds max key size(%d)

**Action**: The key length of YASON object data exceeds the maximum supported length. Please check the data.

## DISTRIBUTED

<span id="errno01001" name="errno01001"></span>

### YAS-01200:ERR_DSTB_INVALID_SESSION

**Message**: node: %u-%u failed to get session: %u

**Action**: The session for the operation is invalid; please check if the session ID is correct.

### YAS-01201:ERR_DSTB_INVALID_NODE

**Message**: failed to get node: %u

**Action**: The node for the operation is invalid; please check if the node ID is correct.

### YAS-01202:ERR_DSTB_NODE_ABNORMAL

**Message**: node in group: %u is abnormal

**Action**: There is a node failure in the node group; please check the status of the node group.

### YAS-01204:ERR_DSTB_ROLLBACK_ALL_DONE

**Message**: the transaction has been fully rolled back because of the %s

**Action**: The current session transaction has been rolled back, please re-execute the related operation.

### YAS-01205:ERR_DSTB_FAULT_POINT

**Message**: this is a fault point: %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01206:ERR_DSTB_NODE_SWITCHOVER

**Message**: node in group: %u is switchover

**Action**: The primary node in the node group is currently switching between Primary/Standby; please retry after the switch is successful.

### YAS-01207:ERR_DSTB_COMMIT_DOUBT

**Message**: not sure whether the commit is successful

**Action**: Please check the transaction documentation to confirm if the transaction has been successfully committed.

### YAS-01208:ERR_DSTB_ENDPOINTS_EMPTY

**Message**: no available endpoint could be found

**Action**: No available nodes were found; please check the node status.

### YAS-01209:ERR_DSTB_INVALID_MESSAGE

**Message**: message is invalid, message cmd: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01210:ERR_DSTB_BATCH_EXECUTE_FAILED

**Message**: execute batch failed, reason: %s

**Action**: The distributed batch bind execution failed; please take appropriate action based on the error description.

### YAS-01211:ERR_DSTB_METADATA_INCONSISTENCY

**Message**: metadata inconsistency exists, indicating a possible switchover or failover occurred, please check if there are remaining DDL publish tasks

**Action**: Please check for residual DDL publish tasks; if any exist, rollback the transaction related to the object.

### YAS-01212:ERR_DSTB_SESSION_WORKER_NOT_ENOUGH

**Message**: node: %u-%u session worker is not enough

**Action**: There is insufficient max_workers configuration on this node; please adjust according to the configuration parameter documentation.

### YAS-01213:ERR_DSTB_SESSION_SEND_FAIL

**Message**: "session send command(%s) to node: %u-%u failed from node: %u-%u, %s"

**Action**: Please investigate the possible reasons for the failure:

- Network failure; you can exclude network issues using operating system commands or refer to internal network-related views such as GV$DIN_NODE, GV$DIN_LINK, etc.
- Resource contention; you can check for CPU, memory issues using operating system commands, or refer to database performance-related views.
- From the perspective of unreasonable configuration; whether the resource configuration can meet actual concurrency, please refer to [Session Management](../Database Administration/Operation Monitoring/Session Management) for managing concurrent tasks.

### YAS-01350:ERR_DSTB_TAG_TYPE_NOT_MATCHED

**Message**: unexpected type(%d) of TLV tag of %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01351:ERR_DSTB_TLV_TAG_MISSING

**Message**: message TLV tag of %s is missing

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01352:ERR_DSTB_UNKNOWN_TLV_TAG

**Message**: unknown message TLV tag(%d) of %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01353:ERR_DSTB_DUPLICATE_TLV_TAG

**Message**: duplicate message TLV tag(%d) of %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01354:ERR_DSTB_UNEXPECTED_TLV_TAG

**Message**: expected message TLV tag(%d) of %s but actually got %d

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01355:ERR_DSTB_SCALING_OUT_AND_DDL_BLOCKED

**Message**: scaling out, DDL and DCL is blocked

**Action**: Node scale-out is in progress; distributed DDL and DCL execution is prohibited. Please wait until scale-out is complete before proceeding.

### YAS-01356:ERR_DSTB_SCALING_OUT_AND_UNSERVICEABLE

**Message**: scaling out and unserviceable

**Action**: Node scale-out is in progress; services are unavailable. Please wait until scale-out is complete before proceeding.

### YAS-01357:ERR_DSTB_SCALING_OUT_FAIL_AND_RETRY

**Message**: scaling out fail, please clean up scaling out task and try again

**Action**: Scale-out has failed abnormally. Please clean up the scale-out tasks and retry.

### YAS-01358:ERR_DSTB_SCALER_KEY_WORD_EXPECTED

**Message**: %s expected in json data

**Action**: Please modify the erroneous request statement and retry.

### YAS-01359:ERR_DSTB_SCALER_CHUNK_LIMIT_EXCEEDED

**Message**: chunks number %d exceeds the limit

**Action**: The number of chunks requested for migration is too high; please reduce the number of chunks and retry.

### YAS-01360:ERR_DSTB_SCALER_INVALID_CHUNK_ID

**Message**: the specified chunk id %d does not exist

**Action**: The requested chunk id does not exist; please modify it and retry.

### YAS-01361:ERR_DSTB_SCALER_DUPLICATED_CHUNK

**Message**: same chunk id %d appears more than once

**Action**: The same chunk must be specified only once in a single request. Please adjust and retry.

### YAS-01362:ERR_DSTB_SCALER_INVALID_GROUP

**Message**: the specified group %d does not exist

**Action**: The requested target group does not exist; please adjust and retry.

### YAS-01363:ERR_DSTB_SCALER_REMOVE_ALL_GROUP

**Message**: cannot remove all groups from dataspace

**Action**: The request will delete all groups; please reduce the number of groups to be deleted and retry.

### YAS-01364:ERR_DSTB_SCALER_INPUT_EMPTY_ARRAY

**Message**: array of %s in json data is empty

**Action**: Please fill in the elements in the array and retry.

### YAS-01367:ERR_DSTB_DEFAULT_TABLE_TYPE_CONFLICT

**Message**: default table type is conflict

**Action**: Please check the configuration parameter DEFAULT_TABLE_TYPE and ensure consistency before retrying.

### YAS-01368:ERR_DSTB_ACCOUNT_DROPPED

**Message**: the account is dropped

**Action**: The user is no longer available; please manually delete and retry.

### YAS-01369:ERR_DSTB_SCALE_GROUP_ONLY_SUPPORT_DN_PN

**Message**: scale group only support DN and PN

**Action**: This operation is supported only for DN groups.

### YAS-01370:ERR_DSTB_HANDLER_RELEASE

**Message**: current handler has been release

**Action**: The current HANDLER has been released; please wait for the system switch or shutdown to complete.

### YAS-01371:ERR_DSTB_SET_SYSTEM_PARAMETER

**Message**: set system parameter failed, %s

**Action**: Failed to modify the configuration parameter; please check according to the error message.

### YAS-01372:ERR_DSTB_SET_SYSTEM_PARAMETER_UNPREDICTABLE

**Message**: the result of setting system parameters is unpredictable, %s

**Action**: The result of modifying configuration parameters is unpredictable; please check according to the error message and verify the result of parameter modification.

### YAS-01373:ERR_DSTB_PLANKEY_MISMATCH

**Message**: plan cache is mismatch on DN

**Action**: Internal transient error; the system will recover automatically. Please ignore.

### YAS-01374:ERR_DSTB_REDISTRIBUTE_ONLY_SUPPORT_DN

**Message**: redistribute only support DN

**Action**: This operation only supports redistribution of DN groups.

### YAS-01375:ERR_DSTB_PN_ROUTE_MISSING

**Message**: PN route system table is empty

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01376:ERR_DSTB_PN_ROUTE_VERSION_OUTDATED

**Message**: PN node received an outdated version: %lu of the route, pn local version is %lu

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01377:ERR_DSTB_SCALER_INVALID_NODE

**Message**: the scale in/out node is invalid: %u-%u

**Action**: The node for the operation is invalid; please input the correct node.

### YAS-01378:ERR_DSTB_CONTEXT_MISMATCH

**Message**: plan cache's context is mismatch on DN

**Action**: Internal transient error; the system will recover automatically. Please ignore.

### YAS-01379:ERR_DSTB_PLAN_CONTEXT_MISMATCH

**Message**: plan cache's planContext is mismatch on DN

**Action**: Internal transient error; the system will recover automatically. Please ignore.

### YAS-01380:ERR_DSTB_EXCLUSIVE_FEATURE

**Message**: %s is only supported in distributed database

**Action**: This operation is only supported in a distributed environment.

### YAS-01381:ERR_DSTB_EXECUTE_START_NO_CONTEXT

**Message**: dn execute start with no context, need re execute

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01400:ERR_DSTB_CM_INVALID_MSG_TYPE

**Message**: invalid cluster manager message type: %d

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01401:ERR_DSTB_CM_INVALID_TIMER_TYPE

**Message**: invalid cluster manager timer type: %d

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01402:ERR_DSTB_CM_INVALID_GROUP_ID

**Message**: invalid group id: %d

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01403:ERR_DSTB_CM_GROUP_ALREADY_EXISTS

**Message**: group already exists: %d 

**Action**: The group already exists; please do not add it again.

### YAS-01404:ERR_DSTB_CM_GROUP_ID_NOT_FOUND

**Message**: group is not found for group id: %d

**Action**: Cluster information is synchronizing; please try again later.

### YAS-01405:ERR_DSTB_CM_GROUP_IS_EMPTY

**Message**: group is empty: %d

**Action**: No nodes in the group; please add nodes or delete the group.

### YAS-01406:ERR_DSTB_CM_GROUP_TYPE_NOT_FOUND

**Message**: group is not found for type: %d

**Action**: Cluster information is synchronizing; please try again later.

### YAS-01407:ERR_DSTB_CM_NODE_ALREADY_EXISTS

**Message**: node already exists: %d-%d

**Action**: The node already exists; please do not add it again.

### YAS-01408:ERR_DSTB_CM_NODE_ID_NOT_FOUND

**Message**: node is not found for node id: %d-%d

**Action**: Cluster information is synchronizing; please try again later.

### YAS-01409:ERR_DSTB_CM_PRIMARY_NODE_NOT_FOUND

**Message**: primary node not found for group: %u

**Action**: Please confirm if a primary node exists; if it does, please retry after some time; if not, please troubleshoot distributed cluster faults and retry after the primary node is restored.

### YAS-01410:ERR_DSTB_CM_ENDPOINT_NOT_FOUND

**Message**: endpoint is not found: %d

**Action**: Cluster information is synchronizing; please try again later.

### YAS-01411:ERR_DSTB_CM_WAIT_MSG_TIMEOUT

**Message**: cluster manager wait message timeout: %d

**Action**: Internal transient error; the system will recover automatically. Please ignore.

### YAS-01413:ERR_DSTB_CM_CLUSTER_ID_CANNOT_MODIFY

**Message**: cluster id cannot be modified

**Action**: The cluster's globally unique ID cannot be changed externally.

### YAS-01414:ERR_DSTB_CM_TAG_TYPE_NOT_MATCHED

**Message**: unexpected type of cluster manager message tag

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01415:ERR_DSTB_CM_TLV_TAG_MISSING

**Message**: cluster manager TLV tag is missing

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01416:ERR_DSTB_CM_UNKNOWN_TLV_TAG

**Message**: unknown cluster manager TLV tag(%d) of %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01417:ERR_DSTB_CM_DUPLICATE_TLV_TAG

**Message**: duplicate cluster manager TLV tag(%d) of %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01418:ERR_DSTB_CM_UNEXPECTED_TLV_TAG

**Message**: expected TLV tag(%d) of %s but actually got %d

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01419:ERR_DSTB_CM_INVALID_ADDRESS

**Message**: invalid cluster manager address parameter

**Action**: The CM_ADDR configuration value in the configuration file is invalid; please check and correct it.

### YAS-01420:ERR_DSTB_CM_NOT_PRIMARY_NODE

**Message**: the node is not primary

**Action**: Internal transient error; the system will recover automatically. Please ignore.

### YAS-01421:ERR_DSTB_CM_LATEST_CLUSTER_VERSION

**Message**: the cluster version is latest

**Action**: Internal transient error; the system will recover automatically. Please ignore.

### YAS-01422:ERR_DSTB_CM_NO_VALID_NODE_FOR_PULLING

**Message**: no valid node for pulling cluster information

**Action**: Internal transient error; the system will recover automatically. Please ignore.

### YAS-01423:ERR_DSTB_CM_NORMAL_NODE_NOT_FOUND

**Message**: normal node not found for %s type

**Action**: Please confirm whether there are normal nodes of this type in the current cluster; if there are, please retry after some time; if not, please troubleshoot distributed cluster faults and retry after normal nodes are restored.

### YAS-01424:ERR_DSTB_CM_NO_VALID_MN_FOR_REGISTER

**Message**: no valid MN node for node register

**Action**: Please check if the CM_ADDR information in the configuration file is correct.

### YAS-01425:ERR_DSTB_CM_REGISTER_TIMEOUT

**Message**: register failed until timeout

**Action**: The reported node has timed out while registering with the primary MN; please ensure the primary MN is reachable from this node and retry.

### YAS-01426:ERR_DSTB_CM_REGISTER_VERIFY_FAILED

**Message**: failed to register verify

**Action**: The reported node is attempting to register with the primary MN of another cluster; please check if the CM_ADDR address in the configuration file is correct.

### YAS-01427:ERR_DSTB_CM_INVALID_NOTIFY_TYPE

**Message**: invalid cluster manager notify type(%s)

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01428:ERR_DSTB_CM_NOT_INITED

**Message**: cluster manager is not inited

**Action**: The CM module has not been initialized; please try again later.

### YAS-01429:ERR_DSTB_CM_NOT_STARTED

**Message**: cluster manager has not been started

**Action**: The CM module has not been started; please try again later.

### YAS-01430:ERR_DSTB_CM_NOTIFY_TYPE_NOT_MATCHED

**Message**: unexpected type of cluster manager notify

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01431:ERR_DSTB_CM_NOTIFY_TARGET_NODE_NOT_FOUND

**Message**: cluster manager notify target nodes not found

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01432:ERR_DSTB_CM_PULL_TIMEOUT

**Message**: pull failed until timeout

**Action**: The node timed out while pulling information from the primary node; please ensure the primary MN is reachable from this node and retry.

### YAS-01433:ERR_DSTB_CM_INVALID_UPDATE_NODE_TYPE

**Message**: invalid cluster manager update node type(%s)

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01434:ERR_DSTB_CM_NODE_ABNORMAL

**Message**: the node(%u-%u) is abnormal

**Action**: There are node or network anomalies in the cluster; please repair the node or network and retry.

### YAS-01435:ERR_DSTB_CM_NODE_NOT_FOUND

**Message**: node not found for %s type

**Action**: Cluster information is synchronizing; please try again later.

### YAS-01436:ERR_DSTB_CM_INVALID_PROBE_TYPE

**Message**: invalid cluster manager probe type(%s)

**Action**: Internal transient error; the system will recover automatically. Please ignore.

### YAS-01437:ERR_DSTB_CM_OP_REQUIRE_PRIMARY_MN

**Message**: cluster manager operation can only be executed on the primary mn

**Action**: Cluster management operations are only permitted on the primary MN node.

### YAS-01438:ERR_DSTB_CM_GROUP_NOT_FOUND_BY_TYPE

**Message**: group is not found for type: %s

**Action**: Cluster information is synchronizing; please try again later.

### YAS-01439:ERR_DSTB_CM_LIST_ITEM_SIZE_NOT_MATCH

**Message**: list itemSize is not match, expect:%u, out:%u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01440:ERR_DSTB_CM_REPEAT_SET_DATA_PATH

**Message**: set data path for node(%u-%u) repeatedly, current path: %s

**Action**: Please do not repeatedly set the data path for the node.

### YAS-01441:ERR_DSTB_CM_INVALID_GET_NODE_LIST_PARAM

**Message**: invalid cluster manager get node list param, role:%s, type:%s, groupId:%u, state:%s, output type:%s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01442:ERR_DSTB_CM_NODE_NOT_FOUND_BY_PARAM

**Message**: node not found by role:%s, type:%s, groupId:%u, state:%s, output type:%s

**Action**: Please confirm if there are nodes matching the search criteria in the current cluster, and retry later.

### YAS-01443:ERR_DSTB_CM_UNSUPPORT_DELETE_GROUP_TYPE

**Message**: group(%u) type(%s) is not support delete

**Action**: This group type does not support delete operations.

### YAS-01444:ERR_DSTB_CM_VERITY_DELETE_GROUP

**Message**: failed to delete group(%u), the number of groups must not be less than 1

**Action**: The number of any type of groups in the cluster cannot be deleted down to zero; use a force delete group command if necessary.

### YAS-01445:ERR_DSTB_CM_UNSUPPORT_NODE_TYPE

**Message**: the task(%s) % PRId64 is not support %s

**Action**: This task type is not currently supported for operations on this type of node.

### YAS-01446:ERR_DSTB_CM_ALTER_NODE_PARAM_REMOTE

**Message**: failed to remote(%u-%u,%u) alter ha param, error code: %u, error msg: %s

**Action**: Failed to modify the HA parameter; please check if the node is functioning normally and retry once confirmed.

### YAS-01447:ERR_DSTB_CM_UNSUPPROT_ALTER_LOCAL_HA_PARAM

**Message**: unsupport alter local(%u-%u) ha param

**Action**: Modifying local node HA parameters is not supported; please verify if the input node ID is correct.

### YAS-01448:ERR_DSTB_CM_INVALID_TASK_TYPE

**Message**: invalid cluster manager task type

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01449:ERR_DSTB_CM_GET_TASK_INFO

**Message**: failed to get %s task info, error msg: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01450:ERR_DSTB_CM_NOTIFY_TIMEOUT

**Message**: notify state %s to cn or mn timeout %lu ms

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01451:ERR_DSTB_CM_NOTIFY_STATE

**Message**: failed to notify state %s to cn or mn

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01452:ERR_DSTB_CM_PN_NODE_ID_NOT_FOUND

**Message**: PN node is not found, node id: %d-%d

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01453:ERR_DSTB_CM_PN_NODES_GREATER_THAN_CHUNKS

**Message**: the number of PN nodes %u in group %u is greater than the number of chunks %u in the target dataspace

**Action**: The number of PN nodes in this group exceeds the current number of chunks; please reduce the number of PN nodes in this group.

### YAS-01454:ERR_DSTB_CM_GROUP_TYPE_NOT_MATCH

**Message**: group[%u] is not %s

**Action**: The queried group ID does not match the expected group type; please check if the expected group type is reasonable.

### YAS-01455:ERR_DSTB_CM_CLUSTER_SINGLE_CLUSTER_PERMITTED

**Message**: only a single distributed cluster is permitted to be created 

**Action**: Only one distributed cluster is allowed to be created.

<span id="errno1501" name="errno1501"></span>

### YAS-01600:ERR_DSTB_PUB_NODE_EMPTY

**Message**: pub push node is empty

**Action**: The push node is empty; please check the current cluster status.

### YAS-01601:ERR_DSTB_PUB_QUEUE_SIZE_EXCEED

**Message**: pub queue exceeds max size

**Action**: The push queue is full; please check the push view to see the current task status.

### YAS-01602:ERR_DSTB_PUB_TASK_ID_NO_MATCH

**Message**: pub task id: %lu is not matched

**Action**: The push task ID does not match; please check the push view and retry.

### YAS-01603:ERR_DSTB_COMMIT_PART_FAIL

**Message**: part nodes have not been committed successfully, please wait for the synchronization to complete

**Action**: The statement was executed successfully, but some nodes have not yet synchronized; please check for any node anomalies. If there are no anomalies or they have been resolved, please wait for synchronization to complete.

### YAS-01604:ERR_DSTB_COMMIT_FAIL

**Message**: distributed commit failed and the operation will be rolled back, reason: %s

**Action**: The distributed commit failed; the current operation will be rolled back.

### YAS-01650:ERR_DSTB_MM_USER_OID_MISMATCHED

**Message**: user oid is mismatched

**Action**: The execution failed; the current system has unhandled abnormal DDL, please wait until recovery is complete and retry.

### YAS-01651:ERR_DSTB_MM_ABNORMAL_DDL_EXCEEDS

**Message**: the number of abnormal DDL exceeds upper limit

**Action**: The number of abnormal DDL exceeds the limit; please wait until the system is restored to normal and retry.

### YAS-01652:ERR_DSTB_MM_INVALID_PARTITIONS

**Message**: invalid partition num, expected: %d, actual: %d

**Action**: The number of partitions is incorrect; please correct it and retry.

### YAS-01653:ERR_DSTB_MM_PROFILE_OID_MISMATCHED

**Message**: profile oid is mismatched

**Action**: The execution failed; the current system has unhandled abnormal DDL, please wait until recovery is complete and retry.

### YAS-01654:ERR_DSTB_MM_PN_NODES_EMPTY

**Message**: the number of normal PN nodes in group %d is 0

**Action**: There are no normal PN nodes; please ensure there is at least one PN node before proceeding.

### YAS-01655:ERR_DSTB_MM_OID_MISMATCHED

**Message**: oid is mismatched

**Action**: The execution failed; the current system has unhandled abnormal DDL, please wait until recovery is complete and retry.

### YAS-01700:ERR_DSTB_TASK_INVALID_TYPE

**Message**: the task type %u is invalid

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01701:ERR_DSTB_TASK_INVALID_TYPE_NAME

**Message**: the task type name '%s' is invalid

**Action**: The task type name is incorrect; please confirm if the name is valid.

### YAS-01702:ERR_DSTB_TASK_INVALID_ROOT_ID

**Message**: the root task id % PRId64 is invalid

**Action**: The root task ID is incorrect; please check if the input parameter is correct.

### YAS-01703:ERR_DSTB_TASK_STATUS_ERROR

**Message**: the status of task % PRId64 is error: %s

**Action**: The current task status does not meet the operational requirements; please verify.

### YAS-01704:ERR_DSTB_TASK_NOT_START

**Message**: the task manager is not started

**Action**: The task management module has not been started; please retry after some time.

### YAS-01705:ERR_DSTB_TASK_MESSAGE_ERROR

**Message**: the task message error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01706:ERR_DSTB_TASK_CONFLICT

**Message**: the task % PRId64 operation '%s' conflict with before '%s'

**Action**: The task conflicts with a previous task; please confirm and address the issue.

### YAS-01707:ERR_DSTB_TASK_TOO_MANY

**Message**: too many tasks run at the same time

**Action**: There are too many tasks being processed; please retry after some time.

### YAS-01708:ERR_DSTB_TASK_CANCEL

**Message**: the task % PRId64 operation '%s' has been canceled

**Action**: The task has been canceled or interrupted; please confirm and retry.

### YAS-01709:ERR_DSTB_TASK_NOT_FOUND

**Message**: the task % PRId64 is not found

**Action**: The task was not found; please confirm and retry.

### YAS-01710:ERR_DSTB_TASK_REQUIRE_PRIMARY_MN

**Message**: task operation can only be executed on the primary mn

**Action**: Task management operations are only permitted on the primary MN node.

### YAS-01711:ERR_DSTB_TASK_REMOTE_EXEC_ERROR

**Message**: the %s task remote execute fail, error code: %u

**Action**: The remote task execution failed; please confirm and retry.

### YAS-01750:ERR_DSTB_META_AGENT_INVALID_MSG

**Message**: meta agent receives an invalid message: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01751:ERR_DSTB_META_AGENT_WAIT_TIMEOUT

**Message**: meta agent times out waiting for a response message

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01752:ERR_DSTB_META_AGENT_INVALID_ENTRY_COUNT

**Message**: unexpected entry count (expected: %d, got: %d) of %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01753:ERR_DSTB_META_AGENT_INSUFFICIENT_MEMORY

**Message**: insufficient memory (need: % PRIu32 , remaining: % PRIu32 ) of %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01754:ERR_DSTB_META_AGENT_NOTIFY_TARGET_NODE_NOT_FOUND

**Message**: meta agent notify target nodes not found

**Action**: Internal error, please contact our technical support for resolution.

### YAS-01755:ERR_DSTB_META_AGENT_INVALID_NOTIFY_TYPE

**Message**: invalid meta agent notify type(%s)

**Action**: Internal error, please contact our technical support for resolution.

## KERNEL

<span id="errno2001" name="errno2001"></span>

### YAS-02001:ERR_ANK_CREATE_DATABASE

**Message**: failed to create database, reason: %s

**Action**: Please refer to the underlying error code action.

### YAS-02002:ERR_ANK_EXCEED_CTRL_LIMIT

**Message**: maximum number of control items is %u

**Action**: The number of ctrl files exceeds the limit; please do not add more ctrl files.

### YAS-02003:ERR_ANK_SET_MMS_AUTOEXTEND

**Message**: cannot set datafile autoextend of memory mapped tablespace

**Action**: Please do not set the autoextend property for mms tablespaces.

### YAS-02004:ERR_ANK_WRITE_FILE

**Message**: failed to write file '%s'

**Action**: Writing the file failed; please check the underlying error codes for specific reasons.

### YAS-02005:ERR_ANK_INVALID_FILE

**Message**: invalid file: %s, the reason: %s

**Action**: The Data File is corrupted; you may try to repair it using Standby Database or restore from backup.

### YAS-02006:ERR_ANK_TOO_MANY_ARCHIVE

**Message**: maximum number of archive logs is %u

**Action**: The number of archive files exceeds the limit; please try to clean up unnecessary archive files.

### YAS-02007:ERR_ANK_NOFREE_EXTENTS

**Message**: no free extent in tablespace %s

**Action**: The tablespace is out of space; please enable auto-extend for the tablespace or add free storage files to the tablespace.

### YAS-02008:ERR_ANK_TOO_LARGE_SEGMENT

**Message**: maximum block count of segment is %u

**Action**: The current segment exceeds the size limit; you may try to clean up the segment or switch to a partitioned table.

### YAS-02009:ERR_ANK_NO_FREE_CURSORS

**Message**: no free cursors in cursor pool

**Action**: Current concurrency is too high, leading to insufficient cursors; please increase the SHARED_POOL_SIZE and CURSOR_POOL_SIZE system parameters to increase the available cursor buffer size.

### YAS-02010:ERR_ANK_USER_NOT_FOUND

**Message**: user '%s' does not exist

**Action**: The user does not exist; please enter the correct username.

### YAS-02011:ERR_ANK_LARGEPOOL_BUSY

**Message**: no free blocks in large pool

**Action**: Current concurrency is too high, leading to insufficient large pool space; please retry later.

### YAS-02012:ERR_ANK_OBJECT_NOT_FOUND

**Message**: %s does not exist

**Action**: The object does not exist; please enter the correct object name.

### YAS-02013:ERR_ANK_OBJECT_ALREADY_EXISTS

**Message**: name is already used by an existing object

**Action**: The name already exists; please enter a unique name or modify or delete the existing object.

### YAS-02014:ERR_ANK_TOO_LARGE_ROW_SIZE

**Message**: maximum number of row size is %u

**Action**: The maximum row length limit is exceeded; please reduce the current row size or split the table.

### YAS-02015:ERR_ANK_TOO_MANY_PENDING_TRANS

**Message**: too many pending transactions in instance %u, which should less than %u

**Action**: If in an ISC Distributed Cluster Deployment environment, it indicates too many unresolved distributed transactions; please retry later. If in YAC/Distributed Cluster Deployment environment, you can try switching instances to perform operations.

### YAS-02016:ERR_ANK_NO_FREE_UNDO_BLOCK

**Message**: no free undo blocks

**Action**: There are no available undo pages for use; please retry later.

### YAS-02018:ERR_ANK_NO_FREE_LOCKS

**Message**: no free items in %s pool

**Action**: Lock space is insufficient; please retry later.

### YAS-02019:ERR_ANK_TOO_MANY_USERS

**Message**: number of user exceeds limit

**Action**: The maximum user limit has been exceeded; please clean up users and retry.

### YAS-02020:ERR_ANK_SNAPSHOT_TOO_OLD

**Message**: snapshot too old

**Action**: Increase the value of the UNDO_RETENTION parameter or use a larger undo tablespace.

### YAS-02021:ERR_ANK_INVALID_ROWID

**Message**: invalid ROWID

**Action**: Check the ROWID format and enter the correct ROWID format; ROWID format: objectId, spaceId, fileId, blockId, dir.

### YAS-02022:ERR_ANK_INVALID_SAVEPOINT

**Message**: savepoint %s does not exist

**Action**: The savepoint does not exist; please enter the correct savepoint.

### YAS-02023:ERR_ANK_DEADLOCK_FOUND

**Message**: deadlock detected while waiting for resource

**Action**: A deadlock occurred due to resource conflicts; please wait for the database to handle it automatically and retry later.

### YAS-02024:ERR_ANK_LOCK_TIMEOUT

**Message**: lock wait timeout, wait time %u milliseconds

**Action**: Lock wait has timed out; please retry later.

### YAS-02025:ERR_ANK_VM_ERROR

**Message**: no free space in virtual memory pool

**Action**: The virtual memory pool space is insufficient; please adjust the VM_BUFFER_SIZE parameter according to usage scenarios.

### YAS-02026:ERR_ANK_RECOVERY_INCONSISTENCY

**Message**: the database cannot recover to a consistent status

**Action**: The redo files are corrupted; please use recovery techniques such as backup recovery.

### YAS-02027:ERR_ANK_REJECT_IP

**Message**: the incoming address %s banned by user profile %s

**Action**: The login IP is banned; please modify the user profile or retry with a different IP.

### YAS-02028:ERR_ANK_SI_CONFLICT

**Message**: cannot serialize access for this transaction

**Action**: Serializable transaction conflict; please retry later.

### YAS-02029:ERR_ANK_SET_TRANSACTION

**Message**: SET TRANSACTION must be the first statement of transaction

**Action**: SET TRANSACTION must be the first statement in a transaction.

### YAS-02030:ERR_ANK_DUPLICATED_KEY

**Message**: unique constraint%s violated

**Action**: Remove the unique constraint or do not insert this row.

### YAS-02031:ERR_ANK_PK_COLUMN_NOT_MATCH

**Message**: no matching unique or primary key for this column-list

**Action**: Use the ALL_CONS_COLUMNS directory view to find the correct column names.

### YAS-02032:ERR_ANK_FK_DATATYPE_NOT_MATCH

**Message**: column type is incompatible with referenced column type

**Action**: Select a compatible data type for the referenced column, and ensure that the character column sorting rules in the foreign key match the corresponding column's sorting rules in the primary key.

### YAS-02033:ERR_ANK_FK_PARENT_NOT_FOUND

**Message**: foreign key constraint (%s.%s) violated: parent key not found

**Action**: Remove the foreign key or add a matching primary key.

### YAS-02034:ERR_ANK_FK_CHILD_FOUND

**Message**: foreign key constraint (%s.%s) violated: child found

**Action**: Remove the data in the child table that depends on the parent table or disable the constraint before retrying the operation.

### YAS-02035:ERR_ANK_SEQ_CANNOT_INITIALIZED

**Message**: sequence %s.NEXTVAL %s and cannot be instantiated

**Action**: Modify the sequence to allow requesting new values.

### YAS-02036:ERR_ANK_DB_ALREADY_OPEN

**Message**: the database is already open

**Action**: If you want to open a new database on this instance, please close the instance first, then start it and retry.

### YAS-02037:ERR_ANK_DB_MUST_MOUNTED

**Message**: the database must be mounted and not open

**Action**: Ensure the database status is correct before retrying.

### YAS-02038:ERR_ANK_COLUMN_ALREADY_EXIST

**Message**: column %s already exists

**Action**: A column with the same name already exists; delete the original column or rename it, then try again, or modify the conflicting column name in the statement.

### YAS-02039:ERR_ANK_INDEX_COLUMNS_EXCEED

**Message**: maximum number of columns indexed is %d

**Action**: Reduce the number of columns involved in the index to not exceed the indicated number.

### YAS-02040:ERR_ANK_PARTITION_UNUSABLE

**Message**: index(%s.%s) or index partition is unusable

**Action**: Delete the specified index, or rebuild the specified index, or regenerate the unusable index partition.

### YAS-02041:ERR_ANK_INVALID_TABLESPACE

**Message**: cannot create segments in undo, cache or swap tablespace

**Action**: Check the tablespace name and re-specify the tablespace.

### YAS-02042:ERR_ANK_DB_ROLLBACKING

**Message**: cannot execute %s when the database is rolling back

**Action**: DDL operations described in the error cannot be executed while the database is rolling back; please wait for the rollback to complete and try again.

### YAS-02043:ERR_ANK_COLUMNS_INDEXED

**Message**: columns have been indexed

**Action**: An index already exists on the column; delete the original index on the column or modify the columns used by the newly created index before trying again.

### YAS-02044:ERR_ANK_FILE_ALREADY_EXISTS

**Message**: file '%s' already exists

**Action**: A file with the same name already exists; delete or rename the original file or modify the conflicting filename in the current statement, then try again.

### YAS-02045:ERR_ANK_FILE_NOT_EXISTS

**Message**: file '%s' does not exist

**Action**: Specify the name or number of an existing file of the correct type.

### YAS-02046:ERR_ANK_SPACE_DROP_FISRT_FILE

**Message**: cannot drop the first file of tablespace %s

**Action**: Cannot delete the first Data File used to create a tablespace.

### YAS-02047:ERR_ANK_SPACE_OFFLINE

**Message**: the tablespace '%s' is offline

**Action**: Bring the tablespace online.

### YAS-02048:ERR_ANK_DATAFILE_NOT_MEMBER

**Message**: file '%s' is not a member of tablespace %s

**Action**: Retry this option using the correct data file or temporary file.

### YAS-02049:ERR_ANK_DATAFILE_NOT_EMPYT

**Message**: file '%s' is not empty

**Action**: Cannot delete a non-empty Data File.

### YAS-02050:ERR_ANK_DATABASE_BUSY

**Message**: the database is busy, try again later

**Action**: Please wait for other operations on the database to complete before retrying.

### YAS-02051:ERR_ANK_FK_ALREADY_EXIST

**Message**: such a referential constraint already exists in the table

**Action**: A foreign key constraint for this reference already exists in the table; delete the original foreign key or modify the dependencies in the statement and retry.

### YAS-02052:ERR_ANK_INDEX_ENFORCED

**Message**: cannot drop index used for enforcement of unique/primary key

**Action**: Delete the constraint instead of the index.

### YAS-02053:ERR_ANK_USED_BY_FOREIGN

**Message**: unique/primary keys in table referenced by foreign keys

**Action**: Delete the foreign key constraints in other tables that rely on this table before retrying.

### YAS-02054:ERR_ANK_DROP_ALL_COLUMN

**Message**: cannot drop all columns in a table

**Action**: Ensure at least one column remains in the table after performing the drop command.

### YAS-02055:ERR_ANK_ROLE_NOT_FOUND

**Message**: role '%s' does not exist

**Action**: This role does not exist; please specify a valid role name.

### YAS-02056:ERR_ANK_LOCK_NOWAIT

**Message**: lock wait timeout with NOWAIT specified

**Action**: Please retry if necessary or increase the timeout period.

### YAS-02057:ERR_ANK_PRIMARY_DUPLICATED

**Message**: table can have only one primary key

**Action**: A table can only have one primary key constraint; choose the required primary key and delete any additional primary key constraints.

### YAS-02058:ERR_ANK_KEY_SIZE_EXCEED

**Message**: %s size exceeds limit %u

**Action**: The column-based key length exceeds the limit; modify the column length to be no greater than the High Value, or define the key on a column that meets the criteria.

### YAS-02059:ERR_ANK_DBVERSION_INCOMPATIBLE

**Message**: control file version %d.%d.%d is incompatible with YashanDB version %d.%d.%d

**Action**: Please restart the database using a compatible software version; if you need to upgrade the software version, please follow the upgrade process.

### YAS-02060:ERR_ANK_TOO_MANY_COLUMNS

**Message**: maximum number of column count is %u

**Action**: If the error results from a CREATE command, please reduce the number of columns in the command and resubmit it. If the error results from an ALTER TABLE command, there are two options:

1) If the table contains unused columns, perform an ALTER TABLE to delete the unused columns before adding new columns.
2) Reduce the number of columns in the command and resubmit.

### YAS-02061:ERR_ANK_INVALID_MAXSIZE

**Message**: number of MAXSIZE must be between % PRIu64  and % PRIu64 

**Action**: Please enter a valid MAXSIZE value.

### YAS-02062:ERR_ANK_OBJECT_TRUNC_DROP

**Message**: object has been truncated or dropped

**Action**: The object has been deleted; please do not operate on this object, or restore the object and try again.

### YAS-02063:ERR_ANK_XACT_READONLY

**Message**: read-only transaction in current session

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02064:ERR_ANK_SPACE_NOT_EMPTY

**Message**: the tablespace is not empty

**Action**: Please use the INCLUDING CONTENTS option to drop objects under the tablespace.

### YAS-02065:ERR_ANK_DROP_BUILTIN_SPACE

**Message**: cannot drop built-in tablespace

**Action**: This operation is not allowed.

### YAS-02066:ERR_ANK_SPACE_FK_CONTRAINTS

**Message**: index in the tablespace is used by foreign key of other tablespace

**Action**: Please delete the related foreign key constraints before performing the above operations.

### YAS-02067:ERR_ANK_DROP_DEFAULT_SPACE

**Message**: cannot drop the default tablespace of user

**Action**: Please reset the default tablespace of the user before performing the above operation.

### YAS-02068:ERR_ANK_DATAFILE_UNAVAILABLE

**Message**: datafile %u-%u has been dropped

**Action**: Please verify whether the file exists.

### YAS-02069:ERR_ANK_ALTER_NOTNULL_COLUMN

**Message**: column contains NULL values, cannot alter to NOT NULL

**Action**: Update the NULL values in the table column to non-null before setting the column to NOT NULL.

### YAS-02070:ERR_ANK_ADD_NOTNULL_COLUMN

**Message**: table must be empty to add NOT NULL column

**Action**: Execute the statement without the NOT NULL option, or set a non-null default value for the column.

### YAS-02071:ERR_ANK_DB_ALREADY_MOUNTED

**Message**: the database is already mounted

**Action**: There is no need to mount the database again.

### YAS-02072:ERR_ANK_DB_NOT_MOUNTED

**Message**: the database is not mounted

**Action**: Please mount the database first.

### YAS-02073:ERR_ANK_SAVE_REDO_HEAD

**Message**: failed to save head of redo file %s

**Action**: There was a filesystem error that caused the redo file writing to fail; please check the system error.

### YAS-02074:ERR_ANK_REDO_RECOVER_GAP

**Message**: cannot recover to current redo file, the redo files may be discontinuous

**Action**: This may occur when old redo files have not been cleaned during a full recovery and the redo files have become non-contiguous with Archive Log Files. Please clean old redo files, ensure that archiving and old redo files are contiguous, or execute a PITR recovery.

If the last PITR recovery or full database flashback was not completed or failed and you continue to use the database, this error may occur; this situation can only be resolved by cleaning the environment and re-executing PITR or the full database flashback operation.

### YAS-02075:ERR_ANK_REDO_SIZE_TOO_SMALL

**Message**: the minimum size of redo file is % PRIu64 

**Action**: The minimum size of the redo log file is influenced by the parameters DB_BLOCK_SIZE, MAX_SESSIONS, and REDO_BUFFER_SIZE. The minimum value can be referenced with the formula: `DB_BLOCK_SIZE * MAX_SESSIONS * 8 + REDO_BUFFER_SIZE / 2`. The value shown in the error message represents the calculated result (in bytes) based on the expected parameter values you intend to modify. However, the actual size of the existing redo log files is smaller than this value. If you encounter this error, please take one of the following solutions: 

- Solution 1: Adjust the corresponding configuration parameter values so that the calculated minimum size of the redo log file is smaller than the actual size of the existing redo log files (i.e., the value shown in the error message, in bytes).

- Solution 2: First, create at least three new redo log files, each larger than the expected minimum size (for example, calculated based on the recommended parameter values), then archive and delete the old redo log files.

### YAS-02076:ERR_ANK_REDO_COUNT_TOO_SMALL

**Message**: the count of instance %u %s redo log files cannot be less than %u

**Action**: Please create at least the minimum number of redo log files.

### YAS-02077:ERR_ANK_REDO_IN_USE

**Message**: redo log file '%s' is in use

**Action**: If the redo status is CURRENT, perform a log switch, then execute a checkpoint to change this redo status to INACTIVE before performing the above operation. The status of the redo file can be viewed through V$LOGFILE.

### YAS-02078:ERR_ANK_DB_NOT_OPEN

**Message**: the database is not open

**Action**: Please open the database first.

### YAS-02079:ERR_ANK_NOT_ARCHIVE_MODE

**Message**: archive log mode must be enabled when %s

**Action**: Please enable Archiving Mode before performing the above operations.

### YAS-02080:ERR_ANK_BASETABLE_NOT_HASH

**Message**: STORE-IN (tablespace list) can only be specified for a LOCAL index on a Hash part table

**Action**: Only Local Index on Hash Partition tables can use the STORE-IN clause; this clause is not applicable in this situation, please do not use the STORE-IN clause.

### YAS-02081:ERR_ANK_COLUMN_NOT_FOUND

**Message**: column %s does not exist

**Action**: Check if the column name is spelled correctly.

### YAS-02082:ERR_ANK_TOO_MANY_ROWIDS

**Message**: maximum number of rowid count is %u

**Action**: Please ensure that the number of rowids does not exceed the maximum allowable rowid count.

### YAS-02083:ERR_ANK_ALTER_BUILTIN_OBJECT

**Message**: built-in object %s cannot be altered

**Action**: It is not allowed to modify built-in objects.

### YAS-02084:ERR_ANK_DROP_BUILTIN_OBJECT

**Message**: built-in object %s cannot be dropped

**Action**: It is not allowed to delete built-in objects.

### YAS-02085:ERR_ANK_TRUNC_BUILTIN_OBJECT

**Message**: built-in object %s cannot be truncated

**Action**: It is not allowed to truncate built-in objects.

### YAS-02087:ERR_ANK_INVALID_ARCH_PATH

**Message**: invalid archive log path: %s

**Action**: Please enter a correct archive path.

### YAS-02088:ERR_ANK_TOO_MANY_REDO

**Message**: maximum number of redo files is %u

**Action**: Please do not create more than the maximum number of redo files.

### YAS-02089:ERR_ANK_TOO_MANY_DATAFILE

**Message**: maximum number of datafile count is %u

**Action**: Please do not create more than the maximum number of data files.

### YAS-02090:ERR_ANK_TOO_MANY_TABLESPACE

**Message**: maximum number of tablespace count is %u

**Action**: Please do not create more than the maxinum number of tablespaces.

### YAS-02091:ERR_ANK_TOO_MANY_SAVEPOINT

**Message**: number of savepoint exceeds limit

**Action**: End the current transaction or release unnecessary savepoints.

### YAS-02092:ERR_ANK_UNDO_SIZE_EXCEED

**Message**: maximum size of undo each use is %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02093:ERR_ANK_SPACE_NOT_FOUND

**Message**: tablespace '%s' does not exist

**Action**: The specified tablespace does not exist, confirm and correct the tablespace name before re-executing.

### YAS-02094:ERR_ANK_HANDLER_CANCEL

**Message**: current session has been killed or canceled

**Action**: The connection has been disconnected, please attempt to reconnect.

### YAS-02095:ERR_ANK_TOO_MANY_VM_OPEN

**Message**: maximum size of open virtual memory is %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02096:ERR_ANK_SEQUENCE_PARAM

**Message**: sequence param %s error, %s

**Action**: Please enter a valid sequence attribute value.

### YAS-02097:ERR_ANK_INDEX_NOTSUPPORT_TYPE

**Message**: cannot create %s on expression with datatype %s

**Action**: The data type of the column does not support index or sort key creation, modify the column's data type, or refrain from creating an index or sort key on this column.

### YAS-02099:ERR_ANK_BTREE_LEVEL_EXCEED

**Message**: btree index level exceeds limit %u

**Action**: The data scale exceeds the limit for building an index; partition the data or consider changing the indexed columns.

### YAS-02100:ERR_ANK_XACT_INPROGRESS

**Message**: must end current transaction before executing the operation

**Action**: Execute COMMIT or ROLLBACK to end the current transaction and retry.

### YAS-02101:ERR_ANK_XA_TOO_MANY_PARTICIPAT

**Message**: too many local sessions participating in global transaction, current session is %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02102:ERR_ANK_XA_PREPARED_NOT_FOUND

**Message**: no prepared transaction found with ID % PRIu64

**Action**: The distributed transaction identified by the specified GTID was not found; query the v$2pc_pending view.

### YAS-02103:ERR_ANK_XA_BRANCH_INPROGRESS

**Message**: distributed transaction branch already attached

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02104:ERR_ANK_XACT_NOT_EXIST

**Message**: local transaction does not exist

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02105:ERR_ANK_XA_ALREADY_EXIST

**Message**: distributed transaction branch with ID % PRIu64 already exists

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02106:ERR_ANK_XA_STATUS

**Message**: distributed transaction status error, expect %s

**Action**: The distributed transaction status is invalid; query the v$2pc_pending view to confirm the status.

### YAS-02107:ERR_ANK_PARTBOUND_EXCEED

**Message**: maximum number of partbound size is %u

**Action**: Adjust the partition key based on column data size.

### YAS-02108:ERR_ANK_TOO_MANY_PARTS

**Message**: maximum number of partition count is % PRIu64

**Action**: Redesign the partition table to ensure the number of partitions does not exceed the limit.

### YAS-02109:ERR_ANK_BASETABLE_NOTPART

**Message**: underlying table of a LOCAL partitioned index must be partitioned

**Action**: Non-partitioned tables do not support creating LOCAL partition indexes; correct the statement and retry.

### YAS-02110:ERR_ANK_TOO_MANY_BUF_CHUNKS

**Message**: maximum number of extending data buffer is reached

**Action**: Buffer Cache expansion has reached the limit; no further ALTER SYSTEM statements for expansion can be executed.

### YAS-02111:ERR_ANK_INVALID_BUF_CHUNK_SIZE

**Message**: extend size of buffer must be between % PRIu64  and % PRIu64 

**Action**: Please enter a valid Buffer Cache extension size value.

### YAS-02112:ERR_ANK_WRONG_PARTITIONS_NUM

**Message**: number of partitions of LOCAL index must equal that of the underlying table

**Action**: The number of partitions for the LOCAL index must match the number of partitions in the partitioned table; retry after specifying the correct number.

### YAS-02113:ERR_ANK_WRONG_PART_UNIQUE_IDX

**Message**: partitioned columns must be a subset of the key column of the unique index

**Action**: If a partition index is needed and the table's partition key column is not a subset of the index column, it must be created as a non-unique index; otherwise, modify the table's partition key or index column to ensure the partition key forms a subset of the index columns.

### YAS-02114:ERR_ANK_XA_INVALID_GTID

**Message**: invalid or incorrectly formatted GTID

**Action**: The specified distributed GTID is incorrect; correct it and retry.

### YAS-02115:ERR_ANK_INVALID_PART_KEY

**Message**: partition key does not map to any partition

**Action**: Do not insert this record, or add a range that includes this record, or modify the partition rules to include this record.

### YAS-02116:ERR_ANK_XA_FORBID_AUTON

**Message**: forbid phase1 in autonomous transaction

**Action**: Autonomous transactions cannot be converted to distributed transactions; wait or retry.

### YAS-02117:ERR_ANK_INVALID_SCN

**Message**: specified number is not a valid system change number

**Action**: Please enter a valid SCN value.

### YAS-02118:ERR_ANK_OBJECT_NOT_PARTED

**Message**: partition maintenance operations may only be performed on partitioned object

**Action**: Confirm if the object specified in the statement is already partitioned.

### YAS-02119:ERR_ANK_PART_COLUMN_MISMATCH

**Message**: number of expressions is not equal to the number of partitioning

**Action**: The number of expressions specified during the partition query must equal the number of columns included in the partition key; please modify the expression count in the statement.

### YAS-02120:ERR_ANK_INVALID_PART_NUM

**Message**: the partition number is invalid or out-of-range

**Action**: Please enter a valid partition number.

### YAS-02121:ERR_ANK_PART_NOT_EXISTS

**Message**: specified partition %s does not exist

**Action**: Please retry with the correct partition name.

### YAS-02122:ERR_ANK_REDO_ARCH_NOT_FOUND

**Message**: redo log file or archive log file with asn %u is not found

**Action**: Check if the redo files or archive files exist, whether the storage device is online, and if the files are in the correct location, then continue trying.

### YAS-02123:ERR_ANK_REDO_BLOCK_CHECKSUM

**Message**: redo file is corrupted, block id %u, asn %u

**Action**: Find the correct redo file to attempt again, restore from backup set, or recreate the Standby Database.

### YAS-02124:ERR_ANK_REDO_LOAD

**Message**: cannot load redo data from point (reset id %u, asn %u, block id %u, lfn % PRIu64 )

**Action**: Check if the redo files exist, whether the storage device is online, and if the files are in the correct location, then continue trying.

### YAS-02125:ERR_ANK_DIST_KEY_SUBSET_UNI_CONS

**Message**: partitioned key is not a subset of unique constraint keys

**Action**: Unique constraints already exist on the table; the partition key must be a subset of the unique constraint columns; redefine the unique constraint columns or re-specify the partition key.

### YAS-02126:ERR_ANK_XA_FORBID_FORCE_COMMIT

**Message**: specified distributed transaction should be force commit without scn

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02127:ERR_ANK_PART_TYPE_MISMATCH

**Message**: type of partition does not match the table

**Action**: Please retry with the correct partition type.

### YAS-02128:ERR_ANK_INVALID_PART_BOUND

**Message**: invalid partition: %s

**Action**: Enter the correct partition name.

### YAS-02129:ERR_ANK_DUPLICATED_PART_BOUND

**Message**: partition bound value already exists in partition %d

**Action**: Reset the partition boundary value and try again.

### YAS-02130:ERR_ANK_DROP_HASH_PART

**Message**: cannot drop hash partition or subpartition

**Action**: Cannot delete Hash Partition or subpartition; please do not perform this operation.

### YAS-02131:ERR_ANK_DROP_ALL_PARTS

**Message**: cannot drop all partitions of the table

**Action**: Cannot delete all partitions of the table; please retain at least one partition.

### YAS-02132:ERR_ANK_DROP_COLUMNS

**Message**: cannot drop column %s

**Action**: This column cannot be deleted; refer to the error message for specific reasons.

### YAS-02133:ERR_ANK_UNI_CONS_NO_COMMON_COL

**Message**: primary key and unique constraints must have at least one same column

**Action**: Please redefine the constraint columns based on the error message.

### YAS-02134:ERR_ANK_TOO_MANY_INDEXES

**Message**: the count of indexes in a table exceeds the limit %d

**Action**: Reduce the number of indexes.

### YAS-02135:ERR_ANK_TABPART_CONS_VIOLATE

**Message**: partitioned table contains partitions in a different tablespace

**Action**: Find the parent table partitions that are not in the current deletion tablespace; delete the parent table first and then retry.

### YAS-02136:ERR_ANK_INDPART_CONS_VIOLATE

**Message**: partitioned index contains partitions in a different tablespace

**Action**: Find the parent index partitions that are not in the current deletion tablespace; delete the parent index first and then retry.

### YAS-02137:ERR_ANK_PARAM_MISMATCH

**Message**: parameter %s mismatch the value when create database, expect %d

**Action**: The parameter value does not match the value used to create the database; please modify the statement according to the expected value.

### YAS-02138:ERR_ANK_FORBID_ALTER_PARAM

**Message**: forbid to alter specified parameter

**Action**: This operation is not allowed.

### YAS-02139:ERR_ANK_TOO_MANY_ACS

**Message**: the count of AC in a table exceeds the limit %d

**Action**: The number of ACs on a single table has exceeded the limit; please reduce it to be no higher than the High Value.

### YAS-02140:ERR_ANK_BOUND_TOO_LARGE

**Message**: partition bound value '%s' is invalid, expected size not more than %u, actual size is %u

**Action**: Ensure that the length of the boundary value does not exceed the corresponding partition column length.

### YAS-02141:ERR_ANK_INVALID_CTRL_FILE_SIZE

**Message**: control file size must between %u and %u

**Action**: Modify the control file related parameters or check the control file size, then retry the operation.

### YAS-02142:ERR_ANK_DROP_PART_COLUMNS

**Message**: cannot drop partitioning column

**Action**: This operation is not allowed.

### YAS-02143:ERR_ANK_USER_LOGIN_DENIED

**Message**: invalid username/password, login denied

**Action**: Please enter a valid username and password combination in the correct format.

### YAS-02144:ERR_ANK_INVALID_USER_SPACE

**Message**: cannot specify %s tablespace as %s tablespace

**Action**: Specify a permanent tablespace as the default tablespace.

### YAS-02145:ERR_ANK_INVALID_USER_ID

**Message**: userId %u is already used or invalid

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02146:ERR_ANK_INVALID_DW_SIZE

**Message**: the minimum size of double write file is % PRIu64 

**Action**: You can reduce the values of DBWR_COUNT or DBWR_BUFFER_SIZE, or increase the size of the double write file. Constraints on the size of the double write file can be referred to in [CREATE DATABASE](../Development Guide/SQL Reference Manual/SQL Statements/CREATE DATABASE).

### YAS-02147:ERR_ANK_BLOCK_CORRUPTED

**Message**: the block %d-%d-%d is corrupted

**Action**: Enable bad block auto-repair functionality on the Primary Database, attempt automatic repair, or recover using the backup set.

### YAS-02148:ERR_ANK_TABLE_DEFINITION_CHANGED

**Message**: %s - table definition has changed

**Action**: If it is an old snapshot, then commit or rollback the transaction and continue; if the object's creation SCN is still greater than the system time, export the object's data, delete the object, recreate it to have a new creation SCN, import the object's data, and continue working.

### YAS-02149:ERR_ANK_FLASHBACK_TABLE_TYPE

**Message**: flashback is not allowed for the table type %s

**Action**: Check whether the table type supports flashback.

### YAS-02150:ERR_ANK_FLASHBACK_IN_XACT

**Message**: cannot flashback the table in the middle of a transaction

**Action**: End the current transaction before performing a flashback operation.

### YAS-02151:ERR_ANK_FLASHBACK_TABLES_EXCEED

**Message**: flashback too many tables, limit %d

**Action**: Please reduce the number of tables being flashed back.

### YAS-02152:ERR_ANK_TOO_LARGE_BLOCK_ROWSIZE

**Message**: total row size %u exceeds block capacity

**Action**: Please reduce the length of the rows.

### YAS-02153:ERR_ANK_DROP_CONNECTED_USER

**Message**: cannot drop a user that is currently connected

**Action**: Ensure that the user is not logged in, then retry this operation.

### YAS-02154:ERR_ANK_DROP_CASCADE_REQUIRED

**Message**: CASCADE must be specified to drop '%s'

**Action**: Please specify CASCADE and then retry this operation.

### YAS-02155:ERR_ANK_SYNONYM_INVALID

**Message**: synonym translation is no longer valid

**Action**: Modify the synonym definition to point to a valid target object.

### YAS-02156:ERR_ANK_FK_TEMP_TABLE

**Message**: unable to create referential integrity constraint on temporary table

**Action**: Cannot add foreign key constraints to temporary tables.

### YAS-02157:ERR_ANK_PART_TEMP_TABLE

**Message**: unable to partition temporary table

**Action**: Cannot create partitioned temporary tables.

### YAS-02158:ERR_ANK_INVALID_TABLE_NAME

**Message**: cannot create an object with a name matching private temporary table prefix

**Action**: Please specify creating a Private temporary table or remove the prefix 'YAS$PTT_' from the table name.

### YAS-02159:ERR_ANK_OBJ_NOT_TEMP_SPACE

**Message**: unable to create temporary object in a non-temporary tablespace %s

**Action**: Please recreate after changing the table type or tablespace.

### YAS-02160:ERR_ANK_OBJ_TEMP_SPACE

**Message**: unable to create non-temporary object in a temporary tablespace

**Action**: Please recreate after changing the table type or tablespace.

### YAS-02161:ERR_ANK_TEMP_CACHE

**Message**: no free space in global temporary table cache

**Action**: Please TRUNCATE some sessions' Global temporary tables and retry this operation.

### YAS-02162:ERR_ANK_SPACE_ALREADY_EXISTS

**Message**: tablespace '%s' already exists

**Action**: Please use a different name for the new tablespace.

### YAS-02163:ERR_ANK_TOO_MANY_PTT

**Message**: the number of private temporary tables of current session exceeded the maximum value

**Action**: Delete some Private temporary tables of the current session and retry this operation.

### YAS-02164:ERR_ANK_TOO_MANY_IPS

**Message**: the number of ip list exceeded the maximum value %u

**Action**: Please modify the configuration of the IP whitelist/blacklist and retry.

### YAS-02165:ERR_ANK_DDL_USED_GTT

**Message**: attempt to execute unsupported DDL statement on global temporary table in use

**Action**: This table is in use; this operation is not allowed, please try again later.

### YAS-02166:ERR_ANK_INDEX_ON_PTT

**Message**: unable to create index on private temporary table

**Action**: This operation is not allowed.

### YAS-02167:ERR_ANK_ALTER_PTT

**Message**: unable to alter private temporary table

**Action**: This operation is not allowed.

### YAS-02168:ERR_ANK_XA_FORBID_TEMPORARY

**Message**: forbid 2-phase when involving temporary tables

**Action**: Cannot perform distributed transactions on temporary tables; please check the environment of the SQL.

### YAS-02169:ERR_ANK_FLASHBACK_TEMP_TABLE

**Message**: unable to execute flashback on temporary table

**Action**: This operation is not allowed.

### YAS-02170:ERR_ANK_INVALID_PTT_NAME

**Message**: create a private temporary table with a name not matching 'YAS$PTT_' prefix

**Action**: Please specify creating a non-Private temporary table or add the prefix 'YAS$PTT_' to the table name.

### YAS-02171:ERR_ANK_LOOPING_CHAIN_SYNONYM

**Message**: looping chain of synonyms

**Action**: Please check the objects linked by the synonyms.

### YAS-02172:ERR_ANK_INCOMPLETE_DATABASE

**Message**: database is not created completely

**Action**: Please take appropriate actions to recreate the database or create a new control file.

### YAS-02173:ERR_ANK_BUFFER_INSUFFICIENT

**Message**: The minimum size of data buffer part is % PRIu64 

**Action**: Please enter a valid data buffer size.

### YAS-02174:ERR_ANK_COMMENT_BUILTIN_OBJECT

**Message**: built-in object %s cannot be commented

**Action**: Built-in objects cannot be commented; please do not perform this operation.

### YAS-02175:ERR_ANK_COMMENT_PTT

**Message**: temporary table does not support comment

**Action**: Temporary tables cannot have comments added; please do not perform this operation.

### YAS-02176:ERR_ANK_DUPLICATE_COLUMN_NAME

**Message**: '%s' is a duplicate column name

**Action**: Remove the duplicate column name or use a new column name and retry this operation.

### YAS-02177:ERR_ANK_DROP_DEFAULT_USER_ROLE

**Message**: specified %s cannot be dropped

**Action**: Default user roles cannot be dropped; please do not perform this operation.

### YAS-02178:ERR_ANK_INDEX_ACTION_CONFLICT

**Message**: ALTER INDEX or INDEX PARTITION %s may not be combined with other operations

**Action**: Please ensure PARTITION is the only operation specified in the ALTER INDEX statement.

### YAS-02179:ERR_ANK_COALESCE_PARTIDX_WHOLE

**Message**: a partitioned index may not be coalesced as a whole

**Action**: Please coalesce one index partition at a time.

### YAS-02180:ERR_ANK_FILE_SIZE_TOO_SMALL

**Message**: file size specified is smaller than minimum required

**Action**: Please increase the file size.

### YAS-02181:ERR_ANK_DIGEST_FILE_INVALID

**Message**: password file missing or disabled

**Action**: Regenerate the password file.

### YAS-02182:ERR_ANK_GATHER_STATS

**Message**: failed to gather statistics, reason: %s

**Action**: Resolve the corresponding issue according to the error message and retry.

### YAS-02183:ERR_ANK_MUST_USE_RESETLOGS

**Message**: must use RESETLOGS option for database open

**Action**: Open the database using the RESETLOGS option.

### YAS-02184:ERR_ANK_NOT_INCOMPLETE_RCY

**Message**: RESETLOGS option is only valid after an incomplete database recovery

**Action**: Retry without specifying RESETLOGS.

### YAS-02185:ERR_ANK_DROP_PRIMARY_KEY

**Message**: cannot drop nonexistent primary key

**Action**: The specified primary key for deletion does not exist; please re-specify an existing primary key.

### YAS-02186:ERR_ANK_DROP_UNIQUE_KEY

**Message**: cannot drop nonexistent unique key

**Action**: The specified unique key for deletion does not exist; please re-specify an existing unique key.

### YAS-02187:ERR_ANK_DROP_CONSTRAINT

**Message**: cannot drop constraint - nonexistent constraint

**Action**: Ensure that the constraint name is entered correctly.

### YAS-02188:ERR_ANK_DROP_CONS_REFERENCED

**Message**: this unique/primary key is referenced by some foreign keys

**Action**: Remove all references to this key before deleting it.

### YAS-02189:ERR_ANK_ADD_INTERVAL_PART

**Message**: ADD PARTITION is not permitted on Interval partitioned objects

**Action**: It is prohibited to perform this operation on INTERVAL partition objects. Insert a row to create a new partition.

### YAS-02190:ERR_ANK_FILE_RESIZE_VALUE

**Message**: file contains used data beyond requested resize value

**Action**: Before resizing the file, delete or move segments containing parts in that area, or choose a resize value so that only free space remains in the resized area.

### YAS-02192:ERR_ANK_RB_NOT_FOUND

**Message**: the object is not in the recycle bin

**Action**: Only objects in the recycle bin can be purged.

### YAS-02193:ERR_ANK_ACCOUNT_LOCKED

**Message**: the account is locked

**Action**: Wait for PASSWORD_LOCK_TIME or contact your DBA.

### YAS-02194:ERR_ANK_OPERATE_RECYCLEBIN_OBJ

**Message**: cannot perform DDL/DML on objects in the recycle bin

**Action**: DDL or DML operations on recycle bin objects are not allowed.

### YAS-02195:ERR_ANK_PARTSTORE_CONS_VIOLATE

**Message**: the tablespace has been used by interval partitioned table

**Action**: It is not allowed to delete or move the tablespace used by the Interval Partition table.

### YAS-02196:ERR_ANK_CHUNK_BOUND_EXCEED

**Message**: maximum length of chunk boundary is %u

**Action**: The maximum character length of the partition key boundary value has been exceeded; please shorten and retry.

### YAS-02197:ERR_ANK_DIST_TEMP_TABLE

**Message**: unable to partition temporary table

**Action**: Cannot create partitioned temporary tables.

### YAS-02198:ERR_ANK_INVALID_TEMP_TABLE_OPERATION

**Message**: operation not supported on private/global temporary table

**Action**: Cannot perform this operation on private/global temporary tables.

### YAS-02199:ERR_ANK_ALLOCATOR_ALLOC_MEM

**Message**: cannot allocate % PRIu64  bytes from %s allocator

**Action**: Appropriately increase the suggested capacity of the memory allocator.

### YAS-02200:ERR_ANK_UNSUPPORT_TEMP_TABLE

**Message**: unsupported feature with temporary table

**Action**: Ensure that the table type being operated on; temporary tables do not support this functionality.

### YAS-02201:ERR_ANK_REBUILD_PARTIDX_WHOLE

**Message**: a partitioned index may not be rebuilt as a whole

**Action**: Rebuild one partition's index at a time (using ALTER index Rebuild partition), or drop and recreate the entire index.

### YAS-02202:ERR_ANK_SEQUENCE_NUM_EXCEED

**Message**: the sequence number cannot be greater than the recovery begin point

**Action**: Reduce the input sequence number to not exceed the asn value in v$database for rcy_point.

### YAS-02203:ERR_ANK_INDEX_ON_VIEW

**Message**: unable to create index on view

**Action**: It is not possible to create indexes on views.

### YAS-02204:ERR_ANK_INVALID_ALLOCATOR_TYPE

**Message**: invalid allocator type: %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02205:ERR_ANK_INVALID_CTRL_FILES

**Message**: the control files are invalid

**Action**: The control files are corrupted, which may indicate a severe database error. Please contact our technical support for resolution.

### YAS-02206:ERR_ANK_CANNOT_ENFORCE

**Message**: specified index does not exist or cannot be used to enforce the constraint

**Action**: Specify a suitable index.

### YAS-02207:ERR_ANK_SHRINK_TABLE_NOT_SUPPORT

**Message**: unsupported table type for shrink

**Action**: Confirm the table type being operated on; LSC Table does not support the shrink operation.

### YAS-02208:ERR_ANK_CONSISTENT_WRITE

**Message**: lock conflict in consistent write

**Action**: Wait for the other consistent write to complete before retrying the process.

### YAS-02209:ERR_ANK_ENABLE_ROW_MOVEMENT

**Message**: ROW MOVEMENT is not enabled

**Action**: Enable row movement and reissue this command.

### YAS-02210:ERR_ANK_INVALID_INDEX_FOR_CONS

**Message**: specified index cannot be used to enforce the constraint

**Action**: Specify a suitable index or allow automatic index creation.

### YAS-02211:ERR_ANK_INVALID_LOBLOCATOR

**Message**: invalid lob locator

**Action**: Ensure that the LOB LOCATOR has been correctly initialized and that LOB data exists before retrying.

### YAS-02212:ERR_ANK_CREATE_SYS_TABLE

**Message**: forbid to create system table

**Action**: Creating system tables is not allowed.

### YAS-02213:ERR_ANK_INSUFFICIENT_PRIVILEGES

**Message**: insufficient privileges

**Action**: Please have the database administrator or designated security administrator grant the necessary privileges to the user.

### YAS-02214:ERR_ANK_GRANT_REVOKE_SELF

**Message**: cannot GRANT/REVOKE privileges or roles to/from yourself

**Action**: It is not permitted to grant or revoke privileges or roles to yourself; please do not perform this operation.

### YAS-02215:ERR_ANK_WRONG_GRANTEE

**Message**: cannot grant/revoke privileges or roles to/from "%s"

**Action**: Only users or roles are allowed to be granted/revoked privileges or roles.

### YAS-02216:ERR_ANK_INVALID_PRIVI_OR_ROLE

**Message**: invalid privilege or role specified

**Action**: Please enter a valid privilege name.

### YAS-02217:ERR_ANK_INVALID_GRANTROLE

**Message**: privilege %s is not a %s privilege

**Action**: Grant or revoke the correct type of privileges.

### YAS-02218:ERR_ANK_GRANT_ROLE_OPTION

**Message**: cannot GRANT to a role WITH OPTION

**Action**: Execute the grant without using grant options.

### YAS-02219:ERR_ANK_GRANT_OPTION_NOT_MATCH

**Message**: GRANT WITH %s OPTION only applies to %s privileges

**Action**: Please enter a valid grant option value.

### YAS-02220:ERR_ANK_REVOKER_NOT_GRANTOR

**Message**: cannot REVOKE privileges you did not grant

**Action**: Please do not revoke these privileges.

### YAS-02221:ERR_ANK_REVOKEROLE_NOT_EXIST

**Message**: privilege not granted to %s

**Action**: Ensure the privilege to be revoked was granted.

### YAS-02222:ERR_ANK_GRANT_DUPLICATE

**Message**: privilege/role has already been granted to user or role

**Action**: Please do not grant duplicate privileges to the same object.

### YAS-02223:ERR_ANK_GRANT_RECORD_NOT_FOUND

**Message**: grant record is not found

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02224:ERR_ANK_MODIFY_BUILDIN_ROLE

**Message**: cannot grant/revoke privileges to/from system build-in user or role

**Action**: Cannot grant or revoke privileges for system-defined users or roles.

### YAS-02225:ERR_ANK_NOT_USER

**Message**: %s is not a USER

**Action**: Please enter a valid username.

### YAS-02226:ERR_ANK_NOT_ROLE

**Message**: %s is not a ROLE

**Action**: Please enter a valid role name.

### YAS-02227:ERR_ANK_PURGE_EXIST_INDEX

**Message**: cannot purge index not dropped, name %s

**Action**: This operation is not permitted.

### YAS-02228:ERR_ANK_LOB_CONS_VIOLATE

**Message**: table with LOBs contains segments in different tablespaces

**Action**: LOB columns cannot directly delete the tablespace if it does not match the tablespace of the table; please delete the table first, then perform this operation.

### YAS-02229:ERR_ANK_LOBFRAG_CONS_VIOLATE

**Message**: table with lobfrags contains segments in different tablespaces

**Action**: LOB column partitions cannot directly delete the tablespace if it does not match the tablespace of the table; please delete the table first, then perform this operation.

### YAS-02230:ERR_ANK_FETCH_INVALID_CURSOR

**Message**: fetch out of sequence

**Action**: (1) Check if DDL was executed after opening the cursor, which could invalidate the cursor. (2) Check if operations continue after the cursor has finished fetching data. (3) Check if the cursor is valid, for example, attempting to fetch data from a cursor that returns no data.

### YAS-02231:ERR_ANK_TOO_LARGE_PART_KEY

**Message**: partitioning key maps to a partition outside maximum permitted number of partitions

**Action**: Ensure the partition key falls within 1048575 partitions or subpartitions.

### YAS-02232:ERR_ANK_VIEW_OPERATION

**Message**: the operation is not permitted on a view

**Action**: Views only allow SELECT operations.

### YAS-02233:ERR_ANK_REDO_SIZE_EXCEED

**Message**: read offset % PRIu64  exceeds the %s file size % PRIu64 

**Action**: Verify if the redo files or archive log files match the database; if not, please contact our technical support for resolution.

### YAS-02235:ERR_ANK_SLICE_NUM_EXCEED

**Message**: %s logic space is full

**Action**: The amount of data in the table has reached its limit.

### YAS-02236:ERR_ANK_INVALID_ROLE_ID

**Message**: roleId %u is already used

**Action**: Re-specify the role name.

### YAS-02237:ERR_ANK_TOO_MANY_ROLES

**Message**: number of role exceeds limit

**Action**: The number of roles exceeds the limit; please clear some roles and retry.

### YAS-02238:ERR_ANK_INVALID_SHRINK_SIZE

**Message**: number of shrink space size must be between % PRIu64 and % PRIu64

**Action**: Please enter a valid tablespace shrink value.

### YAS-02239:ERR_ANK_PASSWORD_EXPIRED

**Message**: the password has expired

**Action**: Change the password or contact the database administrator to understand the rules for selecting a new password that meets the enforced profile.

### YAS-02240:ERR_ANK_ARCHIVE_FAILED

**Message**: failed to archive redo

**Action**: The archiving thread encountered an exception, and the redo file could not be archived normally. For specific exceptions, refer to v$diag_incident; the error number for the archiving thread exception is 212; filter out events with ERROR_NUMBER=212 for the archiving thread exception. You can also check the operation logs for the exact cause. 

### YAS-02241:ERR_ANK_TAB_VERSION_MISMATCH

**Message**: table version mismatch

**Action**: Please check the metadata version of the tables across nodes, wait for the metadata to recover automatically, and retry later.

### YAS-02242:ERR_ANK_INVALID_HM_CHECK_PARAM

**Message**: %s is invalid, reason: %s

**Action**: Correct as per the error message and input valid values.

### YAS-02243:ERR_ANK_FILE_IS_CHECKING

**Message**: the file is performing health check

**Action**: Wait and retry later.

### YAS-02244:ERR_ANK_INVALID_HM_PARAM_VALUE

**Message**: the value of parameter %s is invalid

**Action**: Please enter a valid parameter value for the check item.

### YAS-02245:ERR_ANK_CREATE_SESSION

**Message**: user %s lacks CREATE SESSION privilege; logon denied

**Action**: Grant the user the "CREATE SESSION" privilege.

### YAS-02246:ERR_ANK_INVALID_OFFLINE

**Message**: offline immediate disallowed unless media recovery enabled

**Action**: Bring the tablespace offline normally or shut down abort. Reconsider your backup strategy; if logging is to be archived, this operation can be performed.

### YAS-02247:ERR_ANK_DATAFILE_UNREADABLE

**Message**: file %u cannot be read at this time

**Action**: Check the file's status and bring it online before retrying.

### YAS-02248:ERR_ANK_DROP_OFFLINE_DATAFILE

**Message**: offline datafile cannot be dropped

**Action**: Bring the file online before retrying.

### YAS-02249:ERR_ANK_OFFLINE_BUILTIN_SPACE

**Message**: cannot offline built-in tablespace

**Action**: Built-in tablespaces are not allowed to go offline.

### YAS-02250:ERR_ANK_INVALID_TEMP_SPACE_OPTION

**Message**: invalid option for alter a temporary tablespace

**Action**: Alter operations are not allowed on temporary tablespaces.

### YAS-02251:ERR_ANK_INVALID_DATAFILE_STATUS

**Message**: file %u is %s

**Action**: If the file is already in an offline/online state, it cannot go offline/online; if the file is in a recover state, wait for the recovery to complete before retrying.

### YAS-02252:ERR_ANK_OFFLINE_BUILTIN_DATAFILE

**Message**: cannot offline datafiles of built-in or temporary tablespace

**Action**: Files of built-in/temporary tablespaces are not allowed to go offline.

### YAS-02253:ERR_ANK_RESIDUAL_SPACE

**Message**: tablespace %s is residual, cannot be restored online

**Action**: Residual tablespaces need to be manually deleted.

### YAS-02254:ERR_ANK_CHECK_CONS_VIOLATE

**Message**: check constraint (%s.%s) violated

**Action**: Do not insert values that violate constraints.

### YAS-02255:ERR_ANK_CHECK_CONS_VALIDATE

**Message**: cannot validate constraint (%s.%s)- check constraint violated

**Action**: Remove values that do not comply with the check constraint from the column adding the check constraint, then retry.

### YAS-02256:ERR_ANK_RESIZE_MMS_DATAFILE

**Message**: cannot resize datafile of memory mapped tablespace

**Action**: This operation is not allowed.

### YAS-02257:ERR_ANK_HM_CHECK_PARAM_NOT_FOUND

**Message**: [%s] not found in %s

**Action**: Input a valid check item name and retry the health check.

### YAS-02258:ERR_ANK_CANNOT_CREATE_JT

**Message**: cannot create journal table %s.%s

**Action**: Delete the object with the same name.

### YAS-02259:ERR_ANK_TOO_MANY_DATABUCKETS

**Message**: maximum number of databucket count is %u

**Action**: Reduce the number of DATA BUCKETs to below the maximum value.

### YAS-02260:ERR_ANK_DATABUCKET_ALREADY_EXISTS

**Message**: databucket '%s' already exists

**Action**: Specify a different DATA BUCKET name and retry.

### YAS-02261:ERR_ANK_DATABUCKET_NOT_FOUND

**Message**: databucket '%s' is not found in tablespace %s

**Action**: Specify a DATA BUCKET belonging to that tablespace for deletion.

### YAS-02262:ERR_ANK_DATABUCKET_NOT_EMPTY

**Message**: databucket '%s' is not empty

**Action**: Clear the tables in the DATA BUCKET before retrying.

### YAS-02263:ERR_ANK_SHRINK_MMS

**Message**: cannot shrink memory mapped tablespace

**Action**: This operation is not allowed.

### YAS-02264:ERR_ANK_READ_BLOCK

**Message**: failed to read block %u-%u-%u

**Action**: Please wait and retry.

### YAS-02265:ERR_ANK_DATABUCKET_EXHAUSTED

**Message**: databucket of tablespace '%s' exhausted

**Action**: Create a new DATA BUCKET for the tablespace.

### YAS-02266:ERR_ANK_DB_NOT_OPEN_UPGRADE

**Message**: the database is not in upgrade mode

**Action**: The database is currently not in upgrade mode.

### YAS-02267:ERR_ANK_NOT_SYS_CONN_UPGRADE_MODE

**Message**: the database mode conflicts with the connection type

**Action**: If you are not a SYS user, please try connecting again after the database upgrade is completed; if you are a SYS user, please retry.

### YAS-02268:ERR_ANK_VIEW_OR_PROCEDURE_INVALID

**Message**: %s "%s.%s" has errors

**Action**: For stored procedures, the issue may be syntax errors or references to other non-existent processes. For views, the problem may be references to non-existent tables in the definition query, or references to types that do not exist or cannot be accessed. Fix errors according to the issue or create the referenced objects as necessary.

### YAS-02269:ERR_ANK_GRANT_PRIV_ON_VIEW

**Message**: only SELECT, READ, FLASHBACK privileges are allowed to grant on views

**Action**: Views are only allowed for select and flashback actions; therefore, only grant query and flashback privileges.

### YAS-02270:ERR_ANK_PTT_ANOTHER_USER

**Message**: cannot create private temporary table for another user

**Action**: Only allow creating temporary tables for the user connected to the current session.

### YAS-02272:ERR_ANK_TRIGGER_REPLACE_NOTMATCH

**Message**: trigger %s already exists on another table, cannot replace it

**Action**: Delete the trigger with the same name and recreate it.

### YAS-02273:ERR_ANK_TRIGGER_ORDER_NOTMATCH

**Message**: cannot reference a trigger of a different type

**Action**: Create the referenced trigger as the same type of trigger.

### YAS-02274:ERR_ANK_TRIGGER_ORDER_CYCLE

**Message**: cyclic trigger dependency is not allowed

**Action**: Do not create triggers that may form circular dependencies.

### YAS-02275:ERR_ANK_TRIGGER_BASE_NOTMATCH

**Message**: cannot create this trigger type on this type of %s

**Action**: Instead, create DML triggers on the table.

### YAS-02276:ERR_ANK_TRIGGER_INVALID

**Message**: trigger %s is invalid and re-validation failed

**Action**: Resolve the compilation errors in the trigger, disable or delete the trigger.

### YAS-02277:ERR_ANK_TRIGGER_EXEC_ERROR

**Message**: error during execution of trigger %s

**Action**: Check the triggers involved in the operation according to the specific information returned.

### YAS-02279:ERR_ANK_COLUMN_WITH_CONSTRAINT

**Message**: cannot modify column datatype with current constraint(s)

**Action**: Remove the constraint or refrain from performing operations that violate the constraint.

### YAS-02280:ERR_ANK_COLUMN_NOT_EMPTY

**Message**: column to be modified must be empty to %s

**Action**: Clear the values in the column and retry.

### YAS-02281:ERR_ANK_COLUMN_VALUE_TOO_BIG

**Message**: cannot decrease column length because some values are too big

**Action**: Remove values in the table that exceed the target character length and retry.

### YAS-02282:ERR_ANK_COL_ALREADY_NOT_NULL

**Message**: column to be modified to NOT NULL is already NOT NULL

**Action**: Do not add NOT NULL constraint to a non-null column.

### YAS-02283:ERR_ANK_NULL_VALUES_FOUND

**Message**: cannot enable - null values found

**Action**: Remove null values and retry.

### YAS-02284:ERR_ANK_INVALID_MODIFY_NULL

**Message**: column to be modified to NULL cannot be modified to NULL

**Action**: If a primary key or check constraint enforces NOT NULL, remove that constraint.

### YAS-02285:ERR_ANK_INCOMPATIBLE_REFERENCED_TYPE

**Message**: column type incompatible with referenced column type

**Action**: Choose a compatible data type for the referenced column. Additionally, the collation of character columns in the foreign key must match the collation of the corresponding columns in the primary key.

### YAS-02286:ERR_ANK_UNSUPPORT_ON_PTT

**Message**: unsupported feature in temporary table

**Action**: The current operation is not supported on temporary tables, please check the SQL statement.

### YAS-02287:ERR_ANK_DROP_ENABLE_AUD_POLICY

**Message**: audit policy cannot be dropped as it is currently enabled

**Action**: Disable the audit policy before dropping it.

### YAS-02288:ERR_ANK_FORBID_SET_INTERVAL

**Message**: SET INTERVAL is not legal on this table

**Action**: INTERVAL and non-INTERVAL partition type conversions can only be performed on partition tables where the partition boundary does not contain MAXVALUE.

### YAS-02289:ERR_ANK_NOT_INTERVAL_PART

**Message**: table is not partitioned by the interval

**Action**: Modification of the partition tablespace can only be done on INTERVAL-type partition tables.

### YAS-02290:ERR_ANK_COLUMN_IS_DROPPED

**Message**: column %u has been dropped by other session

**Action**: (1) Retry the operation. (2) The table structure has changed, please check and update the SQL statement.

### YAS-02291:ERR_ANK_TRIGGER_ORDER_BASE

**Message**: cannot reference a trigger defined on another table

**Action**: Do not reference a trigger on another table when creating a trigger, please modify or remove the referenced trigger and retry.

### YAS-02292:ERR_ANK_TRIGGER_BASE_SYS

**Message**: cannot create triggers on objects owned by SYS

**Action**: Creating triggers on objects owned by SYS is not allowed.

### YAS-02293:ERR_ANK_DROP_NONEXIST_SUPPLY

**Message**: cannot drop nonexistent supplemental logging

**Action**: Dropping a nonexistent supplemental log is not permitted.

### YAS-02294:ERR_ANK_SUPPLY_EXISTS

**Message**: supplemental logging has been enabled

**Action**: Remove the existing supplemental logging before adding new supplemental logging.

### YAS-02295:ERR_ANK_NOINDEX_USEDBY_SUPPLY

**Message**: no index can be used by supplemental logging

**Action**: Create a unique index on this table.

### YAS-02296:ERR_ANK_NOPRIMARY_USEDBY_SUPPLY

**Message**: no primary key can be used by supplemental logging

**Action**: Create a primary key index on this table.

### YAS-02297:ERR_ANK_DROP_CONS_USEDBY_SUPPLY

**Message**: cannot drop constraint used by supplemental logging

**Action**: Dropping constraints used by supplemental logging is not allowed.

### YAS-02298:ERR_ANK_DDL_LOGIC_REDO_SIZE

**Message**: maximun length of DDL sql is 32K when supplemental logging enabled

**Action**: When supplemental logging is enabled, the maximum length of DDL statements is 32K.

### YAS-02299:ERR_ANK_DATABUCKET_NOT_OPEN

**Message**: failed to get databucket %u 

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02300:ERR_ANK_AUDIT_LEAST_ONE_OPT

**Message**: audit policy %s must have at least one audit option

**Action**: Delete the audit policy without any audit options and recreate the audit policy.

### YAS-02301:ERR_ANK_AUDIT_USER_TYPE

**Message**: audit policy %s already applied with the %s clause

**Action**: Remove the existing audit policy using the NOAUDIT command.

### YAS-02302:ERR_ANK_REDO_SPACE

**Message**: redo free space is not enough

**Action**: Execute ALTER SYSTEM CHECKPOINT to release redo free space.

### YAS-02303:ERR_ANK_INVALID_PERM_SPACE_OPTION

**Message**: invalid tempfile clause for alter of permanent tablespace

**Action**: Retry using Data File clause.

### YAS-02304:ERR_ANK_DROP_DIST_COLUMN

**Message**: cannot drop partitioned key column in a table

**Action**: Deleting the partition key column in the table is not allowed, do not perform this operation.

### YAS-02305:ERR_ANK_ARCH_VERSION_MISMATCH

**Message**: the archive log %s version does not match the backup set version

**Action**: Archive Log Files and backup sets are not generated from the same database. Clean or remove Archive Log Files that have version mismatches, re-execute recovery, or recover using a backup set that matches the archive version.

### YAS-02306:ERR_ANK_OUTLINE_ALREADY_EXIST

**Message**: outline already exists

**Action**: An outline with the same name, SQL statement, and SQL_ID already exists. Please use the REPLACE command or delete the corresponding outline before re-executing the command.

### YAS-02307:ERR_ANK_DB_IS_ABNORMAL

**Message**: database is abnormal, cannot %s

**Action**: The DBA should investigate the cause of the failure, resolve the issue, and only proceed with the operation after the failure status is eliminated.

### YAS-02308:ERR_ANK_DIAG_ADR_DISABLED

**Message**: automatic diagnostic repository is not enabled, %s

**Action**: Users can enable the automatic diagnostic tool as needed by modifying the configuration parameter DIAG_ADR_ENABLED.

### YAS-02309:ERR_ANK_INVALID_REDO_BLOCKSIZE

**Message**: invalid redo file block size, must be an integer multiple of 512

**Action**: Input a correct redo file block size.

### YAS-02310:ERR_ANK_SPACE_LIMITS_EXCEEDED

**Message**: tablespace limits exceeded in this edition

**Action**: Standard Edition capacity limit reached. Please delete unnecessary Data Files, disable the auto-extend on Data Files, or reduce the size of Data Files before retrying.

### YAS-02311:ERR_ANK_ALTER_CONVERT_NORMAL

**Message**: failed to convert database to normal due to %s

**Action**: Please execute shutdown abort.

### YAS-02312:ERR_ANK_TRUNCATE_UNDO_DATAFILE

**Message**: cannot truncate datafile of undo tablespace

**Action**: Do not truncate the undo datafile.

### YAS-02313:ERR_ANK_SHRINK_UNDO

**Message**: cannot shrink undo tablespace

**Action**: Do not shrink the undo tablespace.

### YAS-02314:ERR_ANK_REVERSE_WRONG_CTX

**Message**: REVERSE/NOREVERSE may not be specified in this context

**Action**: Remove the illegal clause. REVERSE can be specified as an index attribute in CREATE INDEX and ALTER INDEX REBUILD statements (it cannot be an attribute of a single partition when creating a partitioned index). NOREVERSE can only be specified in the ALTER INDEX REBUILD statement.

### YAS-02315:ERR_ANK_READ_PROFILE

**Message**: failed to read user's restriction profile, user is %s

**Action**: Unable to open the user restriction file.

### YAS-02316:ERR_ANK_PASSWORD_REUSE

**Message**: failed to reuse password, please check the password restriction

**Action**: Cannot use a previously used password, please re-enter the password.

### YAS-02317:ERR_ANK_VERIFY_PASSWORD

**Message**: failed to check password complexity for %s

**Action**: Password input does not meet the criteria, please re-enter the password.

### YAS-02318:ERR_ANK_UNSUPPORT_SPACE_OFFLINE

**Message**: drop LSC table or access constraint in offline tablespace is not allowed

**Action**: Ensure the tablespace is not in an offline state when dropping LSC tables or AC.

### YAS-02319:ERR_ANK_FK_SETNULL

**Message**: cannot modify not null constraint in an ON DELETE/UPDATE SET NULL foreign key column

**Action**: Do not modify the NOT NULL attribute of the foreign key constraint column.

### YAS-02320:ERR_ANK_UNRECONIZED_NUMBER

**Message**: unrecognized number version, must drop lsc table %s and create again

**Action**: The number type in LSC Table is incompatible, please rebuild the table or use an older version of the database.

### YAS-02321:ERR_ANK_OBJECT_ON_ENCRYPT

**Message**: cannot build object in un-encrypted tablespace on encrypted table

**Action**: When creating objects on an encrypted table, please specify that the associated object tablespace is also encrypted.

### YAS-02322:ERR_ANK_AC_ON_ENCRYPT

**Message**: can not create ac in un-encrypted tablespace on encrypted table

**Action**: When creating AC on an encrypted table, please specify that the AC's tablespace is also encrypted.

### YAS-02323:ERR_ANK_ENCRYPT_BUILTIN_SPC

**Message**: cannot encrypt built-in tablespace

**Action**: The system's built-in tablespace cannot be encrypted, please modify the create database statement.

### YAS-02324:ERR_ANK_INVALID_FILE_ID

**Message**: invalid file id %u

**Action**: Please input a valid file ID and retry.

### YAS-02325:ERR_ANK_DUMP_BLOCK_EXCEED

**Message**: block id %u exceeds file size

**Action**: Please input the correct block, minblock, or maxblock values and retry.

### YAS-02326:ERR_ANK_INVALID_DUMP_BLOCK

**Message**: invalid dump %s option, reason is %s

**Action**: Please refer to the error reason and retry.

### YAS-02327:ERR_ANK_DUMP_BACKTRACE_TIMEOUT

**Message**: dump backtrace timeout

**Action**: Please check the printing stack session's machine and retry.

### YAS-02328:ERR_ANK_NOLOGGING_IN_HA

**Message**:  table nologging is not allowed when standby exists

**Action**: Please use the nologging table in a standalone environment.

### YAS-02329:ERR_ANK_CORRUPTED_TABLE

**Message**: %s is corrupted

**Action**: The table or associated tables have been damaged, please delete or empty the corrupted table.

### YAS-02330:ERR_ANK_PARALLEL_NOLOGGING

**Message**: the nologging table is not available at this time

**Action**: Do not perform concurrent operations on the nologging table.

### YAS-02331:ERR_ANK_NOLOGGING_TABLE_NOT_ALLOWED

**Message**: %s is not allowed for nologging table

**Action**: Do not perform this operation on the nologging table.

### YAS-02332:ERR_ANK_STATS_LOCKED

**Message**: object statistics are locked

**Action**: Please unlock the statistics for that object.

### YAS-02333:ERR_ANK_OBJ_MMS_SPACE

**Message**: unable to create no inmemory object in a memmory mapped tablespace

**Action**: Please change the object's inmemory option or specify the correct tablespace and retry.

### YAS-02334:ERR_ANK_OBJ_NOT_MMS_SPACE

**Message**: unable to create inmemory object in a non-memory mapped tablespace

**Action**: Please change the object's inmemory option or specify the correct tablespace and retry.

### YAS-02335:ERR_ANK_DATASPACE_LIMITS_EXCEEDED

**Message**: the total number of dataspace has reached the upper limit

**Action**: The number of dataspaces has exceeded the limit. Please delete old dataspace before creating new ones.

### YAS-02336:ERR_ANK_OBJ_VERSION_MISMATCH

**Message**: object version mismatch

**Action**: The metadata version of the objects involved in the DDL has changed, please wait for the metadata to recover automatically and retry later.

### YAS-02337:ERR_ANK_COLINDEX_ERROR

**Message**: failed to columnar index operate, reason: %s

**Action**: Please refer to the error reason and retry.

### YAS-02338:ERR_ANK_MODIFY_PARTITION_COLUMN

**Message**: datatype or length of a table partitioning column may not be changed

**Action**: This operation is not allowed.

### YAS-02339:ERR_ANK_NOT_LOCK_ROW

**Message**: no lock on the current row

**Action**: The current row is not locked.

### YAS-02340:ERR_ANK_OFFSET_LARGER_THAN_LOB_LENGTH

**Message**: offset is larger than lob length

**Action**: The offset is greater than the LOB length.

### YAS-02341:ERR_ANK_UNUSABLE_INDEX

**Message**: unusable index exists on unique/primary constraint key

**Action**: Delete or rebuild the corresponding index.

### YAS-02342:ERR_ANK_UNUSABLE_INDEX_PARTITION

**Message**: index with unusable partition exists on unique/primary constraint key

**Action**: Delete the corresponding index or rebuild the corresponding index partition.

### YAS-02343:ERR_ANK_INVALID_RESOURCE

**Message**: invalid or redundant resource

**Action**: Please confirm the profile resource is correct.

### YAS-02344:ERR_ANK_PROFILE_NOT_FOUND

**Message**: profile %s does not exist

**Action**: Please select the correct profile.

### YAS-02345:ERR_ANK_DROP_DEFAULT_PROFILE

**Message**: default profile cannot be dropped

**Action**: The default profile cannot be dropped.

### YAS-02346:ERR_ANK_INVALID_PROFILE_LIMIT

**Message**: invalid profile limit %s

**Action**: Please confirm that the profile resource parameter limits you input are valid.

### YAS-02347:ERR_ANK_TOO_MANY_PROFILE

**Message**: the number of profile exceeds limit

**Action**: The number of profiles exceeds the limit, please delete some profiles before adding new ones.

### YAS-02348:ERR_ANK_INVALID_PROFILE

**Message**: missing or invalid profile name

**Action**: Please use a valid profile name.

### YAS-02349:ERR_ANK_MODIFY_PRIMARY_KEY

**Message**: cannot modify nonexistent primary key

**Action**: Cannot modify a non-existent primary key, please ensure that the primary key exists before modifying.

### YAS-02350:ERR_ANK_MODIFY_UNIQUE_KEY

**Message**: cannot modify nonexistent unique key

**Action**: Cannot modify a non-existent unique key, please ensure that the unique key exists before modifying.

### YAS-02351:ERR_ANK_MODIFY_CONSTRAINT

**Message**: cannot modify constraint - nonexistent constraint

**Action**: Ensure that you have entered the correct constraint name.

### YAS-02352:ERR_ANK_DUPLICATE_UNIQUE

**Message**: such unique or primary key already exists in the table

**Action**: Do not create multiple unique constraints or primary key constraints on the same column.

### YAS-02353:ERR_ANK_INDEX_DESC_REVERSE

**Message**: indexes cannot use both DESC and REVERSE

**Action**: Do not use DESC in reverse indexes.

### YAS-02354:ERR_ANK_MODIFY_VIRTUAL_DATATYPE

**Message**: cannot modify function index column datatype

**Action**: Do not modify the type of the function index column.

### YAS-02355:ERR_ANK_MISS_NESTED_OPTION

**Message**: must specify table name for nested table column or attribute

**Action**: The table contains Nested Table UDT columns or attributes. Please define a storage clause for it, refer to [CREATE TABLE](../Development Guide/SQL Reference Manual/SQL Statements/CREATE TABLE) in nested_table_clauses.

### YAS-02356:ERR_ANK_INVALID_CONSTRAINT

**Message**: cannot create constraint on column of datatype %s

**Action**: Modify the column's definition type or remove the column's constraint.

### YAS-02357:ERR_ANK_MODIFY_DISTRIBUTE_COLUMN

**Message**: datatype or length of a partitioned key cannot be changed

**Action**: The datatype and length of the partition column are not allowed to be modified, do not perform this operation.

### YAS-02358:ERR_ANK_GRANT_REVOKE_OBJROLE

**Message**: privilege of object owner cannot be changed

**Action**: The privileges owned by the object cannot be modified.

### YAS-02359:ERR_ANK_NSCHAR_COLUMN_SIZE_LIMIT

**Message**: column %s exceeds maxsize %u %s

**Action**: Please refer to the error reason and retry.

### YAS-02360:ERR_ANK_USER_VERSION_MISMATCH

**Message**: user version mismatch

**Action**: The user metadata version has changed, please wait for the background to push automatic recovery, and retry later.

### YAS-02362:ERR_ANK_TSS_MAXSIZE_MORE_THAN_LIMIT

**Message**: new maxsize of tablespace set % PRIu64 exceeds the limit % PRIu64

**Action**: Reduce the size of the tablespace set below the maximum size.

### YAS-02363:ERR_ANK_COLINDEX_NOTSUPPORT_TYPE

**Message**: cannot create columnar index on expression with datatype %s

**Action**: The column's datatype does not support the creation of columnar indexes, modify the column's datatype, or do not create a columnar index on this column.

### YAS-02364:ERR_ANK_TABLE_IN_SETTING_LOGGING

**Message**: table is in setting logging async

**Action**: The table is being converted to logging mode, please try later.

### YAS-02365:ERR_ANK_DEBUG_RUNNING

**Message**: debug is running and cannot support other cmd type

**Action**: Other commands are not supported while in debug mode.

### YAS-02366:ERR_ANK_DEBUG_CANCEL

**Message**: debug current session has been killed or canceled

**Action**: The debugger connection has been disconnected, please try to reconnect.

### YAS-02367:ERR_ANK_NOLOGGING_INDEX_ON_LOGGING_TABLE

**Message**: create nologging index is not allowed for logging table

**Action**: Do not create a nologging index on the logging table.

### YAS-02368:ERR_ANK_STATS_UNSUPPORTED_COL_TYPE

**Message**: unsupported column type

**Action**: Histogram statistics cannot be set for this column type.

### YAS-02369:ERR_ANK_TSS_RESIZE_MORE_THAN_MAXSIZE

**Message**: new size %lu is larger than tablespace set maxsize %lu

**Action**: The size of the tablespace set must be less than its maximum size.

### YAS-02370:ERR_ANK_PARAM_MISMATCH_STR

**Message**: parameter %s does not match initialization parameter, expect %s

**Action**: The current value of the parameter does not match the value specified during database creation, please modify and retry.

### YAS-02371:ERR_ANK_INTERNAL_ERROR

**Message**: kernel internal error

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02372:ERR_ANK_SEQ_CURRVAL_INVALID

**Message**: sequence %s.CURRVAL is not yet defined in this session

**Action**: Please obtain the nextVal of the corresponding sequence before getting the currVal of that sequence.

### YAS-02373:ERR_ANK_COMPRESS_SPC_NOT_SUPPORT

**Message**: compressed tablespace cannot be created because datafile %s does not support compression

**Action**: Choose an appropriate file system or create a non-compressed tablespace.

### YAS-02374:ERR_ANK_NOT_COMPOSITE_PART

**Message**: table is not partitioned by composite partition method

**Action**: Please operate on a Composite Partition table.

### YAS-02375:ERR_ANK_ACROSS_MULTIPLE_PART

**Message**: subpartition names specified are across multiple partitions

**Action**: Please operate on subpartitions under the same partition.

### YAS-02376:ERR_ANK_DROP_ALL_SUBPART

**Message**: cannot drop all subpartitions of a partition

**Action**: Dropping all subpartitions under a partition is prohibited.

### YAS-02377:ERR_ANK_SUBPART_NOT_EXISTS

**Message**: specified subpartition %s does not exist

**Action**: Please input the correct subpartition name.

### YAS-02379:ERR_ANK_BOTH_STORE_IN_AND_PARTS

**Message**: only one of STORE IN or <partition-description> clause may be specified

**Action**: Specifying both store in and partition description is prohibited.

### YAS-02380:ERR_ANK_DATAFILE_SIZE_EXCEED

**Message**: datafile size exceeds maximum file size % PRIu64 

**Action**: Reduce the Data File size and retry.

### YAS-02381:ERR_ANK_DIAGNOSTIC_DEST_USED

**Message**: diagnostic destination directory has been used by another process

**Action**: Modify the parameter DIAGNOSTIC_DEST value different from the instance that has been started and restart the instance.

### YAS-02382:ERR_ANK_CIRCULAR_GRANT

**Message**: circular role grant detected

**Action**: Note that role grants cannot form a circle, for example, A has B role and B also has A role.

### YAS-02383:ERR_ANK_TOO_MANY_TABLESPACE_SET

**Message**: maximum number of tablespace sets is %u

**Action**: Do not create more than the maximum number of tablespace sets.

### YAS-02384:ERR_ANK_ADD_INVALID_PARTS

**Message**: only one partition can be added for hash partition table or missing partition bound for range/list partition table

**Action**: Ensure that only one partition is added at a time for the Hash Partition table or specify partition bounds for other types of partition.

### YAS-02385:ERR_ANK_ARCHIVE_NOT_DELETED

**Message**: archived log %s not deleted, needed for %s

**Action**: This archive may be applied in certain scenarios, so it cannot be deleted.

Common reasons for archive deletion failures are as follows:

- crash recovery: This archive may be used for crash recovery. If you want to delete it, please perform a checkpoint and try again (execute SQL statement ALTER SYSTEM CHECKPOINT).
- backup: This archive may be used for backup set recovery. If you want to delete it, please perform an incremental or full backup, or modify the configuration parameter ARCH_CLEAN_IGNORE_MODE. If the parameter configuration does not ignore backups, a particular archive's NEXT_CHANGE# (obtained by querying V$ARCHIVED_LOG) must be less than the latest backup set's CHECKPOINT_SCN (obtained from the DBA_BACKUP_SET view) before it can be deleted; otherwise, it cannot be deleted.
- standby: This archive may be used for standby database. Please check for synchronization issues with the standby database (view primary/standby synchronization information from V$ARCHIVE_DEST_STATUS. Significant gaps may lead to the need for DBA intervention to fix the gap). Wait for the archive to synchronize with the standby database before deleting, or modify the configuration parameter ARCH_CLEAN_IGNORE_MODE. If the parameter configuration does not ignore the standby database, regardless of whether primary or standby, only the HA cluster's nodes have synchronized the archive can this archive be deleted; otherwise, it cannot be deleted.
- flashback database: Under the full database flashback functionality, the guarantee point depends on this archive. If you want to delete this archive, please remove the guarantee point first.
- create more backups: The archive cleanup policy ARCHIVELOG_DELETION_POLICY has been configured. The archive file can only be cleaned after the target number of backups has been reached. Please check the configured policy and the archive files to be cleaned against the backup requirements.

When encountering errors related to backup, standby, or creating more backups after receiving the error message, if you still want to delete the archive without meeting the backup or standby database synchronization requirements, you can use the optional field FORCE. This field may cause the standby database to become unavailable or the backup set to be unable to restore the database to its latest state, requiring caution in usage.

### YAS-02386:ERR_ANK_FAILED_DELETE_ARCHIVE

**Message**: expected to delete the archived logs until sequence %u, but failed when deleting sequence %u

**Action**: Only some archives were deleted, the others were not, please refer to the underlying error code for the specific reason for failure.

### YAS-02387:ERR_ANK_INVALID_PART_DEF

**Message**: invalid partition description

**Action**: Please remove the illegal partition descriptor clause, or ensure the operation's table is a Composite Partition table.

### YAS-02388:ERR_ANK_SYNC_SPACE_FAILED

**Message**: failed to synchronized tablespace

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02389:ERR_ANK_MVIEW_ON_STATEMENT

**Message**: on-statement materialized join view error: %s

**Action**: Specifying on-statement in creating a materialized view conflicts with other definitions, please modify the creation SQL statement accordingly.

### YAS-02390:ERR_ANK_MVIEW_ON_COMMIT_OPTION

**Message**: ON COMMIT attribute is incompatible with other options

**Action**: Specifying on commit when creating a materialized view conflicts with its definitions, please modify the SQL statement according to the creation rules of materialized views.

### YAS-02391:ERR_ANK_MVIEW_INTERVAL_TIME

**Message**: interval must evaluate to a time in the future

**Action**: The time for the next refresh of the materialized view must be a future time, please re-modify the SQL statement.

### YAS-02392:ERR_ANK_MVIEW_OR_AC_COMMENT

**Message**: cannot comment on the materialized view/access constraint

**Action**: Do not add comments to the materialized view or AC.

### YAS-02393:ERR_ANK_MVIEW_OPER_NOT_ALLOWED

**Message**: %s operation is not allowed on materialized views

**Action**: Specified operation is not allowed on materialized views.

### YAS-02394:ERR_ANK_MVIEW_IN_SPACE

**Message**: materialized views and/or their indices exist in the tablespace

**Action**: Delete the materialized views in the tablespace before deleting the tablespace.

### YAS-02395:ERR_ANK_INVALID_MODIFY_COLUMN

**Message**: invalid modification of columns, reason: %s

**Action**: This type of conversion requiring changes to the underlying storage is not allowed, please check if the corresponding column is a store as LOB column.

### YAS-02396:ERR_ANK_STORE_AS_LOB_LIMIT

**Message**: cannot add foreign key (reference) on columns stored as lob

**Action**: Foreign keys cannot be added to or referenced from columns defined as `STORE AS LOB`.

### YAS-02397:ERR_ANK_INVALID_YMINTERVAL

**Message**: cannot specify this interval with existing high bounds

**Action**: Cannot specify this interval with existing upper bounds, please reset the upper bound.

### YAS-02398:ERR_ANK_BASETABLE_NOSUBPART

**Message**: partitioning method for LOCAL index is inconsistent with that of the underlying table

**Action**: Please check the partition type of the table and recreate the Local Index.

### YAS-02399:ERR_ANK_MVIEW_RENAME_NOT_ALLOWED

**Message**: cannot rename a materialized view

**Action**: Renaming of materialized views is not allowed.

### YAS-02400:ERR_ANK_ONLY_OPEN_UPGRADE

**Message**: cannot mount or open database during the upgrade process

**Action**: The database upgrade is not complete. If the upgrade process fails, please perform the rollback operation.

### YAS-02401:ERR_HA_DEMOTE_ON_STANDBY

**Message**: converting to standby cannot be executed on standby database

**Action**: Perform this operation on the Primary Database.

### YAS-02402:ERR_HA_SWITCHOVER_ON_PRIMARY

**Message**: switchover cannot be executed on primary database

**Action**: Perform this operation on the Standby Database.

### YAS-02403:ERR_HA_FAILOVER_ON_PRIMARY

**Message**: failover cannot be executed on primary database

**Action**: Perform this operation on the Standby Database.

### YAS-02404:ERR_HA_RECV_TIMEOUT

**Message**: replication receive timeout

**Action**: Check the network quality of the deployment environment.

### YAS-02405:ERR_HA_PEER_NOT_OPEN

**Message**: %s database is not open

**Action**: Execute this operation when the peer database is open; you can execute ALTER DATABASE OPEN on the peer database.

### YAS-02406:ERR_HA_PEER_NOT_CONNECTED

**Message**: database is not connected to %s

**Action**: Check if the peer database is started, if the configuration parameter ARCHIVE_DEST_n is correct, and whether the network status is normal.

### YAS-02407:ERR_HA_PARAM_ARCHIVE_DEST

**Message**: invalid format of parameter %s for ARCHIVE_DEST_%u

**Action**: Check if the configuration format is correct, provide a valid parameter value, and refer to [Configuration Parameters](Configuration Parameters) for formatting requirements.

### YAS-02408:ERR_HA_PEER_IS_CONNECTED

**Message**: database is already connected to %s

**Action**: Failover operations cannot be performed while the Primary Database is connected normally; if necessary, you can perform a switchover operation.

### YAS-02409:ERR_HA_UNEXPECTED_MSG_TYPE

**Message**: expected message is (type %u, size %u), but received message is (type %u, size %u)

**Action**: Check the network communication devices.

### YAS-02410:ERR_HA_INVALID_MSG_CTX

**Message**: received message content is invalid

**Action**: Check the network communication devices.

### YAS-02411:ERR_HA_ARCHIVE_LOG_BRAIN_SPLIT

**Message**: there is already a brain split between the archived logs

**Action**: The generation node of this archive has already encountered log inconsistencies (a brain split has occurred), and under these circumstances, this archive cannot be applied to the current node. Please select the Archive Log Files from the current node or the normal Archive Log Files from the Standby Database of the current node for registration.

### YAS-02412:ERR_HA_SWITCHOVER_ON_MASTER

**Message**: switchover cannot be executed on non-master instance

**Action**: Please perform the switchover on the primary instance of the standby cluster.

### YAS-02413:ERR_HA_STANDBY_NAME_NOT_EXIST

**Message**: the specified standby database %s does not exist

**Action**: Specify a valid Standby Database name or IP address.

### YAS-02414:ERR_HA_FAILED_PROCESS_REQ

**Message**: failed to process request

**Action**: Failed to execute local requests on the peer database, consult the underlying error code (the peer database's error will be shown in the local database's underlying error code) or check the running log on the peer database to analyze the failure reason.

### YAS-02415:ERR_HA_SWITCHOVER_RUNNING

**Message**: primary is already in another switchover progress

**Action**: The switchover operation has already been initiated, if necessary, please wait for the completion before retrying.

### YAS-02416:ERR_HA_SWITCHOVER_FAILED

**Message**: primary instance %u switchover failed

**Action**: Check the underlying error or refer to the alert log and running log.

### YAS-02417:ERR_HA_NEED_REPAIR

**Message**: database needs to be repaired

**Action**: The Standby Database is in an abnormal state; it can be repaired using the build database operation or from the backup set. Refer to [Repairing Abnormal Standby Database](../High Availability/Standby Repair).

### YAS-02418:ERR_HA_FAILOVER_ON_MASTER

**Message**: failover cannot be executed on non-master instance

**Action**: Please perform the failover on the primary instance of the standby cluster.

### YAS-02419:ERR_HA_NOT_EANBLED

**Message**: replication mode is not enabled

**Action**: To enable replication mode, at least one of the following conditions must be met:

- The configuration parameter REPLICATION_ADDR is not empty.
- At least one primary/standby link is configured in the ARCHIVE_DEST_n parameter.

### YAS-02420:ERR_HA_CONVERT_STATUS

**Message**: once a crash recovery is executed, the primary cannot be converted to standby

**Action**: Once recover database has been executed, it cannot be manually downgraded. You can choose to open as a Primary Database role or re-execute the restore operation, then perform the downgrade followed by the recover database operation.

### YAS-02421:ERR_HA_REDO_NOT_SNYC

**Message**: primary redo is not synchronized to the specified standbys

**Action**: The Standby Database is in an abnormal state and cannot synchronize the Primary Database's redo logs. This error commonly occurs in the following scenarios:

- Change protection mode: First switch to maximize availability mode, wait for the logs to synchronize, and then try to switch to maximize protection mode.
- Open database: If the database is under maximize protection mode and has unsynchronized redo logs, the database open operation may fail. In this situation, first repair synchronization with the standby database to ensure redo is synchronized before executing the open data operation. If the standby database cannot be repaired, switch the primary database to maximize availability mode then perform the open operation.

### YAS-02422:ERR_HA_STBY_UPGRADE_FAILED

**Message**: standby upgrade failed

**Action**: The Standby Database upgrade failed, check the underlying error code to clarify the specific reason.

### YAS-02423:ERR_HA_LSC_FILE_SYNC

**Message**: primary lsc static files is not synchronized to the specified standbys

**Action**: For detailed information regarding errors, please refer to the alert log and running log.

### YAS-02424:ERR_HA_RECEIVER_NOT_READY

**Message**: standby redo receiver is not ready

**Action**: The standby database's receiving thread is not yet ready, no action is needed, just wait for the Primary Database to reconnect.

### YAS-02425:ERR_HA_NOT_MODIFY_ARCHIVE_DEST

**Message**: parameter ARCHIVE_DEST_%u cannot be modified because %s

**Action**: Modify according to the error prompt or wait for reattempt.

### YAS-02426:ERR_HA_NOT_ESTABLISH_REPLICATE

**Message**: replication is not established

**Action**: Primary/Standby has not established a connection; check the ARCHIVE_DEST_n configuration parameter for Primary/Standby configuration.

### YAS-02427:ERR_HA_NO_SYNC_STANDBYS

**Message**: cannot %s, requires at least one synchronous standby

**Action**: Increase the number of [Synchronous Standbys](../High Availability/Defining Synchronous Standbys) or select the correct protection mode.

### YAS-02428:ERR_HA_FAILED_BUILD_DATABASE

**Message**: failed to build database to %s

**Action**: Check the underlying error code to clarify the specific reason.

### YAS-02429:ERR_HA_WAIT_STANDBY_OPEN

**Message**: failed to wait for standby %s to open

**Action**: Check the underlying error code to clarify the specific reason.

### YAS-02430:ERR_HA_ELECTION_IS_NOT_RUNNING

**Message**: election is not running

**Action**: Leader Election has not been started yet, please wait for it to start.

### YAS-02431:ERR_HA_ELECTION_ERROR

**Message**: election error

**Action**: Internal error in Leader Election, no action is needed.

### YAS-02432:ERR_HA_ELECTION_DISABLED

**Message**: election disabled

**Action**: Leader Election has been disabled. If needed, execute "alter system ha_election_enabled=true" to enable Leader Election.

### YAS-02433:ERR_HA_ROUTER_CHANGE

**Message**: information of router has changed

**Action**: Wait for the node routing information to update before executing again.

### YAS-02434:ERR_HA_ELECTION_OPER

**Message**: %s cannot be executed on election

**Action**: Failover operations cannot be manually executed while in Leader Election mode; if necessary, please perform a switchover operation.

### YAS-02435:ERR_HA_ELECTION_NEW_PRIMARY

**Message**: find a new primary database when %s

**Action**: Check if Leader Election has occurred or if a manual failover has selected a new Primary Database.

### YAS-02436:ERR_HA_ELECTION_START_FAILED

**Message**: failed to start election, reason: %s

**Action**: Failed to start Leader Election, do not perform this operation. Ensure the database is OPEN.

### YAS-02437:ERR_HA_ELECTION_ALREADY_STARTED

**Message**: election already started

**Action**: Self-electing primary has started, no need to start again.

### YAS-02438:ERR_HA_ELECTION_NOT_SUPPORT

**Message**: election does not support specifying standby name

**Action**: Self-electing primary does not support specifying Standby Database name in QUORUM_SYNC_STANDBYS and REQUIRED_SYNC_STANDBYS parameters.

### YAS-02439:ERR_HA_ELECTION_SRC_MISMATCH

**Message**: election configuration does not match the source database

**Action**: The current instance's Leader Election configuration parameters do not match the Primary Database, such as:

- The value of parameter HA_ELECTION_ENABLE is TRUE in primary database, while the values of parameter DISABLE_ELECTION is TRUE in standby database, as HA_ELECTION_ENABLE is TRUE too.
- The value of parameter HA_ELECTION_ENABLE is TRUE in primary database, while the values of parameter DISABLE_ELECTION is FALSE in standby database, but HA_ELECTION_ENABLE is FALSE.
- The value of parameter HA_ELECTION_ENABLE is FALSE in primary database, but TRUE in standby database.

All the scenarios will lead primary-standby disconnections and other abnormal exceptions. Please adjust the configuration and then perform subsequent operations.

### YAS-02450:ERR_HA_WAIT_STBY_REPLAY

**Message**: failed to wait for standby %u to replay

**Action**: Check whether the Standby Database status is normal. Query the status of the Standby Database from the V$ARCHIVE_DEST_STATUS view on the Primary Database or V$REPLICATION_STATUS view on the Standby Database.

### YAS-02451:ERR_HA_FAILED_REPAIR_STANDBY

**Message**: the standby database is normal or not repairable

**Action**: The Standby Database is either in normal status and does not need repair, or cannot be repaired through the BUILD DATABASE REPAIR STANDBY operation. Check the STATUS and ERROR in the V$REPLICATION_STATUS view, or inspect the underlying error code for specific reasons. If the Standby Database status is NEED REPAIR, it can only be rebuilt using the BUILD DATABASE statement.

### YAS-02452:ERR_HA_STANDBY_NOT_NORMAL

**Message**: the standby database is not normal

**Action**: The standby database performing this operation must be a normal Standby Database. Check the status of the Standby Database by querying the V$ARCHIVE_DEST_STATUS view on the Primary Database or V$REPLICATION_STATUS view on the Standby Database.

### YAS-02453:ERR_HA_FILES_NUMBER_DIFFERENT

**Message**: %s of the database are different from the standby

**Action**: This database's files differ from the target database, meaning the Standby Database cannot be repaired and can only be rebuilt using the BUILD DATABASE statement.

### YAS-02454:ERR_HA_STANDBY_START_TIME

**Message**: the start time of standby process does not match, expected %s, actual %s

**Action**: The IP address of the Standby Database is occupied or restarted unexpectedly. Inspect the Standby Database for normal operation and re-execute the operation after troubleshooting.

### YAS-02455:ERR_HA_INCR_BUILD

**Message**: failed to incremental build standby, reason: %s

**Action**: This error commonly occurs when using the Standby Database to fix the gap of another Standby Database. The chosen Standby Database is not synchronized; this Standby Database has less log data and cannot be used to repair another Standby Database. Check the SYNCHRONIZED field in the V$ARCHIVE_DEST_STATUS view to find a synchronized Standby Database. If it displays YES, the Standby Database can be used to repair the gap of another Standby Database. If there are no synchronized Standby Databases, only the Primary Database can be used to repair the Standby Database's gap.

### YAS-02456:ERR_HA_TARGET_DATABASE

**Message**: the target database is not %s

**Action**: The target of this operation is incorrect, please refer to the operation description.

### YAS-02457:ERR_HA_STBY_RESTART_TIMEOUT

**Message**: waiting for standby restart timeout

**Action**: The BUILD DATABASE INCREMENTAL STANDBY or BUILD DATABASE REPAIR STANDBY operation will restart the Standby Database. For reasons behind failures, please check the running log.

### YAS-02458:ERR_HA_METADATA_DIFF_FROM_STBY

**Message**: the %s of primary is different from standby

**Action**: The metadata information of the two databases is inconsistent, possibly due to different sources or differing binary versions of the databases. Update binary versions or check for mixed connections of different clusters in the cluster configuration.

### YAS-02459:ERR_HA_REPAIR_STANDBY

**Message**: failed to repair standby, reason: %s

**Action**: Common scenarios for this error include:

- Unable to find the primary/standby log divergence point, can't perform brain split repairs, commonly the divergence point will be found in archive log files where the primary or standby database cleared the archives (check if the archive cleanup configuration is unreasonable which caused the automatic cleanup), or the archive files are corrupted.
- Situations where restore times are inconsistent can only be resolved using the full BUILD operation to rebuild the standby database.

This standby database can only be rebuilt using the BUILD DATABASE statement.

### YAS-02460:ERR_HA_TOO_MANY_SPLIT_BRAIN_DATA

**Message**: there are too many %s different from the source database

**Action**: Error scenarios include:

- Excessive data pages need repair, which is inefficient. It is not advisable to use this operation to repair the standby database.
- Too many slice files need repair, which is inefficient. It is not advisable to use this operation to repair the standby database.

This standby database can only be rebuilt using the BUILD DATABASE statement.

### YAS-02461:ERR_HA_FAIL_ANALYZE_DIFF_BLOCK

**Message**: failed to analyze difference block

**Action**: Check the underlying error code for more specific details.

### YAS-02462:ERR_HA_HEARTBEAT_LINK_BUSY

**Message**: heartbeat link is busy

**Action**: Check the underlying error code for more specific details.

### YAS-02463:ERR_HA_STANDBY_SWITCHING_REDO

**Message**: standby is switching redo file and will try to reconnect later

**Action**: Connection failed; the Standby Database is switching log files, please wait.

### YAS-02464:ERR_HA_DROP_MULT_LOGFILE

**Message**: cannot drop more than one logfile at a time

**Action**: Please remove log files one by one.

### YAS-02465:ERR_HA_CHANGE_PROTECTION_MODE

**Message**: cannot change protection mode, reason: %s

**Action**: This operation is not allowed.

### YAS-02466:ERR_HA_CONFIRM_ROLE_FAILED

**Message**: failed to confirm the actual database role

**Action**: Please check if yasom and the corresponding yasagent to the Primary Database are online, and whether the yasom Election has been enabled.

### YAS-02467:ERR_HA_STANDBY_MASTER_INSTANCE

**Message**: the instance %u isn't master, master instance id %u

**Action**: Choose an appropriate instance to execute this operation.

### YAS-02468:ERR_HA_INSTANCE_FAILED_MOUNT

**Message**: failed to mount standby instance

**Action**: Check the underlying error code.

### YAS-02469:ERR_HA_PARAM_VALID_FOR

**Message**: in cluster mode, the value of VALID_FOR in ARCHIVE_DEST_%u cannot be STANDBY_ROLE or ALL_ROLES

**Action**: In cluster mode, VALID_FOR must be specified as PRIMARY_ROLE.

### YAS-02470:ERR_HA_NOT_EMPTY_ARCHIVE_DEST

**Message**: no empty archive dest

**Action**: No empty archive destination can be found; the number of HA links on this node has reached the limit.

### YAS-02471:ERR_HA_NOT_EXECUTED_ON_PRIMARY

**Message**: %s cannot be executed on %s database

**Action**: This operation cannot be performed on the Primary Database or logical Standby Database; it needs to be executed on the Standby Database.

### YAS-02472:ERR_HA_REPLAY_HAS_BEEN_CANCEL

**Message**: redo apply has been canceled

**Action**: The current Apply operation is terminated because the other session paused it.

### YAS-02473:ERR_HA_RECOVERY_NOT_ACTIVE

**Message**: %s is not active

**Action**: There is currently no Apply operation active. Physical Standby Database can query v$recovery_status view to obtain the current Apply running state, and logical Standby Database can refer to v$LOGSTDBY_PROGRESS view to see the SQL Apply running state.

### YAS-02474:ERR_HA_RECOVERY_REPLAY_AGAIN

**Message**: %s is active

**Action**: There is already an Apply operation active, no need to start Apply operation again. The Physical Standby Database can query v$recovery_status view for the current Apply running state. If the state shows PAUSED, please re-execute the operation. The Logical Standby Database can check the v$LOGSTDBY_PROGRESS view for SQL Apply running state.

### YAS-02475:ERR_HA_RECOVERY_THREAD_CRASHED

**Message**: the standby replay thread has crashed

**Action**: The current Apply thread has failed; Apply cannot proceed. Check the running log or alert log for the issue's cause. Fix the problem and restart the database, then re-execute the operation.

### YAS-02476:ERR_HA_DISABLE_REGISTER_ARCHIVE

**Message**: the database has not enabled archive log registration

**Action**: Archive registration is only used under the following scenarios:

- Sandbox Standby Database, i.e., the standby database with the SANDBOX_STANDBY configuration parameter set to TRUE.
- Used in conjunction with the RESTORE operation, register archives after the RESTORE operation so that these archives can be applied when executing RECOVER, allowing the database to return to the desired state.

### YAS-02477:ERR_HA_SANDBOX_STANDBY_DISCONNECT

**Message**: the sandbox standby cannot be connected

**Action**: Connections to the Standby Database are not allowed when SANDBOX_STANDBY is enabled.

### YAS-02478:ERR_HA_PITR_REPLAY_INCOMPLETE

**Message**: recovery requesting unknown archived log for thread %u with sequence %u and starting SCN of % PRIu64

**Action**: The specified point in time or SCN is too large, and even if Apply completes, it will not reach the specified point. If there are available Archive Log Files copies, you can register the archives and re-execute the restore operation. If there are no spare archives, the current point in time is the only option; continue with the resetlogs operation.

### YAS-02479:ERR_HA_NOT_CANCEL_RECOVERY

**Message**: the recovery of the %s database cannot be cancelled during %s

**Action**: Pausing physical Apply or SQL Apply during switchover or failover is not allowed; please wait for the leader election process to complete.

### YAS-02480:ERR_HA_INVALID_REPLICATION_ADDR

**Message**: the ip address of REPLICATION_ADDR cannot be set to 0.0.0.0

**Action**: The configuration parameter REPLICATION_ADDR cannot be configured as 0.0.0.0 format; please reconfigure a valid IP address.

### YAS-02481:ERR_HA_PITR_NOT_EXPECTED_TIME

**Message**: the database cannot recover to the target %s, and the current %s of the database is %s, but database can be open resetlogs

**Action**: The specified point in time or SCN is too large, and even if Apply completes, it will not reach the specified point. If there are available Archive Log Files copies, please register them and then execute the restore operation again. If there are no spare archives, the current time point is the maximum apply point, continue with the resetlogs operation. It is advised that after receiving this error message, you can continue and execute OPEN resetlogs to open the database.

### YAS-02482:ERR_HA_OPERATE_WHEN_SWITCHOVER

**Message**: cannot execute %s when switchover

**Action**: This operation cannot be performed during the switchover process, wait for the switchover to complete before executing it (query V$DATABASE to obtain the switchover progress).

### YAS-02483:ERR_HA_SCF_ERROR

**Message**: sync coast file module error: %s

**Action**: The local cold data synchronization of the LSC table failed; please handle according to the error information.

### YAS-02484:ERR_HA_OM_ELECTION_NOT_SUPPORT

**Message**: the operation is not allowed in yasom election mode

**Action**: In yasom election mode, SQL statements that involve Primary/Standby roles and protection mode switching will be intercepted, only allowing yasom to automatically manage the database's Primary/Standby roles and protection mode. If manual management is needed, please disable the election.

### YAS-02485:ERR_HA_POSITION_SMALL

**Message**: the last position %s is smaller than applied position %s

**Action**: The specified position cannot be smaller than the already applied position.

### YAS-02486:ERR_HA_STBY_SYNC_TIME

**Message**: the standby replay synchronization timeout

**Action**: Timeout waiting for Standby Database to synchronize with Primary Database; check if the network is functioning normally and whether the Standby Database Apply is lagging significantly. If the Standby Database Apply is slow, check its Apply parallelism and whether its hardware performance is significantly inferior to that of the Primary Database.

### YAS-02487:ERR_HA_SET_PROTECTION_ABNORMAL

**Message**: failed to synchronize redo to the specified standbys

**Action**: When setting Maximize Protection mode, due to disconnections or other exceptions on some synchronized Standby Databases, the Primary Database's redo cannot synchronize to these Standby Databases, resulting in failure to set Maximize Protection mode. Check the network status between Primary and Standby to ensure that the Standby Database is not in an abnormal state.

### YAS-02488:ERR_HA_DB_GUARD_ENABLE

**Message**: database guard is enabled

**Action**: The logical Standby Database only supports business operations executed by the SYS user; other users cannot perform write operations.

### YAS-02489:ERR_HA_NOT_FOUND_LOGIC_LOG

**Message**: logical log not found

**Action**: Reported during switching to the logical Standby Database. Common reasons include:

- The primary database has not executed the BUILD operation.
- The standby database has not waited for the BUILD operation to sync before closing the database.
- The standby database has not paused physical apply before executing the BUILD operation on the primary database.

### YAS-02490:ERR_HA_SCF_FILE_CORRUPT

**Message**: slice index file corrupted, %s

**Action**: The file format of the slice index is invalid or corrupted, possibly because files in the _sliceindex directory were manually deleted or modified or were damaged during disk read/write operations. The database will automatically restore and rebuild all index files as needed.

### YAS-02491:ERR_HA_STBY_UNREADABLE

**Message**: standby is unreadable

**Action**: The physical Standby Database is an unreadable disaster recovery replica (STANDBY_RECOVER_ONLY=TRUE) and can only be used to Apply redo logs, not for queries. To start querying the Standby Database, first stop business operations on the Primary Database. After all redo logs have been synchronized to all Standby Databases, adjust the STANDBY_RECOVER_ONLY parameter on the Primary and Standby Databases to FALSE.

### YAS-02492:ERR_HA_PARAM_ARCHIVE_DEL_POLICY

**Message**: invalid format of parameter %s for ARCHIVELOG_DELETION_POLICY

**Action**: Please check the configuration format of the archive cleanup policy and its parameter requirements by referring to [Configuration Parameters](Configuration Parameters).

<span id="errno2501" name="errno2501"></span>

### YAS-02501:ERR_BAK_RUNNING

**Message**: the %s is already in progress

**Action**: Please wait for the current backup, restore, or tablespace migration process to finish, or use kill session to manually end the backup thread.

### YAS-02502:ERR_BAK_OPER_FILE_WHEN_BACKUP

**Message**: cannot perform file operations when database is %s

**Action**: Wait until the backup or tablespace migration has completed before executing file operations.

### YAS-02503:ERR_BAK_IS_FORBIDDEN

**Message**: cannot backup database when %s

**Action**: Make sure that transactions have ended and that no file operations are being executed on the database before backing up.

### YAS-02504:ERR_BAK_DB_NOT_RESTORE

**Message**: database restore is not yet complete, cannot %s

**Action**: Clean up leftover files and re-execute RESTORE DATABASE or BUILD DATABASE action.

### YAS-02505:ERR_BAK_TAG_ALREADY_EXISTS

**Message**: backupset with tag %s already exists

**Action**: Set a unique backup set TAG. For cleaning up conflicting TAGs, refer to [yasrman user guide > Deleting backup set](../Tools Guide/yasrman/User Guide for yasrman/00User Guide for yasrman), **please operate with caution as needed**.

### YAS-02506:ERR_BAK_TOO_MANY_FILES

**Message**: maximum number of backup files is %u

**Action**: Perform a full checkpoint and clean up archive files to reduce the number of backup set archive files.

### YAS-02507:ERR_BAK_BASE_NOT_EXISTS

**Message**: base incremental backup set does not exist

**Action**: First perform a LEVEL 0 incremental backup to create a baseline for incremental backups.

### YAS-02508:ERR_BAK_TOO_MANY_INCREMENT

**Message**: maximum number of incremental backup sets is %u

**Action**: Perform a LEVEL 0 incremental backup to update the incremental backup baseline.

### YAS-02509:ERR_BAK_TIME_TOO_LATE

**Message**: database must be restored from backup created before %s

**Action**: Check the UNTIL TIME clause or select a sufficiently old backup set.

### YAS-02510:ERR_BAK_SCN_TOO_LARGE

**Message**: database must be restored from backup older than SCN % PRIu64 

**Action**: Check the UNTIL SCN clause or select a sufficiently old backup set.

### YAS-02511:ERR_BAK_STRING_TOO_LONG

**Message**: %s value exceeds maximum length of %u characters

**Action**: Check the backup set path length.

### YAS-02512:ERR_BAK_STBY_BACKUP_CANCELLED

**Message**: standby backup has been cancelled

**Action**: For detailed error information, please see the running log of the Primary Database.

### YAS-02513:ERR_BAK_STBY_NEED_RECOVER

**Message**: standby must recover to LFN % PRIu64 before incremental backup

**Action**: Wait for the Standby Database to Apply to the specified LFN before performing the incremental backup.

### YAS-02514:ERR_BAK_VERSION_MISMATCH

**Message**: the backup set version does not match the database version. The recoverable version of the database is %u to %u, and the current backup set version is %u.

**Action**: Please check the backup set version and the database version to be restored, along with the binary version. Select the database files that match the backup set for restoration.

### YAS-02515:ERR_BAK_RESETLOGS_GAP

**Message**: resetlogs gap is found in the backup archive files

**Action**: Perform a full checkpoint, then execute the backup again.

### YAS-02516:ERR_BAK_FORCE_BACKUP

**Message**: FORCE option only supports full type backup

**Action**: Forced backups should only be executed in full backup scenarios; please check the backup type.

### YAS-02517:ERR_BAK_LOCK_REDO_TIMEOUT

**Message**: lock cluster redo timeout

**Action**: Distributed backup requires locking redo on all nodes, timeout may be due to slow redo disk or high network latency. Please retry outside of peak business hours.

### YAS-02518:ERR_BAK_SEND_DATA_FAILED

**Message**: failed to send data during remote backup

**Action**: Remote backup failed to send data, please check the backup process status and the network status.

### YAS-02519:ERR_BAK_TAG_NOT_EXISTS

**Message**: backupset with tag %s does not exist

**Action**: The specified backup set TAG does not exist, please verify that the TAG name is correct.

### YAS-02520:ERR_BAK_RMAN_VERSION_MISMATCH

**Message**: current yasrman version is lower than expected

**Action**: The current backup tool *yasrman* version is too low.

### YAS-02521:ERR_BAK_RMAN_UNEXPECT_MSG_TYPE

**Message**: yasrman remote %s error, unexpect message type

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02522:ERR_BAK_BACKUP_SPACE

**Message**: backup space fail, reason is %s

**Action**: Tablespace migration has failed; please handle according to the error reason.

### YAS-02523:ERR_BAK_INVALID_MSG_CTX

**Message**: received message content is invalid, request %d but receive %d

**Action**: Internal error, please contact our technical support for resolution.

### YAS-02524:ERR_BAK_NOT_BASE_TAG_BAK

**Message**: the backup set %s does not an independent incremental backup set

**Action**: The specified baseline for incremental backup must belong to an independent incremental backup, please confirm its attributes using the DBA_BACKUP_SET view.

### YAS-02525:ERR_BAK_ONLY_INDEPEND_INCR

**Message**: failed to execute backup statements, reason: %s

**Action**: Independent baseline incremental backup statement parsing failed, please verify the validity of the statement.

### YAS-02526:ERR_BAK_ONLY_BASE_INDEPEND

**Message**: only incremental backups of LEVEL 0 can be specified as INDEPEND

**Action**: Only LEVEL 0 incremental backups can be designated as INDEPEND.

### YAS-02527:ERR_BAK_MISMATCH_WITH_DB

**Message**: the specified backup set does not match the target database ID

**Action**: The target database of the restore does not match the database ID that generated the backup set; please use a data backup set generated by the same database to execute incremental restore.

### YAS-02528:ERR_BAK_BASE_BAK_NOT_INCR

**Message**: the specified baseline does not an incremental backup set

**Action**: The specified baseline backup set is not an incremental backup; please specify a backup set tag that meets the conditions as a baseline.

### YAS-02529:ERR_BAK_BASE_INCR_MISMATCH

**Message**: after restoring the database, a new baseline backup is required

**Action**: After executing the restore operation on the database, it is necessary to redo all independent incremental backups.

### YAS-02530:ERR_BAK_INCR_DISCONTINUITY

**Message**: the specified incremental backup set does not contiguous with the current database baseline

**Action**: The specified backup set is not contiguous with the current restored database, please use a continuous incremental backup set from the same database for the restore process.

### YAS-02531:ERR_BAK_NOT_RST_STATE

**Message**: the current database does not in restore state

**Action**: The current database state does not meet the criteria for independent backup set restoration; please confirm if the RECOVER operation or rebuild operation has been executed, or you may need to restore the database again.

### YAS-02532:ERR_BAK_RST_INCR_SPLIT

**Message**: the backup set and the current database does not belong to the same incremental backup chain

**Action**: The current database and the backup set being generated have inconsistent restore times; please check if the source database has executed the RESTORE operation, or you may need to redo the baseline.

### YAS-02533:ERR_BAK_BASETAG_RST_FAILED

**Message**: last indenpend incremental restore failed

**Action**: The last independent incremental restore failed, please check if the CTRL1.BAK file is left in the $YASDB_DATA/dbfiles directory. You need to manually rename files like this to CTRL1, making it share the same name as the configuration file's specified ctrl file, then re-execute the RESTORE operation.

### YAS-02534:ERR_BAK_BASETAG_RST_ONLYINCR

**Message**: only incremental backup sets can be continuously restored

**Action**: Only incremental backup sets are applicable for continuous restore; please check if the backup set is usable.

### YAS-02535:ERR_BAK_INCR_RST_FAILED

**Message**: database incremental restore does not completed

**Action**: The last incremental RESTORE may have failed, leading to an incomplete database restoration. Please investigate the reasons for the RESTORE failure. If it is due to insufficient disk space or other environmental reasons, you can repair the environment without cleaning up database files and re-execute the incremental RESTORE command. Alternatively, clean up leftover database files and restore using other backup sets.

### YAS-02536:ERR_BAK_RECOVER_HAS_COMPLETED

**Message**: crash recovery has been completed and cannot be executed again

**Action**: The crash recovery has been completed and cannot be repeated. If needed, open the database to the mount phase to continue the recover operation or re-execute the restore operation and then continue with the recover operation.

### YAS-02537:ERR_BAK_NOT_RESTORE_REDO_FILE

**Message**: the redo file of instance %u in the backup set cannot be restored, file name %s, sequence# %u, reset id %u

**Action**: For specific reasons, refer to the underlying error code. This error is common during restore operations under the following necessary conditions:

- There are backup redo files in the backup set, commonly found in YAC backups, standby database backups, etc. These backups capture the current state of the redo files.
- An identically named redo file already exists in the environment, and the redo file in the backup set is older than that one.
- The redo file in the backup set cannot find corresponding archive files in the environment, or those archive files are corrupted.

When all three conditions are met, the redo file in the backup set cannot overwrite the identically named files already present in the environment, leading to restoration failures. Possible strategies to handle this exception include:

- If copies of the archive exist, you can copy them to the current environment's archive directory.
- If no copies exist, you cannot restore to the state of the latest redo file. DBA intervention is necessary to resolve issues with that redo file to prevent backup set restore failures.

### YAS-02538:ERR_BAK_NOT_REUSE_REDO_FILE

**Message**: there is a redo file with the same name that cannot be reused

**Action**: The file cannot be reused during restoration of the backup set, resulting in inability to recover the redo files of the backup set. For specific recovery strategies, please check the upper-level error code, which is typically YAS-02537.

### YAS-02539:ERR_BAKARCH_INVALID_RANGE

**Message**: the specified archive range is illegal, reason: %s

**Action**: The specified range of archive files is incorrect, please specify valid boundaries with the left boundary being smaller than the right boundary.

### YAS-02540:ERR_BAKARCH_NO_ARCH_BAKSET

**Message**: no backup of archived log for thread %u with sequence %u found to restore

**Action**: In the specified archive backup set, the corresponding archived log file backup cannot be found, please specify a qualifying backup set.

### YAS-02541:ERR_BAKARCH_NO_MATCH_ARCH

**Message**: specification does not match any archived log in the repository

**Action**: The specified archive range does not contain qualifying Archive Log Files, please confirm that the specified archive range is valid.

### YAS-02542:ERR_BAKARCH_ILLEGAL_SYNTAX

**Message**: illegal statement, reason: %s

**Action**: The specified archive backup or restore statement is illegal; please use the correct statement as prompted.

### YAS-02543:ERR_BAKARCH_ARCH_DISCONTINUITY

**Message**: the target archive file is discontinuous, start sequence: %u

**Action**: The archive files in the current database are non-continuous. Please check for existing archive files or confirm whether there were any archiving operations on the database during use.

### YAS-02544:ERR_BAKARCH_ARCH_NOT_EXIST

**Message**: instance: %u, specification archive file sequence: %u does not exist in the repository

**Action**: The specified sequence corresponding archive file does not exist within the current database, please check whether the statement used is valid.

### YAS-02545:ERR_BAKARCH_AFTER_DB_RST

**Message**: archive file recovery must be performed after the database has been restored but before recover has been executed

**Action**: The restore operations of the database must be completed before executing archive recovery.

### YAS-02546:ERR_BAKARCH_TOO_MUCH_TO_RST

**Message**: too many archive file need restore

**Action**: The number of archive files needing restoration in a single instance is too high; please select suitable Archive Log Files restoration ranges, splitting them and executing multiple recoveries.

### YAS-02547:ERR_BAKARCH_SEQ_RST_INVALID

**Message**: there is a gap in the restored archived logs on instance %u, reason: %s

**Action**: There is a gap in the specified restored Archive Log Files on instance without continuity. Ensure that the restored Archive Log Files maintain continuity and check the continuity of the ASN available for restoration.

### YAS-02548:ERR_BAKARCH_MISMATCH_BAKSET

**Message**: the specified backup set type does not match the target

**Action**: Ensure that the type of the specified backup set matches the type of the restoration target.

### YAS-02549:ERR_BAK_PITR_BAKSET_NOT_FOUND

**Message**: no valid database backup set was found

**Action**: During PITR operations, no valid database backup set was found. Please check if the database was backed up before the current time point or confirm if the DBID is correctly set.

### YAS-02550:ERR_BAK_INVALID_DEPLOY_MODE

**Message**: the backup command does not match the current deployment mode

**Action**: Please use backup commands that conform to the current deployment style.

### YAS-02551:ERR_BAK_HAS_DUPLICATION_SPC

**Message**: the tablespace %s was created by migration

**Action**: The current node has migration-created tablespace contents; please recreate the incremental backup baseline LEVEL 0.

### YAS-02552:ERR_BAK_INVALID_BAK_SET

**Message**: invalid backupset %s, reason: %s

**Action**: The specified backup set is unavailable; please check and specify the correct backup set file.

### YAS-02553:ERR_BAK_READONLY_NEED_FORCE

**Message**: only allow force full backup in read only mode

**Action**: Use forced full backup in read-only mode.

### YAS-02554:ERR_BAK_ABNORMAL_EXIT

**Message**: %s process exited abnormal, reason: %s

**Action**: The backup or BUILD process has exited abnormally; please refer to the detailed error information. If it was due to connection timeout, you need to re-execute the BUILD operation.

### YAS-02555:ERR_BAK_RESTORE_NOT_ALLOWED

**Message**: restore database is not allowed, reason: %s

**Action**: Currently, the database cannot execute restore operations, possible reasons include:

- yasom election is enabled: This indicates that [yasom election](../Tools Guide/yasboot/Introduction to yasboot Command/yasboot election) has been enabled. You need to first disable the election through *yasboot*, then execute the restore operation.

### YAS-02556:ERR_BAK_TAR_CANNOT_BACKUP

**Message**: the target object %s cannot be backed up, reason: %s

**Action**: The specified target tablespace cannot be backed up, common reasons include:

- The tablespace is a temporary tablespace and cannot be backed up.
- The datafile under the tablespace is corrupted.
- The specified tablespace name is duplicated.

### YAS-02557:ERR_BAK_TARGET_FILE_IN_USE

**Message**: the specified file: %s is in use

**Action**: The target tablespace or data file is currently in use; please check the status of that data file or tablespace before executing the corresponding operation.

### YAS-02558:ERR_BAK_NEED_LAST_BAKSET

**Message**: the backup set has expired

**Action**: The backup set used is much older than the recoverable time point; please execute the restoration operation using a more recent backup set.

### YAS-02559:ERR_BAK_DB_RECOVER_RUNING

**Message**:  the database recovery process is running

**Action**: Another recovery operation is currently in progress; please wait until this recovery process has completed.

### YAS-02560:ERR_BAK_TAR_CANNOT_RESTORE

**Message**: the target object %s cannot be restored, reason: %s

**Action**: The specified restoration object cannot be restored, possible reasons include:

- The target is a temporary tablespace.
- The specified tablespace or datafile does not exist in the specified backup set.

### YAS-02561:ERR_BAK_ARCHRST_EXCEED_LIMIT

**Message**: archive file exceeds restore limit: %lu

**Action**: The number of archive files awaiting restoration exceeds the maximum allowable limit; please check the specified size and specify the correct range.

### YAS-02562:ERR_BAK_OBJ_NOT_IN_BACKUPSET

**Message**:  the recovered object is not in the specified backup set

**Action**: The specified tablespace for restoration does not exist in the target database; please check the validity of the tablespace name.

### YAS-02570:ERR_RMAN_INSTANCE_RUNNING

**Message**: yasrman has been started by another process

**Action**: The yasrman backup process has been started. Please wait for the current backup process to complete or manually terminate the backup process.

### YAS-02571:ERR_RMAN_DSTBAK_ONLY_CN

**Message**: cluster %s statement must be executed on cn node

**Action**: Distributed backup must be executed on the CN node.

### YAS-02572:ERR_RMAN_SELECT_NULL_ROWS

**Message**: execute statement %s on cluster, but return null rows

**Action**: Executed query node SQL statement on the distributed cluster, but the result is empty. Please check the cluster's operational status.

### YAS-02573:ERR_RMAN_NODE_STATUS_ERROR

**Message**: cluster node %s %d-%d running_state is not normal, current is %s

**Action**: The state of the distributed cluster node is abnormal. Please ensure that the state is normal.

### YAS-02574:ERR_RMAN_XBSA_CALL

**Message**: failed to %s, XBSA error code %u

**Action**: XBSA interface call failed. Please handle it according to the XBSA error code.

### YAS-02575:ERR_RMAN_CONNECT

**Message**: failed to connect to yasrman, reason: %s

**Action**: Failed to connect to remote backup or streaming backup. Please handle it according to the error reason.

### YAS-02576:ERR_BAK_RMAN_VALIDATE

**Message**: backup set verification failed, reason: %s

**Action**: Backup set verification failed. The backup set file is corrupted.

### YAS-02577:ERR_BAK_RMAN_COMMON_ERROR

**Message**: yasrman abnormal exit, reason: %s

**Action**: The yasrman tool exited abnormally. Please refer to the error information for specific reasons.

### YAS-02578:ERR_BAK_RMAN_INVALID_INSTANCE       

**Message**: instance %u-%u unavailable, reason: %s

**Action**: An instance is unavailable. Please refer to the detailed error information for specific reasons.

### YAS-02579:ERR_BAK_RMAN_BAK_RST_PRESTAT  

**Message**: disk: %s free space pre-check failed, expected % PRIu64, free space: % PRIu64

**Action**: The remaining space on the backup or restore target disk is insufficient. Please increase the storage space on the current path or switch to a more suitable path before re-executing the current command.

### YAS-02580:ERR_BAK_ARCH_SKIP_BACKUP       

**Message**: archived logs of thread %u from sequence %u to %u reached the target backup limit

**Action**: The archived files within the specified backup interval have reached the designated maximum backup count, and the backup operation cannot continue. Please check if the current backup count of the archived files and the specified backup count limit are reasonable.

### YAS-02600:ERR_BAK_TOPO_CHANGED

**Message**: the topology status of cluster nodes has changed

**Action**: During the backup or recovery process, the topology status of the cluster has changed. Please check if other nodes in the cluster are running normally.

### YAS-02601:ERR_BAK_REDOLOCK

**Message**: instance %u redo lock has been released due to timeout

**Action**: Internal error; the backup set generated by YAC may have consistency issues. Please re-execute the backup command to generate the backup set.

### YAS-02602:ERR_BAK_BACKUPSET_INVALID

**Message**: backup set and recovery database configuration does not match

**Action**: The backup set does not match the configuration of the target recovery database. Please check the specifications of the backup set and the database configuration.

### YAS-02603:ERR_BAK_SWITCH_REDO

**Message**: cluster instance %u failed to switch log during backup

**Action**: Internal error; please contact our technical support for resolution.

### YAS-02604:ERR_BAK_GET_RCYBEGIN

**Message**: cluster instance %u failed to load point during backup

**Action**: Internal error; please contact our technical support for resolution.

### YAS-02605:ERR_BAK_IS_NOT_MASTER

**Message**: the current instance is not master role

**Action**: Before performing recovery operations, please check the role of the current instance.

### YAS-02606:ERR_BAK_LOCK_REDO

**Message**: cluster instance %u failed to lock redo during backup

**Action**: Internal error; please contact our technical support for resolution.

### YAS-02607:ERR_BAK_SWITCHREDO_TIMEOUT

**Message**: instance %u switch redo file timeout

**Action**: Internal error; please contact our technical support for resolution.

### YAS-02651:ERR_ANK_RTREE_ON_TEMP

**Message**: cannot create rtree index on temporary table

**Action**: Please ensure the table type is not a temporary table and recreate the RTree index.

### YAS-02652:ERR_ANK_SPLIT_INVALID_SUBPART

**Message**: subpartition descriptions cannot be specified during the SPLIT of a partition of a *-list or *-range partitioned table

**Action**: It is not allowed to specify subpartition definitions when splitting *-list or *-range.

### YAS-02653:ERR_ANK_SPLIT_INVALID_VALUE

**Message**: value does not exist in partition %s

**Action**: Please check that the partition boundary values exist in the partition to be split.

### YAS-02654:ERR_ANK_DUPLICATED_PART

**Message**: resulting partition name conflicts with that of an existing partition

**Action**: Please check if the resulting partition name conflicts with any existing partition names.

### YAS-02655:ERR_ANK_AT_LEAST_ONE_VALUE

**Message**: resulting list partition must contain at least one value

**Action**: Please ensure that the resulting list partition contains at least one boundary value.

### YAS-02656:ERR_ANK_SPLIT_HASH_PART

**Message**: could not split/merge hash partition

**Action**: Cannot split Hash Partition; it is recommended to use add partition.

### YAS-02657:ERR_ANK_SPLIT_AT_ON_LIST_PART

**Message**: could not split at on list partition

**Action**: Please use split values to split the list partition.

### YAS-02658:ERR_ANK_SPLIT_VALUES_ON_RANGE_PART

**Message**: could not split values on range partition

**Action**: Please use split at to split the range partition.

### YAS-02659:ERR_ANK_ADD_INVALID_SUBPARTS

**Message**: only one subpartition can be added for *-hash composite partition table or missing subpartition bound for *-range/list composite partition table

**Action**: Please ensure that only one subpartition is added for the *-hash Composite Partition table or specify partition boundaries for other types of subpartitions.

### YAS-02660:ERR_ANK_XA_TOO_MANY_SLICE

**Message**: too many slices in distributed transaction

**Action**: Please reduce the number of slices generated in the distributed transaction.

### YAS-02661:ERR_ANK_SPACE_READ_ONLY

**Message**: space is read only

**Action**: The tablespace is currently in read-only status. If the tablespace migration has been successful, please change the tablespace to read-write status.

### YAS-02662:ERR_ANK_IMPORT_DATAFILE

**Message**: import datafile fail, reason is %s

**Action**: Data file import failed. Please address this according to the error reason.

### YAS-02663:ERR_ANK_RECLAIM_SEGMENT_NOT_EXISTS

**Message**: reclaim segment not exists

**Action**: Internal error; please contact our technical support for resolution.

### YAS-02664:ERR_ANK_UNTRANSPORTABLE_SPACE

**Message**: untransportable tablespace, %s %s is not contained in tablespace set

**Action**: Tablespace migration self-containment check failed; please handle according to the specific reason.

### YAS-02665:ERR_ANK_CREATE_WHILE_SPACE_MOVING

**Message**: cannot create object, bulk load or alter slice while moving space

**Action**: Object creation, bulk load, or slice alteration is not allowed during tablespace migration. Please wait until the tablespace migration is complete before proceeding.

### YAS-02666:ERR_ANK_INVALID_MIGRATE_PATH

**Message**: invalid migrate path, %s

**Action**: Invalid tablespace migration destination path; please review the specific path specifications.

### YAS-02667:ERR_ANK_CONSTRAINT_DISABLE_VALIDATE

**Message**: no insert/update/delete on table with constraint disabled and validated

**Action**: Please delete the constraints that are disabled and validated.

### YAS-02668:ERR_ANK_NOTNULL_ENABLE_NOVALIDATE

**Message**: cannot enable not null constraint with novalidate when contains null values

**Action**: Do not enable the non-null constraint while ignoring checks.

### YAS-02669:ERR_ANK_MVIEW_NEVER_REFRESH

**Message**: cannot explicitly refresh a NEVER REFRESH materialized view

**Action**: Refreshing the materialized view set to NEVER REFRESH is not allowed. If needed, you can modify the setting using the ALTER MATERIALIZED VIEW statement.

### YAS-02670:ERR_ANK_MVIEW_REFRESH_PATH

**Message**: error in materialized view refresh path

**Action**: An error occurred during the refresh of the materialized view. Please retry or fix the table structure according to the specific error.

### YAS-02671:ERR_ANK_OUTLINE_IS_BUSY

**Message**: outline %s is busy, try later

**Action**: The outline is being modified by another session; please try again later.

### YAS-02672:ERR_ANK_SPACE_BROKEN

**Message**: tablespace %s is broken

**Action**: Operations on a damaged tablespace are not allowed.

### YAS-02673:ERR_ANK_SPACE_MOVING

**Message**: space is read only while moving

**Action**: The tablespace is currently in read-only status. If the tablespace migration has been successful, please change the tablespace to read-write status.

### YAS-02674:ERR_ANK_SPACE_DDL_FORBIDDEN

**Message**: space ddl is forbidden, because %s

**Action**: DDL operations on the tablespace are not allowed at this time; please try again later.

### YAS-02675:ERR_ANK_RESIZE_FILE_FORBIDDEN

**Message**: %s datafile is forbidden, because %s

**Action**: Extending the Data File is not allowed at this time; please try again later.

### YAS-02676:ERR_ANK_INVALID_SUBPART_ATTR

**Message**: this physical attribute may not be specified for a table subpartition

**Action**: Please remove this option. Subpartitions only allow specifications of tablespace and segment creation options.

### YAS-02677:ERR_ANK_VIRTUAL_PART

**Message**: virtual partition is not operational, distribution information is incorrect

**Action**: Virtual partition cannot be operated on; please check if the shard data is correct.

### YAS-02678:ERR_ANK_INVALID_PART_METHOD

**Message**: an unsupported partitioning method was specified in this context

**Action**: Subpartitions in Composite Partition cannot be interval partitions. Please remove the interval option.

### YAS-02679:ERR_ANK_GLOBAL_OID_LOAD_TIMEOUT

**Message**: load global object id timeout, wait time %u milliseconds

**Action**: Timed out when requesting a global object ID. Please check the status of the nodes and whether the network connection is normal.

### YAS-02680:ERR_ANK_ARCHIVE_NOT_MISMATCH

**Message**: failed to register the archive logs because archive log %s does not match archive log %s

**Action**: The registered archives are not continuous with the existing database archives and cannot be applied normally. Register the correct archive files.

### YAS-02681:ERR_ANK_ARCHIVE_ALREADY_REGISTER

**Message**: archive log with sequence number %u has been registered

**Action**: The sequence number of the archive has already been registered. If necessary, you can use the OR REPLACE clause to overwrite the original file.

### YAS-02682:ERR_ANK_ARCHIVE_NOT_REGISTER

**Message**: failed to register archive log %s, reason: %s

**Action**: Archive registration has failed. Common causes include:

- The reset ID of the archive is smaller.

  The reset ID of the database is incremental and cannot register an archive file with a reset ID smaller than the current database (the current database's reset ID can be checked in v$database).

- The archive file does not match the database.

  If the node that generated the archive file has encountered a log inconsistency with the current node (brain split occurred), the archive cannot be applied to the current node. Please select the archive copy of the current database or the archive backup set before the brain split for registration. If you need to restore or manually register the archive of a new primary database, please ensure that this database is the new primary database, or restored from the backup set of the new primary database.

### YAS-02683:ERR_ANK_TABLESPACE_IS_BUSY

**Message**: tablespace %s is busy, try again later

**Action**: The objects in the tablespace are in use; please try again later.

### YAS-02684:ERR_ANK_EXTERNAL_WITH_TEMP

**Message**: cannot create a temporary table with EXTERNAL organization

**Action**: External tables cannot be temporary tables; please check the table creation statement.

### YAS-02685:ERR_ANK_WRONG_EXTERNAL_OPERATION

**Message**: operation not supported on external organized table

**Action**: This operation cannot be performed on external tables.

### YAS-02686:ERR_ANK_WRONG_EXTERNAL_COLUMN

**Message**: column type not supported on external organized table

**Action**: There are unsupported data types in the external table; please modify.

### YAS-02687:ERR_ANK_EXTERNAL_REJECT_LIMIT

**Message**: reject limit reached

**Action**: There are errors in the data of the external table; please refer to the error log.

### YAS-02688:ERR_ANK_FILE_CONTAIN_SPECIFICATION

**Message**: file name cannot contain a path specification: %s

**Action**: The file name contains a directory separator; please modify.

### YAS-02689:ERR_ANK_NO_FILENAME_IN_LOCATION

**Message**: no file name specified in LOCATION clause

**Action**: No file name is specified in the location; please modify.

### YAS-02690:ERR_ANK_NO_DIRECTORY_FOR_LOCATION

**Message**: no directory object specified for location %u in LOCATION clause

**Action**: There is no relevant directory for the current location; please modify.

### YAS-02691:ERR_ANK_FILENAME_LEN_EXCEEDS

**Message**: length of file name exceeds limit %u

**Action**: The file path exceeds the limit; please modify.

### YAS-02692:ERR_ANK_FILE_NOTEXISTIN_DIR

**Message**: file %s in %s not found

**Action**: The file does not exist in its directory; please modify.

### YAS-02693:ERR_ANK_RENAME_BUILTIN_SPACE

**Message**: cannot rename built-in tablespace

**Action**: This operation is not allowed.

### YAS-02694:ERR_ANK_SAVEPOINT_NOT_ALLOWED

**Message**: cannot create savepoint if %s 

**Action**: Creating a savepoint is not allowed under the conditions described in the error.

### YAS-02695:ERR_ANK_MIN_SUPPLY_LOG

**Message**: unable to drop minimal supplemental logging

**Action**: Minimal supplemental logging can only be deleted when no other database-level supplemental logging is enabled. Check V$DATABASE's SUPPLEMENTAL_LOG_DATA_PK and SUPPLEMENTAL_LOG_DATA_ALL columns, delete other database-level supplemental logging, and retry.

### YAS-02696:ERR_ANK_MULTI_SUPPLY_LOG

**Message**: multiple specification of a supplemental logging attribute

**Action**: Remove the duplicate fields.

### YAS-02697:ERR_ANK_EXT_PARSE_ACSPARS

**Message**: failed to parse access parameters

**Action**: Modify the access parameters of the external table according to the error message.

### YAS-02698:ERR_ANK_NO_DIRECTORY_FOR_LOG

**Message**: no directory object specified for %s file

**Action**: Specify a directory for the log file.

### YAS-02699:ERR_ANK_INDEX_ON_AC

**Message**: create index on access constraint is not allowed

**Action**: Creating an index on AC is not allowed.

### YAS-02700:ERR_ANK_DROP_TABLESPACE_OBJECTS

**Message**: failed to remove all objects in the tablespace specified

**Action**: Delete the objects in the tablespace before deleting the tablespace.

### YAS-02701:ERR_ANK_ARCHIVES_NOT_DELETED

**Message**: some archive logs are not successfully deleted 

**Action**: Only some archives were deleted, while others failed to delete. Refer to the underlying error code for specific reasons for the failure.

### YAS-02702:ERR_ANK_BATCH_GATHER_STATS

**Message**: %s 

**Action**: Error information generated from collecting statistics for the entire library/specifying user; address the corresponding issues according to the error message before retrying.

### YAS-02703:ERR_ANK_EXCEED_MAX_LOB_SIZE

**Message**: operation would exceed maximum size allowed for a LOB value, which is (4 gigabytes - 1) * DB_BLOCK_SIZE 

**Action**: Reduce data so that the size of the LOB after insertion does not exceed (4G - 1) * DB_BLOCK_SIZE.

### YAS-02704:ERR_ANK_MODIFY_COL_WITH_INDEX

**Message**: table must be empty to modify indexed column datatype

**Action**: The table must be empty before modifying the data type of indexed columns.

### YAS-02705:ERR_ANK_MVIEW_FORBID_ON_COMMIT

**Message**: cannot set the ON COMMIT refresh attribute for the materialized view

**Action**: Attempting to modify the refresh mode of the materialized view.

### YAS-02706:ERR_ANK_TEMP_SPACE_PATH_INCOMPATIBLE

**Message**: Local temporary tablespace contains YFS and local file system at the same time is not allowed

**Action**: It is not allowed for local temporary tablespaces to simultaneously contain YFS and local file system paths.

### YAS-02707:ERR_ANK_SYNC_LOCAL_SPACE_FAILED

**Message**: The error in instance %u is that %s

**Action**: The instance is unable to continue creating, changing, or deleting local temporary tablespaces due to the error described.

### YAS-02708:ERR_ANK_LOCAL_TEMP_SPACE_ON_CLUSTER

**Message**: Local temporary tablespaces can only be used on clusters

**Action**: Local temporary tablespaces can only be used in clusters.

### YAS-02709:ERR_ANK_OPEN_LOB_RDWR_NO_TRANSACTION

**Message**: cannot open lob in readwrite mode without transaction

**Action**: It is not possible to open a LOB in read-write mode without a transaction.

### YAS-02710:ERR_ANK_OPEN_LOB_RDWR_SPAN_TRANSACTION

**Message**: cannot open lob in readwrite mode spanning transaction

**Action**: You cannot open a LOB in read-write mode across transactions.

### YAS-02711:ERR_ANK_COMMIT_LOB_NOT_CLOSED

**Message**: all open lobs should be closed when commit transaction

**Action**: There should be no open LOBs when committing a transaction. Please close all open LOBs before committing.

### YAS-02712:ERR_ANK_WRITE_LOB_SPAN_TRANSACTION

**Message**: cannot write lob spanning transaction

**Action**: You cannot write a LOB across transactions.

### YAS-02713:ERR_ANK_VIEW_REINVALIDATION

**Message**: errors during revalidation of %s.%s 

**Action**: Failed to refresh view metadata; refer to the underlying error code for failure reasons.

### YAS-02714:ERR_ANK_ARCHIVES_HAS_DELETED

**Message**: the archive log file with ASN %u for instance %u has been deleted

**Action**: The target archive file has been deleted; please check the configuration of the archive file auto-cleanup parameters.

### YAS-02715:ERR_ANK_XA_DUPLICATED_GTID

**Message**: XA: duplicate transaction identifier

**Action**: Please check if the GTID is duplicated.

### YAS-02716:ERR_ANK_XA_INVALID_FLAG

**Message**: XA: invalid flag

**Action**: Please use the correct flag.

### YAS-02717:ERR_ANK_XA_NO_CURRENT

**Message**: XA: no current xa transaction

**Action**: Ensure that there is an XA transaction on this connection.

### YAS-02718:ERR_ANK_XA_GTID_NOT_EXIST

**Message**: XA: transaction with specified identifier does not exist

**Action**: Ensure that the specified GTID exists.

### YAS-02719:ERR_ANK_TOO_MANY_LOCAL_TEMPFILE

**Message**: maximum number of local space tempfile count is %u

**Action**: Please do not create more than the maximum number of local temporary files; the maximum number of files in local temporary tablespace is the maximum number of files in the tablespace divided by the number of instances, rounded down.

### YAS-02720:ERR_ANK_INVALID_MVIEW_OPERATION

**Message**: operation not supported on materialized view

**Action**: This operation cannot be executed on a materialized view.

### YAS-02721:ERR_ANK_INVALID_AC_BTREE_OPERATION

**Message**: operation not supported on access constraint on HEAP/TAC

**Action**: This operation cannot be executed on an AC of HEAP/TAC tables.

### YAS-02722:ERR_ANK_INVALID_SUBPART_TEMPLATE_NAME

**Message**: Attempt to generate name from parent name string and template name string failed as the combine named would have been longer than allowed

**Action**: Please shorten the length of the names in the primary partition or the secondary partition template.

### YAS-02723:ERR_ANK_SHUTDOWN_FAILED

**Message**: the database shutdown cannot be performed normally and database needs to be aborted. reason: %s

**Action**: This error indicates that an exception occurred during the shutdown process of the database, unable to consistently shut down the database or revert to the open state; the database process will be forcibly exited. This usually occurs in the following cases:

- In maximize protection mode, due to the standby database error, primary database transaction submission or checkpoint blocking prevents shutdown from executing normally.
- Standby database apply is blocked due to resource issues, making shutdown impossible.

### YAS-02724:ERR_ANK_XACT_COMMENT_LEN

**Message**: transaction comment length exceeds limit 256

**Action**: Reduce the length of the transaction's COMMENT.

### YAS-02725:ERR_ANK_DROP_UNDO_DATAFILE

**Message**: cannot drop datafile of undo tablespace

**Action**: This operation is not allowed.

### YAS-02726:ERR_ANK_PK_NOT_FOUND

**Message**: referenced table does not have a primary key

**Action**: Please check if the primary table has a primary key constraint.

### YAS-02727:ERR_ANK_SESSIONS_PER_USER

**Message**: exceeded simultaneous SESSIONS_PER_USER limit

**Action**: Please check the sessions_per_user configuration in the user's corresponding profile and ensure that the number of user session connections does not exceed the Maximum Availability concurrent connection limit.

### YAS-02728:ERR_ANK_SPACE_EMPTY

**Message**: temporary tablespace %s is empty

**Action**: The current temporary tablespace is empty. Please add files to this temporary tablespace.

### YAS-02729:ERR_ANK_ENCRYPTION_ALGORITHM

**Message**: missing or invalid encryption algorithm

**Action**: Please specify the correct encryption algorithm. If you need to use national cryptography algorithms, please ensure the database server has installed [OpenSSL that meets the version requirements](../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies.md#openssl).

### YAS-02730:ERR_ANK_YSTREAM_ARG

**Message**: argument '%s' is invalid, reason: %s

**Action**: Please modify the YStream input parameter format according to the prompt.

### YAS-02731:ERR_ANK_YSTREAM_SERVER_STATUS

**Message**: YStream server status is invalid, current status: %s

**Action**: Please ensure that the YStream server is in the expected state.

### YAS-02732:ERR_ANK_YSTREAM_SERVER_NOT_FOUND

**Message**: YStream server %s is not found

**Action**: Please enter the correct name or create the YStream server.

### YAS-02733:ERR_ANK_YSTREAM_SERVER_EXIST

**Message**: YStream server %s already exists

**Action**: Please use a different name.

### YAS-02734:ERR_ANK_TOO_MANY_YSTREAM

**Message**: too many YStream %s, limit %u

**Action**: Please do not exceed the given limit.

### YAS-02736:ERR_ANK_YSTREAM_VERIFY

**Message**: failed to %s, reason: %s

**Action**: YStream verification failed; please check the configuration according to the error message.

### YAS-02737:ERR_ANK_EXEC_COMMIT_IN_XA

**Message**: COMMIT is not allowed in a subordinate session

**Action**: In a connection bound to an XA transaction, using standalone transaction statements for submission is not allowed.

### YAS-02738:ERR_ANK_FAILED_SET_PROTECT_MODE

**Message**: instance %u cannot set protection mode

**Action**: Failed to set the protection mode; check the underlying error for specific failure reasons.

### YAS-02739:ERR_ANK_LONG_TRANSACTION

**Message**: transaction is too long

**Action**: The transaction is too long; please reduce the bulk size of the deduplication transaction.

### YAS-02740:ERR_ANK_INVALID_STAT_TABLE

**Message**: Unable to %s table %s: does not appear to be a statistics table

**Action**: Please specify the name as `DBMS_STATS.CREATE_STAT_TABLE` when establishing the stattab parameter.

### YAS-02741:ERR_ANK_PDML_MODIFY_DML

**Message**: cannot modify an object in parallel after modifying it

**Action**: Disable parallel DML and then reexecute the business, or break down the statement into two transactions. Commit the operation before the parallel modification and then perform the parallel modification.

### YAS-02742:ERR_ANK_READ_PDML

**Message**: cannot read/modify an object after modifying it in parallel

**Action**: Disable parallel DML and then reexecute the business, or break down the statement into two transactions. After committing the parallel modifications, proceed with read/write operations.

### YAS-02743:ERR_ANK_PXACT_TO_XA

**Message**: cannot convert parallel transaction to xa

**Action**: It is not supported to convert parallel transactions into XA transactions. Roll back the current transaction, disable parallel DML, and reexecute the business.

### YAS-02744:ERR_ANK_DUP_TS_QUOTA

**Message**: duplicated quota/floor clause for a tablespace

**Action**: There are duplicate quota or floor clauses. Please do not set multiple quota/floor values for the same tablespace.

### YAS-02745:ERR_ANK_TS_QUOTA

**Message**: tablespace quota error, reason: %s

**Action**: Please address this failure according to the specific error reason.

### YAS-02746:ERR_ANK_GRANT_TO_ROLE

**Message**: cannot grant %s to a role

**Action**: This privilege cannot be granted to roles.

### YAS-02747:ERR_ANK_VIOLATE_FLOOR

**Message**: violate the floor constraint on tablespace: %s

**Action**: This operation would violate the lower limit constraint of the corresponding tablespace. Please configure users' reserved space according to the specific use of the tablespace.

### YAS-02748:ERR_ANK_QUOTA_CHECK_MAX

**Message**: exceed the quota limit, tablespace: %s, user: %s

**Action**: The user's space usage will exceed the quota for this tablespace, causing allocation to fail. Please adjust the user's tablespace quota.

### YAS-02749:ERR_ANK_QUOTA_CHECK_MIN

**Message**: violate the floor constraint on tablespace: %s, user: %s

**Action**: The user's allocation of space in this tablespace will violate the lower limit reserved for other users. Please check the lower limit configuration of the tablespace.

### YAS-02750:ERR_ANK_INVALID_PROFILE_ID

**Message**: profileId %u is already used or invalid

**Action**: Internal error; please contact our technical support for resolution.

### YAS-02751:ERR_ANK_SUPPLY_ENABLE_WITH_YSTREAM

**Message**: cannot enable database supplemental logging when YStream server created

**Action**: Database-level supplemental logging cannot be enabled when a YStream server is created. It is recommended to enable database-level supplemental logging before creating the YStream server, or enable table-level supplemental logging for specified tables.

### YAS-02752:ERR_ANK_PARSE_SUPPLY_ENABLE

**Message**: cannot enable database supplemental logging during logical log parsing

**Action**: There was an operation to enable database-level supplemental logging encountered during the YStream logical log parsing process, preventing further log parsing. Please ensure that after the YStream starting point, there are no operations to enable database-level supplemental logging in the logs. If so, please recreate YStream.

### YAS-02753:ERR_ANK_DROP_DEFAULT_SPACE_SET

**Message**: cannot drop the default tablespace set of user

**Action**: Before performing this operation, please reset the user's default tablespace set.

### YAS-02754:ERR_ANK_IMPORT_INVALID_HGRAM

**Message**: import invalid histogram statistics, table name: %s, column name: %s

**Action**: Please re-export the statistics for that column and then import.

### YAS-02755:ERR_ANK_VIEW_CHECK_OPTION_VIOLATION

**Message**: view WITH CHECK OPTION where-clause violation

**Action**: The data being operated on does not satisfy the CHECK OPTION condition of the view.

### YAS-02756:ERR_ANK_INVALID_TABLE_PRIV

**Message**: %s privilege not allowed for tables

**Action**: Please grant the correct object-level privilege on the table.

### YAS-02757:ERR_ANK_INVALID_TYPE_PRIV

**Message**: only EXECUTE, UNDER privileges are valid for types

**Action**: Only EXECUTE or UNDER object-level privileges can be granted to UDT-related objects; please retry.

### YAS-02758:ERR_ANK_INVALID_UNDER_OBJECT

**Message**: cannot grant UNDER privilege on this object

**Action**: Cannot grant UNDER privilege on a NOT FINAL object type; please retry.

### YAS-02759:ERR_ANK_REVOKE_TYPE_EXECUTE_DEP

**Message**: cannot revoke execute on a type with table dependents

**Action**: Please delete users' tables that use the UDT before revoking execute.

### YAS-02760:ERR_ANK_XA_ASYNC

**Message**: Asynchronous operation already outstanding

**Action**: There is already an outstanding asynchronous operation; please review whether the XA interface usage complies with XA protocol specifications.

### YAS-02761:ERR_ANK_XA_NOTA

**Message**: XID is not valid

**Action**: The XID is invalid; please provide a valid XID.

### YAS-02762:ERR_ANK_XA_INVAL

**Message**: Invalid arguments were given

**Action**: Invalid parameters have been provided; please provide valid parameters.

### YAS-02763:ERR_ANK_XA_PROTO

**Message**: Routine invoked in an improper context

**Action**: The routine was called in an improper context; please invoke it in the appropriate context.

### YAS-02764:ERR_ANK_XA_RMFAIL

**Message**: Resource manager unavailable

**Action**: The resource manager is unavailable; please try again later.

### YAS-02765:ERR_ANK_XA_DUPID

**Message**: XID already exists

**Action**: The XID already exists; please end any existing XA transactions or change the XID before retrying.

### YAS-02766:ERR_ANK_XA_OUTSIDE

**Message**: Resource manager doing work outside global transaction

**Action**: The resource manager is operating outside of the global transaction; please check whether the XA interface usage complies with XA protocol specifications.

### YAS-02767:ERR_ANK_VM_SWAPSIZE_NOT_ENOUGH

**Message**: can not allocate %lu bytes from columnar vm swap

**Action**: Please appropriately increase the COLUMNAR_VM_SWAP_SIZE configuration and retry.

### YAS-02768:ERR_ANK_FAILED_LOGIN_INTERVAL

**Message**: the account is locked, please wait %u second(s) and try again

**Action**: Please try to log in again later.

### YAS-02769:ERR_ANK_TOO_MANY_XRM

**Message**: too many transactions, active transactions should less than %u

**Action**: Too many concurrent active transactions; please try again later.

### YAS-02770:ERR_ANK_XA_AREA_INVALID

**Message**: distribute transaction area#%u is not ready

**Action**: The distributed transaction area is not ready; the main instance may be loading the managed transaction area or an instance may be joining the cluster. Please try again later.

### YAS-02771:ERR_ANK_SKIP_LOGIC_APPLY_DDL

**Message**: %s skipped due to %s

**Action**: An error occurred during the logical Apply process, and DDL/DML operations were skipped. This is often recorded in the DBA_LOGSTDBY_EVENTS view.

### YAS-02772:ERR_ANK_DB_NOT_LOGIC_STDBY

**Message**: not a valid logical standby database

**Action**: The current database is not a logical Standby Database. Please execute on a logical Standby Database. This is often recorded in the DBA_LOGSTDBY_EVENTS view.

### YAS-02773:ERR_ANK_LOGIC_DB_NOT_OPER

**Message**: logical standby cannot %s

**Action**: Constraints on the functionality of the logical Standby Database.

### YAS-02774:ERR_ANK_INVALID_BUFFER_OBJECT

**Message**: invalid buffer object %lu

**Action**: The specified dataOid does not match with the dataOid on the block when accessing the block; please retry with the correct dataOid.

### YAS-02775:ERR_ANK_CREATE_SYS_INDEX

**Message**: forbid to create system index

**Action**: Creating system indexes is prohibited.

### YAS-02776:ERR_ANK_DUPLICATE_PRIV_LISTED

**Message**: duplicate privilege or role listed

**Action**: The privilege names or role names granted at one time are duplicated; please modify and retry.

### YAS-02777:ERR_ANK_MAX_GRANT_ROLES

**Message**: cannot grant/revoke more than %d roles/privileges at the same time in %s mode

**Action**: The number of roles granted/revoked at one time exceeds the limit. Please reduce the number of roles to be granted and retry.

### YAS-02778:ERR_ANK_REDO_FILE_FOR_RECOVER

**Message**: log file %s needed for crash recovery of instance %u

**Action**: Please choose the corresponding repair method based on the actual scenario:

- If the database has not entered the OPEN state, you can try executing ALTER DATABASE OPEN to start the database. If it cannot be OPEN due to a corrupted log file, you need to restore the database from a backup set; please refer to [Backup and Recovery](../Database Administration/Backup and Recovery/00Backup and Recovery).
- If the database is in the OPEN state, please perform a full checkpoint once and then retry the current operation.

### YAS-02779:ERR_ANK_REDO_FILE_FOR_ARCHIVE

**Message**: log file %s of instance %u needs to be archived

**Action**: Please wait for the redo file to be archived before retrying, or use the UNARCHIVED clause for this operation (this operation may lead to unavailable backup sets, synchronization anomalies in standby databases, etc.).

### YAS-02780:ERR_ANK_NO_SUCH_LOGFILE

**Message**: there is no such log file in instance %u

**Action**: The specified redo file does not exist in the current instance. Please specify an existing redo file and retry.

### YAS-02782:ERR_ANK_SELF_CHANGE

**Message**: modify conflict in statement

**Action**: This operation is not allowed.

### YAS-02783:ERR_ANK_SELF_DELETE

**Message**: specified row no longer exists

**Action**: This operation is not allowed; the row has already been deleted in the statement or has been deleted via foreign key Cascade.

### YAS-02784: ERR_ANK_PART_MIXED_SYNTAX

**Message**: Partitions can not be merged with mixed syntax

**Action**: Please use a uniform partition specification method, specifying the partition either by name or uniformly using for (value, etc.) locating through partition key values.

### YAS-02785: ERR_ANK_LOWER_BOUND_RESULT

**Message**: can not reuse lower-bound partition as resulting partition

**Action**: Please do not specify the resulting partition as a partition with a lower boundary value.

### YAS-02786: ERR_ANK_LOWER_BOUND_FIRST

**Message**: lower-bound partition must be specified first

**Action**: Please specify the partition with the lower boundary value first when specifying the range partition to be merged.

### YAS-02787: ERR_ANK_SUBPARTS_NOT_ADJACENT

**Message**: subpartitions being merged are not adjacent

**Action**: Please confirm that the subpartitions to be merged belong to the same primary partition.

### YAS-02788: ERR_ANK_LISTPART_USING_MERGE_TO

**Message**: merging partitions or subpartitions using <name> TO <name> syntax is only supported for range partitioned tables

**Action**: Please do not use the A TO B syntax to specify the list partition to be merged; the list partition can only enumerate all partitions to be merged.

### YAS-02789: ERR_ANK_PART_KEY_NOT_EXIST

**Message**: The partition number is invalid or out-of-range

**Action**: The partition key value is illegal or does not belong to any partition; please reconfirm whether the partition key value is reasonable.

### YAS-02790: ERR_ANK_PART_SELF_MERGE

**Message**: cannot merge a partition with itself

**Action**: Please do not specify the same partition for self-merge; the statement "merge a, a to b" is illegal.

### YAS-02791: ERR_ANK_RST_PART_CONFLICT

**Message**: resulting partition name conflicts with that of an existing partition

**Action**: Please ensure that the resulting partition is not any existing partition that is not included in the partition list to be merged.

### YAS-02792: ERR_ANK_PARTS_NOT_ADJACENT

**Message**: partitions being merged are not adjacent

**Action**: Please specify the partitions to be merged in increasing order according to the partition boundaries.

### YAS-02793: ERR_ANK_XA_FORBID_DELTABUFFER

**Message**:cannot convert transaction to xa if transaction has insert/delete/update lsc table

**Action**: This operation is not allowed; the current transaction has made inserts/deletes/updates to the LSC table and cannot be converted to an XA transaction.

### YAS-02794: ERR_ANK_ILLEGAL_MERGE_SUBPART

**Message**: cannot merge subpartitions in non-composite table

**Action**: Cannot merge subpartitions in a non-Composite Partition table; please ensure the operation object is a Composite Partition table before executing this operation.

### YAS-02795:ERR_ANK_YSTREAM_OTHER_NODE_STARTED

**Message**: "%s while the stream service is started on the other nodes, please try again on group: %u node: %u"

**Action**: When the YStream server is started on other nodes, the current instruction cannot be executed. Please connect to the indicated node to retry.

### YAS-02796: ERR_ANK_SUSPEND_DDL

**Message**: the operation has been paused due to %s

**Action**: The database is undergoing a rolling upgrade or role switch; this operation is not allowed. Please wait for the rolling upgrade or role switch to complete and then retry.

### YAS-02797: ERR_ANK_BALANCER_RESET

**Message**: balancer cannot reset %s memory pool be smaller than the memory currently in use

**Action**: Memory adjustment failed; please retry when the business is idle.

### YAS-02798: ERR_ANK_DATABUCKET_IN_USE

**Message**: databucket '%s' is being used, cannot be dropped

**Action**: The operation cannot be completed at this time; please retry later.

### YAS-02799: ERR_ANK_SHARE_POOL_SHRINK

**Message**: cannot shrink share pool size online, current size: % PRIu64 , new size: % PRIu64

**Action**: The share pool cannot be shrunk online; to shrink the share pool, adjust the configuration file and restart the database.

### YAS-02800: ERR_ANK_SHARE_POOL_ITEM_IVNALID_MAX

**Message**: the %s maximum size(% PRIu64 ) cannot be less than its current size(% PRIu64 )

**Action**: Unable to set the size limit for the share pool member; this value must not be less than the current size. Please adjust the parameters and retry.

### YAS-02801: ERR_ANK_CREATE_PART_WITH_CREATE_IDX

**Message**: cannot create a partition while creating index

**Action**: Cannot create a partition while creating an index online; please retry after the index has been created.

### YAS-02802: ERR_ANK_FB_ON_NON_MASTER

**Message**: flashback database cannot be executed on non-master instance

**Action**: Full database flashback can only be executed on the master instance.

### YAS-02803: ERR_ANK_FB_IS_NOT_ENABLE

**Message**: flashback database is not enabled

**Action**: The full database flashback functionality is not enabled yet; please enable it and retry.

### YAS-02804: ERR_ANK_FB_IS_ENABLE

**Message**: flashback database is already turned on

**Action**: Full database flashback is currently on; please turn it off and retry.

### YAS-02805: ERR_ANK_FB_RESTORE_POINT_IS_OVERFLOW

**Message**: restore point is overflow

**Action**: The number of created restore points has exceeded the limit; please delete unnecessary restore points before creating new ones.

### YAS-02806: ERR_ANK_FB_RESTORE_POINT_IS_EXIST

**Message**: restore point is already exist

**Action**: A restore point with the same name already exists; please delete the restore point with the same name or modify the restore point name before retrying.

### YAS-02807: ERR_ANK_FB_RESTORE_POINT_IS_NOT_EXIST

**Message**: restore point not exist

**Action**: The specified restore point does not exist; please create the corresponding restore point before retrying.

### YAS-02808: ERR_ANK_FB_CREATE_FILE_PATH

**Message**: can not create flashback file directory

**Action**: Unable to create the full database flashback file directory; please check the server situation and retry.

### YAS-02809: ERR_ANK_FB_PARAM_INVALID_RANGE

**Message**: the value of parameter %s must be between %lu and %lu

**Action**: The value of the configuration parameter is invalid; please adjust the target value.

### YAS-02810: ERR_ANK_FB_INVALID_TARGET_SCN

**Message**: %s target scn invalidate, oldest scn %lu

**Action**: The target SCN for flashback is invalid; please adjust the target SCN.

### YAS-02811: ERR_ANK_FB_LOGFILE_CORRUPTED

**Message**: the flashback logfile %s is corrupted

**Action**: The full database flashback log file is damaged; please check the file.

### YAS-02812: ERR_ANK_FB_PACK_CORRUPTED

**Message**: the flashback logfile %s pack is corrupted, offset %lu

**Action**: The pack of the full database flashback log file is damaged; please check the file.

### YAS-02813: ERR_ANK_FB_GUARANTEE_POINT_EXIST

**Message**: %s for guarantee point exist

**Action**: A permanent restore point exists, preventing the operation from proceeding; please delete the permanent restore point before retrying.

### YAS-02814: ERR_ANK_FB_IS_FLASHBACKING

**Message**: flashback database is working

**Action**: Full database flashback is in progress; please continue the unfinished full database flashback before retrying.

### YAS-02815: ERR_ANK_FB_CLEAN_CURR_MARKER_ASN

**Message**: arch file could not be cleaned for current marker depend on

**Action**: The archive file cannot currently be cleaned; please retry later or use the optional field FORCE when deleting archives.

### YAS-02816: ERR_ANK_FB_NOT_SUPPORT_OPERATION

**Message**: can not flashback database to not support operation, target scn must bigger than %lu

**Action**: It is not possible to flashback to a point before unsupported operations. If the suggested SCN is 18446744073709551615, it indicates that flashback is currently not possible; please retry later.

### YAS-02817: ERR_ANK_FB_SPACE_OVER_LIMIT

**Message**: flashback database logfile space exceeds the upper limit

**Action**: The directory space for the full database flashback log file has reached the limit; please adjust the DB_FLASHBACK_FILE_DEST_SIZE configuration parameter.

### YAS-02818: ERR_ANK_YSTRM_START_WITHIN_DDL

**Message**: YStream start point cannot be in the middle of DDL transaction

**Action**: A DDL transaction's initial log was before the YStream parsing starting point, and the committing log for that transaction was after the YStream parsing starting point, causing the DDL transaction to be incomplete. Please recreate YStream, specify a different starting SCN, and ensure there are no DDL operations near that SCN.

### YAS-02819: ERR_ANK_ONLINEDDL_TOPO_CHANGED

**Message**: cluster topo changed

**Action**: YAC topology changed before and after the online index creation was completed; please retry.

### YAS-02820: ERR_ANK_AXC_JTMNGR_CLEANING

**Message**: cluster journal table manager is cleaning

**Action**: YAC online log table manager is cleaning; please retry later.

### YAS-02821: ERR_ANK_JT_BCST_FAILED

**Message**: failed to broadcast %s request

**Action**: YAC log table message broadcasting failed; please retry later.

### YAS-02822: ERR_ANK_SPC_IN_RECOVERING

**Message**: %s: %s is in recovering

**Action**: The target file is executing a recovery operation; please wait for it to complete.

### YAS-02823: ERR_ANK_ROLLING_UPGRADE

**Message**: database is undergoing a rolling upgrade

**Action**: The database is undergoing a rolling upgrade; please retry after it has completed.

### YAS-02824:ERR_ANK_ADD_INSTANCE_IS_FORBIDDEN

**Message**: cannot add instance when %s

**Action**: Please ensure that the transaction has ended, the database is not executing file operations, and the YStream server is not started before performing instance addition operations.

### YAS-02825:ERR_ANK_ADD_INSTANCE_ALREADY_MAX

**Message**: the current number of instances has reached the maximum number of instances

**Action**: The maximum instance limit has been reached, and further instance addition operations are not possible.

### YAS-02826:ERR_ANK_DURING_SCALE_INSTANCE_FORBID

**Message**: cannot %s when scale instance

**Action**: During instance scaling, distributed transactions, file operations, and instance state change operations are not permitted.

### YAS-02827:ERR_ANK_SCALE_INSTANCE_FAILED

**Message**: failed to scale instance, reason: %s

**Action**: Instance scaling failed. Please resolve the issue according to the specific error details.

### YAS-02828:ERR_ANK_FB_REDO_FILE_NOT_EXIST

**Message**: flashback database not started, required redo log sequence %u in thread %u is not available

**Action**: Full database flashback execution failed; the arch or redo it depends on cannot be accessed. Please adjust the flashback time point.

### YAS-02829:ERR_ANK_OPER_NOLOGGING

**Message**: The Operation on this nologging table only allowed on instance: %d

**Action**: The corresponding operation failed on this instance and can only be executed on the indicated instance.

### YAS-02830:ERR_ANK_SHARE_POOL_ITEM_MAX_OVERFLOW

**Message**: the %s maximum size(% PRIu64 ) cannot be larger than the share pool size(% PRIu64 )

**Action**: Unable to set the size limit for the share pool member; this value cannot exceed the total size of the share pool. Please expand the share pool and retry.

### YAS-02831: ERR_ANK_KEYSTORE_MEK_INVALID

**Message**: invalid master key identifier or master key value

**Action**: The ID or value of the master key is invalid; please enter a valid key ID or value.

### YAS-02832: ERR_ANK_KEYSTORE_MEK_TAG_INVALID

**Message**: tag is invalid or missing

**Action**: The provided TAG is invalid or missing; please enter a valid TAG.

### YAS-02833: ERR_ANK_KEYSTORE_PWD_NOT_OPEN

**Message**: password-based keystore is not open

**Action**: The password-based wallet is not open; please open the wallet first.

### YAS-02834: ERR_ANK_KEYSTORE_KEY_NOT_FOUND

**Message**: master key with the specified identifier does not exist

**Action**: The specified master key is invalid; please specify a valid master key.

### YAS-02835: ERR_ANK_KEYSTORE_IS_NOT_OPEN

**Message**: wallet is not open

**Action**: The wallet is not open; please open the wallet first.

### YAS-02836: ERR_ANK_KEYSTORE_CREATE_FAILED

**Message**: keystore cannot be created at the specified location '%s', %s

**Action**: The wallet file cannot be created at the specified location; please check the path and retry.

### YAS-02837: ERR_ANK_KEYSTORE_MEK_NOT_FOUND

**Message**: typed master key not found in wallet

**Action**: The activated master key was not found; please set and activate the master key first.

### YAS-02838: ERR_ANK_KEYSTORE_MEK_ID_EXISTS

**Message**: master key identifier exists in the keystore

**Action**: The specified master key ID already exists.

### YAS-02839: ERR_ANK_KEYSTORE_ALREADY_OPEN

**Message**: Encryption wallet, auto login wallet, or HSM is already open

**Action**: The wallet or HSM is already open and cannot be opened again.

### YAS-02840: ERR_ANK_KEYSTORE_INVALID_KEY

**Message**: check encryption key failed, encryption key is invalid

**Action**: Key value check failed; please investigate key-related operations, such as whether the wallet file is valid. If this error occurs during the online tablespace process, check whether the master key has been changed after the encrypted tablespace was taken offline.

### YAS-02841: ERR_ANK_COPY_BLOCK_FAILED

**Message**: when online recover, failed to copy current block for flushing current, obj %lu, block %u-%u-%u

**Action**: Failed to obtain the current page during online recovery; automatic retries will occur.

### YAS-02842: ERR_ANK_INVALID_DURING_BUILD_INDEX

**Message**: could not execute %s during creating/rebuilding index online

**Action**: Please check whether there are concurrent online CREATE/REBUILD index operations during this operation. If so, please wait for the online index creation/rebuilding to complete before executing this operation.

### YAS-02843: ERR_ANK_REDO_WITHOUT_ARCH_MODE

**Message**: the redo file or the archive file is not generated in archive mode, inst % PRIu32 asn % PRIu32

**Action**: YStream only supports parsing redo or Archive Log Files generated in Archiving Mode; please recreate the YStream server, reselect the parsing starting SCN, and ensure that the logs being parsed are compliant with the requirements.

### YAS-02844: ERR_ANK_SCALE_INSTANCE_REFORM_CLEAN

**Message**: all nodes failed when scaling instance of cluster, this operations are not allowed to be executed.

**Action**: If a fault occurs during cluster instance scaling, this operation is not permitted when the cluster is restarted. Please execute ALTER DATABASE OPEN first, then restart and retry the operation.

### YAS-02845: ERR_ANK_DROP_SYS_SEQUENCE

**Message**: cannot drop a system-generated sequence

**Action**: Deleting system-generated sequences is prohibited.

### YAS-02846: ERR_ANK_MODIFY_IDENTITY

**Message**: column to be modified is not an identity column

**Action**: The column to be modified is not an identity column; please ensure that the column to be modified meets the identity column requirements.

### YAS-02847: ERR_ANK_MODIFY_IDENTITY_DEFAULT

**Message**: identity column cannot have a default value

**Action**: Adding a default value to an identity column is prohibited.

### YAS-02848: ERR_ANK_DROP_IDENTITY_CONS

**Message**: cannot drop NOT NULL constraint on an identity column

**Action**: Dropping the NOT NULL constraint on an identity column is prohibited.

### YAS-02849: ERR_ANK_ALTER_SYS_SEQUENCE

**Message**: cannot alter a system-generated sequence

**Action**: Altering system-generated sequences is prohibited.

### YAS-02850: ERR_ANK_KEYSTORE_HSM_ERROR

**Message**: Failed to `%s`, HSM/KMS module error: %s(%lu).

**Action**: An error occurred while using the PKCS #11 module, please investigate the specific cause based on the HSM module error codes.

### YAS-02851: ERR_ANK_KEYSTORE_HSM_EXCEPTION

**Message**: HSM/KMS module exception: %s

**Action**: When using the PKCS #11 module, exceptions external to the module may occur (e.g., unsupported features). Please refer to the error message for details.

### YAS-02852: ERR_ANK_KEYSTORE_CRYPTOKI_VERSION

**Message**: Cryptoki standard(pkcs#11) %u.%u is expected, but version of give module is %u.%u

**Action**: The PKCS #11 module implementation does not meet the required standards.

### YAS-02853: ERR_ANK_KEYSTORE_STORE_OBJ

**Message**: HSM module doesn't support find object by label or store object

**Action**: The current PKCS #11 module cannot perform lookups by label.

### YAS-02854: ERR_ANK_KEYSTORE_PWD_MISMATCH

**Message**: keystore password mismatch

**Action**: During key management operations, the password does not match the one used to open the wallet.

### YAS-02855: ERR_ANK_KEYSTORE_MUST_BACKUP

**Message**: no backup clause specified when using wallet/KMS

**Action**: When performing key management operations, the current wallet must be backed up.

### YAS-02856: ERR_ANK_KMS_URL_INVALID

**Message**: KMS connection string is invalid: %s

**Action**: The password URL format using KMS for key management is incorrect. Please refer to specific instructions or documentation to adjust the password URL.

### YAS-02857: ERR_ANK_KEYSTORE_BACKUP_IN_HSM

**Message**: backup keystore is not allowed when keystore type is HSM

**Action**: Key backup operations cannot be completed using an external PKCS #11 module.

### YAS-02858: ERR_ANK_LOGSTDBY_YSTREAM_ERROR

**Message**: logical replay failure occurred, resulting in standby database promotion failure

**Action**: Query the V$DIAG_INCIDENT view to identify the cause of the failure, and proceed to promote to primary database after resolving the issue.  

### YAS-02859: ERR_ANK_GRANT_SECURITY_PRIVS

**Message**: security privilege or administrator role cannot be granted when the separation of duty is enabled

**Action**: After enabling separation of duties, security-related privileges or administrative roles cannot be granted.  

### YAS-02860: ERR_ANK_FB_RESTORE_TO_MARKER

**Message**: flashback database failed when restore to target marker

**Action**: Full database flashback encountered an error while rolling back the database to the specified snapshot point.

### YAS-02861: ERR_ANK_FB_MOUNT_FAILED

**Message**: mount flashback resource failed on inst %u

**Action**: Instance initialization of flashback resources failed in YAC.

### YAS-02862: ERR_ANK_FB_IS_FORBIDDEN

**Message**: cannot execute flashback database ddl when %s

**Action**: Ensure no other system-level operations are in progress when executing flashback-related DDL operations. 

### YAS-02863: ERR_ANK_EXCHANGE_DSTTAB_MISMATCH

**Message**: ALTER TABLE EXCHANGE requires a non-partitioned table

**Action**: The target table of ALTER TABLE EXCHANGE statement must be a non-partitioned table.

### YAS-02864: ERR_ANK_EXCHANGE_COLCNT_MISMATCH

**Message**: tables in ALTER TABLE EXCHANGE PARTITION must have the same number of columns

**Action**: Ensure the number of columns in the partitioned table and the target table are consistent in ALTER TABLE EXCHANGE PARTITION statement.

### YAS-02865: ERR_ANK_EXCHANGE_COL_MISMATCH

**Message**: column %s mismatch in ALTER TABLE EXCHANGE PARTITION

**Action**: Ensure the column definitions of the partitioned table and the target table are consistent in ALTER TABLE EXCHANGE PARTITION statement.

### YAS-02866: ERR_ANK_EXCHANGE_INDEX_MISMATCH

**Message**: index mismatch for tables in ALTER TABLE EXCHANGE PARTITION

**Action**: Ensure the number and definitions of local indexes in the partitioned table match those of the target table in ALTER TABLE EXCHANGE PARTITION statement.

### YAS-02867: ERR_ANK_EXCHANGE_ROWS_MISMATCH_PART

**Message**: all rows in table do not qualify for specified partition

**Action**: Verify that all data in the target table can be mapped to the partition being exchanged.

### YAS-02868: ERR_ANK_EXCHANGE_CHEKCONS_MISMATCH

**Message**: CHECK constraint mismatch in ALTER TABLE EXCHANGE PARTITION

**Action**: Ensure the number and definitions of CHECK constraints in the partitioned table and the target table are consistent.

### YAS-02869: ERR_ANK_EXCHANGE_FK_MISMATCH

**Message**: FOREIGN KEY constraint mismatch in ALTER TABLE EXCHANGE PARTITION

**Action**: Ensure the number and definitions of foreign key constraints in the partitioned table and the target table are consistent.

### YAS-02870: ERR_ANK_EXCHANGE_PKUK_MISMATCH

**Message**: UNIQUE constraints mismatch in ALTER TABLE EXCHANGE PARTITION

**Action**: Ensure the number and definitions of UNIQUE constraints in the partitioned table and the target table are consistent.

### YAS-02871: ERR_ANK_EXCHANGE_ENCR_MISMATCH

**Message**: encryption properties mismatch

**Action**: Ensure the encryption attribute definitions of the partitioned table and the target table are consistent.

### YAS-02872: ERR_ANK_EXCHANGE_IDNSEQ_MISMATCH

**Message**: identity column sequence mismatch in ALTER TABLE EXCHANGE PARTITION

**Action**: Ensure the identity column definitions of the partitioned table and the target table are consistent.

### YAS-02873: ERR_ANK_INST_NOT_OPEN_DB

**Message**: instance %u is not open

**Action**: Perform operations only after opening the instance to the OPEN stage.

### YAS-02874: ERR_ANK_NOT_SWITCH_REDO_FILE

**Message**: instance %u has no available redo log files

**Action**: Add additional available redo files to meet capacity requirements.

### YAS-02875: ERR_ANK_NOT_OPER_REDO_FILE

**Message**: cannot add or drop redo files for online instances

**Action**: In YAC/Distributed Cluster Deployment, the master instance cannot manage redo files of an online instance; please connect directly to the online instance to operate its redo files.

### YAS-02876: ERR_ANK_INVALID_SO_PRIV

**Message**: only EXECUTE privileges are valid for procedure, function or package

**Action**: For stored procedures, UDFs, and packages, only the EXECUTE privilege is valid.

### YAS-02877: ERR_ANK_FB_IS_CORRUPTED

**Message**: This database instance failed to turn on flashback database

**Action**: Unable to obtain flashback log information, database instance startup failed. When enabling flashback functionality, the database validates flashback log files during startup. Please check and confirm whether the corresponding files exist or if the file-related configurations are correct.

### YAS-02878: ERR_ANK_MODIFY_IDENTITY_CONS

**Message**: cannot modify NOT NULL constraint on an identity column

**Action**: Modifying the NOT NULL constraint of an identity column is prohibited.

### YAS-02879: ERR_ANK_INVALID_SEQ_PRIV

**Message**: only SELECT privileges are valid for sequence

**Action**: Only SELECT permissions are valid for sequences.

### YAS-02880: ERR_ANK_OPERATE_FUNC_INDEX

**Message**: failed to %s func index/virtual column by reference the function, %s

**Action**: Please confirm whether the function or virtual column dependencies satisfy the constraint conditions.

### YAS-02881: ERR_ANK_INDEX_KEY_NOT_FIND

**Message**: index key value is not find

**Action**: The index key value cannot be found. If it is a UDF index, please verify the determinism of the UDF.

### YAS-02882: ERR_ANK_BUILD_LOGSTBY_TIMEOUT

**Message**: lock table timeout when build logic standby

**Action**: A lock is applied to all tables once during logical standby construction. Please check for long-running uncommitted transactions or business operations that hold table locks for extended periods, ensuring no long-running transactions exist during construction and maintaining low business load.

### YAS-02883: ERR_ANK_INVALID_ALTER_TABLE_NAME

**Message**: cannot alter to an object with a name matching private temporary table prefix

**Action**: Cannot rename the current object to a name that includes the private temporary table prefix.

### YAS-02884: ERR_ANK_TIME_INTERVAL_PART

**Message**: cannot use time type as the partition key for interval partitioning

**Action**: Cannot create an interval partition using the time data type.

### YAS-02885: ERR_ANK_YSTREAM_SHRINK_WL

**Message**: adding tables to an empty whitelist causes whitelist shrinkage, which is not allowed while the ystream server is started. please stop the ystream server and try again

**Action**: The parsing objects of the Ystream server are all tables. It is not possible to execute ADD_TABLES to narrow the scope of parsing objects while the Ystream server is running. Please stop the Ystream server and try again.

<span id="errno3001" name="errno3001"></span>

### YAS-03001:ERR_VLD_OUT_COLUMN_NOT_FOUND

**Message**: output column of access constraint is missing

**Action**: Please add the y value column according to the [CREATE ACCESS CONSTRAINT](../Development Guide/SQL Reference Manual/SQL Statements/CREATE ACCESS CONSTRAINT) syntax diagram.

### YAS-03002:ERR_VLD_TABLE_TYPE_NOT_SUPPORT

**Message**: access constraint can only be created on table

**Action**: AC can only be created on LSC tables.

### YAS-03003:ERR_VLD_COLUMN_ALREADY_EXIST

**Message**: such column list already has access constraint

**Action**: Specify different columns, aggregate functions, or filter conditions and retry.

### YAS-03004:ERR_VLD_AC_COLUMNS_EXCEED

**Message**: maximum number of access constraint columns is %u

**Action**: Reduce the number of columns in the AC.

### YAS-03005:ERR_VLD_BUILDER_FAILED

**Message**: failed to ac build, ac id is % PRIu64 

**Action**: Conversion failed; please contact our technical support for resolution.

### YAS-03006:ERR_VLD_COL_TYPE_NOT_SUPPORT

**Message**: cannot create access constraint on expression with %s datatype

**Action**: AC currently does not support this data type.

### YAS-03009:ERR_VLD_NEED_DROP_AC

**Message**: table is referenced by some access constraints

**Action**: You must DROP AC first before dropping the table.

### YAS-03010:ERR_VLD_CREATED_IN_INVALID_SPACE

**Message**: cannot create access constraint from LSC table in a tablespace without data bucket

**Action**: Please create DATA BUCKET for the tablespace, then retry.

### YAS-03013:ERR_VLD_ALTER_TABLE_COLUMN

**Message**: cannot alter columns related to access constraint

**Action**: Drop AC before modifying the column attributes.

### YAS-03014:ERR_VLD_BUILD_AC_NOT_ENABLED

**Message**: build ac is disabled

**Action**: Enable the build ac feature using the ALTER TABLE statement, refer to the [enable_xfmr_clause](../Development Guide/SQL Reference Manual/SQL Statements/ALTER TABLE.md#enablexfmrclause) in the ALTER TABLE documentation.

### YAS-03015:ERR_VLD_TABLE_DEF_CHANGE

**Message**: table definition changed during the creation of access constraint

**Action**: Please retry creating the AC as the table definition has changed.

### YAS-03016:ERR_VLD_DROP_SPACE

**Message**: table %s in the tablespace is used by access constraint %s of other tablespace

**Action**: Before making the above operation, please delete the associated AC first.

### YAS-03201:ERR_DATASET_OVERFLOW

**Message**: dataset is overflow, capacity %u, rows %u

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03202:ERR_DATASET_COL_IS_READONLY

**Message**: column %u is read-only

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03203:ERR_DATASET_COL_NOT_FOUND

**Message**: column %u is not found in dataset

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03204:ERR_DATASET_COL_ID_INVALID

**Message**: column id is invalid

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03205:ERR_DATASET_TYPE_MISMATCH

**Message**: type mismatch for column %d , expect %s type, actual %s type

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03206:ERR_DATASET_OFFSET_NOT_ZERO

**Message**: first offset for var column %d is not zero, actual %u

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03207:ERR_DATASET_VAR_COL_INVALID

**Message**: column %u value '%s' is invalid, expected size not more than %u, actual size is %u

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03208:ERR_DATASET_COl_NOT_NULLABLE

**Message**: column %u is not nullable, but try to set null value

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03209:ERR_DATASET_SLICE_NOT_EMPTY

**Message**: column slice is not empty, op is not allowed

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03210:ERR_DATASET_CAN_NOT_ATTACH

**Message**: build column %u cannot be attached

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03211:ERR_DATASET_IS_NOT_ALIGNED

**Message**: column %u slice size is %u, other slice size %u

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03212:ERR_DATASET_IS_NOT_MOVABLE

**Message**: column %d is attached, cannot move

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03213:ERR_DATASET_SLICE_IS_EMPTY

**Message**: column %u slice is empty

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03214:ERR_DATASET_ROW_NOT_FOUND

**Message**: row (%u, %u) not found for dataset

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03215:ERR_DATASET_COL_SIZE_INVALID

**Message**: column %u size is %u, value size is %u

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03216:ERR_DATASET_DATA_CONFLICT

**Message**: slice does not have data, but some rows is not null

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03217:ERR_DATASET_COL_COUNT_MISMATCH

**Message**: destination dataset column count %u does not match with source dataset %u

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03219:ERR_DATASET_SORT_COL_OVERFLOW

**Message**: dataset has columns %u, but sort column num %u which is overflow

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03220:ERR_DATASET_SORT_COL_INVALID

**Message**: dataset invalid sort column id %u, max valid columnId is %u

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03221:ERR_DATASET_SORT_BUF_NOT_ENOUGH

**Message**: dataset has %u rows, but sort buffer only has %u rows which is not enough

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03222:ERR_DATASET_ALLOC_SIZE_OVERFLOW

**Message**: dataset need to allocate % PRIu64  bytes of memory, which overflow 4GB

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03223:ERR_DATASET_INVALID_CAPACITY

**Message**: invalid capacity of dataset

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03224:ERR_DATASET_INVALID_STATUS

**Message**: invalid status %u of dataset

**Action**: Internal error; please contact our technical support for resolution.

### YAS-03237:ERR_SWF_COLUMN_TOO_LARGE

**Message**: column size %u is too large

**Action**: Please refer to product specifications-data type specifications document to ensure the data is within the specification range before retrying.

### YAS-03242:ERR_SWF_UNEXPECTED_BLOCK

**Message**: block {file: %u, id: %u} type expected to be %u, but type is %u now

**Action**: The object has been truncated or deleted; please retry.

### YAS-03243:ERR_SWF_COL_TYPE_NOT_SUPPORT

**Message**: TAC table having column with invalid datatype "%s"

**Action**: TAC table currently does not support storing this type of data.

### YAS-03244:ERR_SWF_ENTRY_BLOCK_NUM_EXCEED

**Message**: maximum number of entry block is %u

**Action**: Reduce the amount of data in the table.

### YAS-03247: ERR_SWF_READ_UNEXTEND_BLOCK

**Message**: read unextend block

**Action**: Reading an unextended block, please contact our technical support for resolution.

### YAS-03248:ERR_SWF_DICTIONARY_FULL

**Message**: column %s dictionary is full

**Action**: The dictionary item has reached its limit; only existing values can be inserted into the column.

### YAS-03249:ERR_SWF_META_CHANGED

**Message**: MCol metadata changed

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03402:ERR_COAST_HEAP_ALLOC_MEMORY

**Message**: cannot allocate % PRId64  bytes from heap allocator

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03403:ERR_COAST_STACK_OVERFLOW

**Message**: stack overflow, try to allocate %d bytes

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03409:ERR_COAST_DECOMPRESS_SIZE

**Message**: decompress size %d does not match expected size %d, compression type %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03410:ERR_COAST_CACHE_ALLOC

**Message**: cannot allocate object from scol data buffer, max object count is %u, %s allocator is not enough

**Action**: Please adjust the size of the corresponding allocator as described.

### YAS-03413:ERR_COAST_DATA_CORRUPT

**Message**: data corrupt: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03414:ERR_COAST_CTRL_FORCE_FREEING

**Message**: failed to wait for object to load

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03415:ERR_COAST_COMP_TYPE_INVALID

**Message**: compression type %u is invalid

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03418:ERR_COAST_DATA_BUCKET_OFFLINE

**Message**: data bucket %s is offline

**Action**: The data bucket in use is offline; please contact our technical support for resolution.

### YAS-03419:ERR_COAST_FATAL_ERROR

**Message**: coast fatal error, operation is %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03420:ERR_COAST_BUCKET_IS_FULL

**Message**: data bucket %s is full

**Action**: The data bucket is full; please increase the provision for a new data bucket. If this error occurs when other data buckets have sufficient space, it means the reserved space of the data bucket cannot support this import. Consider increasing BUCKET_RESERVED_SPACE to allow the import to select other data buckets with more remaining space.

### YAS-03421:ERR_COAST_SERDE_FILE_CORRUPT

**Message**: serialize file corrupt, %s

**Action**: The temporary swap file for SCOL is corrupted; please clean up files with the SCOL_ prefix in the $YASDB_DATA/tmp directory and try again.

### YAS-03451:ERR_DISKCACHE_VER_INCOMPATIBLE

**Message**: diskcache version incompatible, metadata version %u, current version %u

**Action**: Internal error; the disk buffer will automatically recover, please ignore.

### YAS-03452:ERR_DISKCACHE_FAILED_READ_DATA

**Message**: diskcache failed to read data: %s

**Action**: The disk buffer cannot retrieve data, which does not affect query results; please ignore.

### YAS-03453:ERR_DISKCACHE_OUT_OF_SPACE

**Message**: diskcache has run out of space, requested: % PRId64 , capacity: % PRId64

**Action**: The disk buffer has reached the maximum space limit; this limit can be adjusted by modifying the DISKCACHE_CAPACITY parameter.

### YAS-03454:ERR_DISKCACHE_INVALID_DIRCTORY

**Message**: diskcache directory is in an invalid state: %s for %s

**Action**: The disk buffer directory is in an unusual state; please check this folder or modify DISKCACHE_ROOT to a different folder.
- not a directory: This path is not a folder.
- unowned directory: This path is not a folder governed by the disk buffer.

<span id="errno3501" name="errno3501"></span>

### YAS-03504:ERR_CRL_SORTED_NOT_INIT

**Message**: only LSC can be sorted

**Action**: Only columns of LSC tables can be sorted.

### YAS-03505:ERR_CRL_CALLBACK_NOT_REGISTER

**Message**: LSC table "%s" accessor is not registered, please make sure external lib "libyas_col_executor.so" already dlopen

**Action**: Check if the library libyas_col_executor.so is installed in the current environment.

### YAS-03508:ERR_CRL_COL_TYPE_NOT_SUPPORT

**Message**: LSC table do not support "%s" data type

**Action**: The LSC table currently does not support storing this type of data.

### YAS-03601:ERR_SWD_ITEM_NOT_FOUND

**Message**: meta item not found

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03703:ERR_SPF_SLICE_NOT_EXIST

**Message**: slice not found

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03708:ERR_SPF_EXECUTE_XFMR_FAILED

**Message**: failed to execute transformer task, task number is % PRIu64

**Action**: Forced conversion failed, please contact our technical support for resolution.

### YAS-03710:ERR_SPF_COLUMN_CODEC_INVALID

**Message**: column %s's codec type is invalid

**Action**: Use a codec type supported by this data type.

### YAS-03711:ERR_SPF_CREATED_IN_INVALID_SPACE

**Message**: can not create LSC table in a tablespace without data bucket

**Action**: Retry after creating DATA BUCKET for the tablespace.

### YAS-03714:ERR_SPF_DELTABUFFER_INSERT_FAILED

**Message**: failed to bulkload because of backend convert failed, errorCode is %d, errorMessage is %s

**Action**: Modify according to the error prompt.

### YAS-03717:ERR_SPF_COAST_SLICE_NOT_VALID

**Message**: %s is not a valid slice file: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03718:ERR_SPF_INTERNAL_ERROR

**Message**: lsc internal error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03719:ERR_SPF_VGD_SCAN_ERROR

**Message**: object data has been transformed or object has been truncated or dropped

**Action**: Scan data has been transformed or scan object has been truncated or dropped, please retry.

### YAS-03720:ERR_SPF_TRANSFORM_NOT_ENABLED

**Message**: transform data is disabled

**Action**: Enable the data transformation switch using the ALTER TABLE statement, refer to the enable_xfmr_clause in the ALTER TABLE documentation.

### YAS-03722:ERR_SPF_XFMR_INTERNAL_ERROR

**Message**: xfmr internal error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03723:ERR_SPF_XFMR_CANCEL

**Message**: current xfmr has been canceled

**Action**: Internal error, please contact our technical support for resolution.

### YAS-03724:ERR_SPF_COMPACT_NOT_ENABLED

**Message**: compact data is disabled

**Action**: Enable the data merging switch using the ALTER TABLE statement, refer to the enable_xfmr_clause in the ALTER TABLE documentation.

### YAS-03725:ERR_SPF_LOCK_SLICE_FAILED

**Message**: lock slice %s failed

**Action**: Failed to lock slice, please retry.

### YAS-03726:ERR_SPF_DROP_ORDER_COLUMNS

**Message**: cannot drop order key column

**Action**: Cannot drop the column of the order key.

### YAS-03728: ERR_SPF_BULKLOAD_CHECK

**Message**: action not allowed : %s

**Action**: Memory-related error, please adjust according to the error message and retry.

### YAS-03730: ERR_SPF_BULKLOAD_FAILED

**Message**: append to memory slice failed, the whole transaction has been rolled back

**Action**: Insertion of cold data failed and rolled back the entire transaction.

### YAS-03731: ERR_SPF_DISABLE_MCOL_FAILED

**Message**: disable mcol failed

**Action**: Failure due to other operations affecting the closure of the MCOL module, please try again after pausing DML operations.

### YAS-03732: ERR_SPF_DELTA_BUFFER_EXTEND_FULL

**Message**: SCOL delta buffer count reach the upper limit

**Action**: Please try reducing the data volume of a single transaction.

### YAS-03733: ERR_SPF_TRANSFORM_WAIT_QUOTA_TIMEOUT

**Message**: transform wait alloc quota timeout

**Action**: Please try to reduce the amount of data per transaction or the transaction concurrency level.

<span id="errno4001" name="errno4001"></span>

### YAS-04001:ERR_ANS_SQLPOOL_BUF_TOO_SMALL

**Message**: sql pool buffer too small

**Action**: Adjust the size of SHARE_POOL_SIZE.

### YAS-04002:ERR_ANS_SQL_TEXT_TOO_LONG

**Message**: sql text too long

**Action**: SQL statement length exceeds limit, modify the statement to be less than the length defined in product specifications - logical specifications.

### YAS-04003:ERR_ANS_TOO_MANY_OPEN_CURSORS

**Message**: maximum number of open cursors is %d

**Action**: Too many cursors opened simultaneously, please refer to the OPEN_CURSORS configuration item in the [Configuration Parameters](Configuration Parameters) document and adjust the configuration value as resources allow.

### YAS-04004:ERR_ANS_STMT_ALLOC

**Message**: failed to allocate statement

**Action**: Statement allocation failure for SQL execution, please check for excessive connections.

### YAS-04005:ERR_ANS_FETCH_NOT_SUPPORTED

**Message**: fetch not supported

**Action**: This fetch method is not supported at the moment.

### YAS-04006:ERR_ANS_NULL_VALUE

**Message**: cannot insert NULL value to column %s

**Action**: Cannot insert NULL value into this column.

### YAS-04007:ERR_ANS_RS_METADATA_CHANGED

**Message**: result set metadata changed

**Action**: The result set metadata has changed. Common handling methods are as follows:

- If connected via JDBC or C driver, the related Prepare interface of DML may be expired, please regenerate it.
- If not connected via driver, re-execute the DML statement.
- If the above is ineffective, please contact our technical support for resolution.

### YAS-04008:ERR_ANS_VARIANT_SIZE_TOO_LARGE

**Message**: %s size exceeding limit % PRIu32

**Action**: There is an upper limit for data length processing in the scenario indicated by the error message. Adjust the SQL statement according to the specific scenario and try again:

- When "material row buffer" is prompted: There is an upper limit specification for the storage of a single row in the materialization operator ([Row Specification of Materialized Area](../Product Overview/Specifications/Logical Specifications.md#Syntax)). Please adjust the statement plan or reduce unnecessary projection columns.

- When "variant" is prompted: The input parameter value exceeds the maximum value allowed by the variable. Please reduce the parameter value.

- When "bind param count" is prompted:The number of binding parameters in the statement exceeds the upper limit of 32,000. Please reduce the number of binding parameters.

- When the table column name is prompted: The inserted data exceeds the table column definition.

If the problem still cannot be solved, please contact our technical support for a solution.

### YAS-04009:ERR_ANS_BUF_NOT_ENOUGH

**Message**: buffer is not enough

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04010:ERR_ANS_STMT_EOF

**Message**: statement is eof

**Action**: (1) Check if operations were continued after all cursor data was fetched. (2) Check if the cursor is valid, such as executing fetch on a cursor that returns no data.

### YAS-04011:ERR_ANS_MEM_GUARD

**Message**: plan cache internal error

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04012:ERR_ANS_EXE_NOT_SUPPORTED

**Message**: %s does not support batch execution

**Action**: Batch execution is not currently supported.

### YAS-04013:ERR_ANS_PDEBUG_NOT_START

**Message**: PL/SQL debugger does not start

**Action**: Please start the PL debugger first.

### YAS-04014:ERR_ANS_PDEBUG_ILL_TRAP

**Message**: illegal trapper protocol in PL/SQL debugger

**Action**: Debugger protocol error, please contact our technical support for resolution.

### YAS-04015:ERR_ANS_PDEBUG_BACKTRACE

**Message**: at "%s", line %d

**Action**: PL execution exception, please check the exception point.

### YAS-04101:ERR_ANS_LEXER_STACK_OVERFLOW

**Message**: sql too complex

**Action**: SQL statement is too complex, please simplify the SQL statement. Check if the SQL statement length exceeds 2M, the number of nested parentheses exceeds the limit of 128 layers, and if the number of PX SENDER PLANs exceeds the limit of 128.

### YAS-04102:ERR_ANS_LEXER_INVALID_SYMBOL

**Message**: invalid symbol %c

**Action**: Illegal symbol, please enter a valid symbol.

### YAS-04103:ERR_ANS_LEXER_INCOMPLETE_BRACKET

**Message**: incomplete bracket

**Action**: Incomplete bracket, please enter a complete bracket.

### YAS-04104:ERR_ANS_LEXER_INCOMPLETE_OPERATOR

**Message**: incomplete operator

**Action**: Incomplete operator, please enter a complete operator.

### YAS-04105:ERR_ANS_LEXER_INVALID_NUMBER

**Message**: invalid number character %s

**Action**: Illegal number, please enter a valid number.

### YAS-04106:ERR_ANS_LEXER_INCOMPLETE_STRING

**Message**: incomplete string

**Action**: Incomplete string, please enter a complete string.

### YAS-04107:ERR_ANS_LEXER_STRING_TOO_LONG

**Message**: string exceeding limit % PRIu64

**Action**: String length exceeds the allowed maximum length, please reduce the string length.

### YAS-04108:ERR_ANS_LEXER_NAME_TOO_LONG

**Message**: name exceeding limit % PRIu32

**Action**: Name length exceeds the allowed maximum length, please reduce the name length.

### YAS-04109:ERR_ANS_LEXER_TOO_MANY_NAME_MEMBERS

**Message**: name exceeding member limit % PRIu32

**Action**: Name members exceed the allowed maximum number, please reduce the number of name members.

### YAS-04110:ERR_ANS_LEXER_VARIANT_NAME_INVALID

**Message**: invalid variant name

**Action**: Illegal variable name, please enter a valid variable name.

### YAS-04111:ERR_ANS_LEXER_KEY_WORD_IN_NAME

**Message**: key word in name

**Action**: The name contains a keyword, please modify the name.

### YAS-04112:ERR_ANS_LEXER_INVALID_COMMENT

**Message**: invalid comment block text

**Action**: Illegal block comment, please enter a valid one.

### YAS-04113:ERR_ANS_LEXER_BRACKET_EXPECTED

**Message**: bracket expected

**Action**: Missing bracket, please enter a bracket.

### YAS-04114:ERR_ANS_LEXER_DATA_TYPE_EXPECTED

**Message**: need to specify the datatype

**Action**: Missing data type, please enter a valid data type.

### YAS-04115:ERR_ANS_LEXER_WORD_EXPECTED

**Message**: "%s" expected but missing

**Action**: Missing keyword, please enter the missing keyword.

### YAS-04116:ERR_ANS_LEXER_NUMBER_EXPECTED

**Message**: number expected

**Action**: Missing number, please enter a number.

### YAS-04117:ERR_ANS_LEXER_PASSWORD_EXPECTED

**Message**: password text expected

**Action**: Missing password, please enter the password.

### YAS-04118:ERR_ANS_LEXER_SIZE_EXPECTED

**Message**: size expected

**Action**: Missing size, please enter the size.

### YAS-04119:ERR_ANS_LEXER_STRING_EXPECTED

**Message**: string expected

**Action**: Missing string, please enter the string.

### YAS-04120:ERR_ANS_LEXER_NOT_ALLOW_SPECIFY_COL

**Message**: may not specify column datatypes in this CREATE TABLE

**Action**: Data types for columns must be specified during table creation.

### YAS-04121:ERR_ANS_LEXER_INVALID_DATATYPE

**Message**: invalid datatype

**Action**: Illegal data type, please enter a valid data type.

### YAS-04128:ERR_ANS_LEXER_INTEGER_EXPECTED

**Message**: integer value required

**Action**: Please enter an integer data type.

### YAS-04129:ERR_ANS_LEXER_MISSING_DOUBLE_QUOTE

**Message**: missing double quote in identifier

**Action**: Please check the use of double quotes for validity.

### YAS-04151:ERR_DPH_TOO_MANY_CONNECTIONS

**Message**: remote instance too many connections

**Action**: Too many connections, please disconnect some connections and then retry.

### YAS-04152:ERR_DPH_TOO_MANY_NODES

**Message**: remote instance too many nodes

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04153:ERR_DPH_INVALID_COMMAND

**Message**: remote instance invalid command: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04154:ERR_DPH_INVALID_NODE

**Message**: remote instance invalid node: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04155:ERR_DPH_SOCKET_TIMEOUT

**Message**: %s timeout

**Action**: Network timeout, please retry.

### YAS-04156:ERR_DPH_CONNECTION_EXPIRED

**Message**: remote instance connection expired

**Action**: Cluster connection expired, please retry.

### YAS-04201:ERR_ANS_PARSER_CTE_COLUMN_MISMATCH

**Message**: number of WITH clause column names does not match number of elements

**Action**: Please ensure the number of column names in the WITH clause matches the actual number of columns.

### YAS-04202:ERR_ANS_PARSER_INVALID_TABLE_NAME

**Message**:  missing or invalid table name

**Action**: Missing table name, please enter the table name.

### YAS-04203:ERR_ANS_PARSER_CLAUSE_CONFLICT

**Message**: clause "%s" is declared or conflict

**Action**: Please check if the clause is defined redundantly.

### YAS-04204:ERR_ANS_PARSER_NUMBER_RANGE

**Message**: number of %s must be between % PRId64  and % PRId64

**Action**: Please input a valid number within the specified range as prompted.

### YAS-04205:ERR_ANS_PARSER_EXPLAIN_DML_EXPECTED

**Message**: EXPLAIN missing DML keyword

**Action**: Please input a DML clause in the EXPLAIN statement.

### YAS-04206:ERR_ANS_PARSER_INVALID_SIZE

**Message**: invalid size value

**Action**: Illegal size value, please enter a valid size value.

### YAS-04207:ERR_ANS_PARSER_INVALID_INTEGER

**Message**: invalid integer value

**Action**: Illegal integer, please enter a valid integer.

### YAS-04208:ERR_ANS_PARSER_SUBQUERY_EXPECTED

**Message**: subquery expected

**Action**: Missing subquery, please enter a subquery.

### YAS-04209:ERR_ANS_PARSER_UNEXPECTED_WORD

**Message**: unexpected word %s

**Action**: Illegal word, please check the SQL statement.

### YAS-04210:ERR_ANS_PARSER_INVALID_FILE_NAME

**Message**: invalid file name

**Action**: Illegal file name, please enter a valid file name.

### YAS-04211:ERR_ANS_PARSER_INVALID_CHARSET_NAME

**Message**: invalid charset name

**Action**: Illegal charset name, please enter a valid charset name GBK/UTF8/ASCII/ISO88591.

### YAS-04212:ERR_ANS_PARSER_INVALID_OPEN_MODE

**Message**: invalid open mode

**Action**: Illegal database open mode, please enter a valid database open mode.

### YAS-04213:ERR_ANS_PARSER_EXPRESSION_EXPECTED

**Message**: expression expected

**Action**: Missing expression, please enter the expression.

### YAS-04214:ERR_ANS_PARSER_INCOMPLETE_EXPRESSION

**Message**: incomplete expression

**Action**: Please enter a complete expression.

### YAS-04215:ERR_ANS_PARSER_COMPARISON_EXPECTED

**Message**: comparison expected

**Action**: Missing comparison operator, please enter the comparison operator.

### YAS-04216:ERR_ANS_PARSER_INCOMPLETE_COMPARISON

**Message**: incomplete comparison

**Action**: Please enter the complete comparison operator.

### YAS-04217:ERR_ANS_PARSER_CONDITION_EXPECTED

**Message**: condition expected

**Action**: Missing condition, please enter the condition clause.

### YAS-04218:ERR_ANS_PARSER_INCOMPLETE_CONDITION

**Message**: incomplete condition

**Action**: Please enter the complete condition clause.

### YAS-04219:ERR_ANS_PARSER_UNEXPECTED_SUBQUERY

**Message**: unexpected subquery

**Action**: Please check if the input statement meets the syntax tree requirements, there is an illegal subquery.

### YAS-04220:ERR_ANS_PARSER_INCOMPLETE_SELECT

**Message**: incomplete select

**Action**: Please enter the complete query statement.

### YAS-04221:ERR_ANS_PARSER_INVALID_TABLE_ALIAS

**Message**: invalid table alias

**Action**: Illegal table alias, please enter a valid table alias.

### YAS-04222:ERR_ANS_PARSER_ALTER_TABLE_EXPECTED

**Message**: alter table expected

**Action**: Missing alter table, please enter alter table.

### YAS-04223:ERR_ANS_PARSER_DUPLICATED_CLAUSE

**Message**: duplicate clause for %s

**Action**: Please remove the duplicate clause.

### YAS-04224:ERR_ANS_PARSER_UNEXPECTED_EXTENT

**Message**: extent clause is not supported for undo and swap tablespace

**Action**: Please remove the extent clause.

### YAS-04225:ERR_ANS_PARSER_INVALID_WORD

**Message**: invalid word %s

**Action**: Please remove the word.

### YAS-04226:ERR_ANS_PARSER_TOO_MANY_TABLES

**Message**: too many tables or views

**Action**: Please remove the excess tables or views.

### YAS-04227:ERR_ANS_PARSER_TOO_MANY_SELECT

**Message**: too many subquery or select

**Action**: Please remove the excess subqueries or selects.

### YAS-04228:ERR_ANS_PARSER_UNEXPECTED_EOF

**Message**: unexpected eof

**Action**: Please check if the SQL statement is complete.

### YAS-04229:ERR_ANS_PARSER_INVALID_DATA_TYPE

**Message**: invalid datatype

**Action**: Please enter a valid data type.

### YAS-04230:ERR_ANS_PARRSR_INVALID_PART_CNT

**Message**: invalid number of partitions specified in PARTITIONS clause

**Action**: Please specify the partition count as a value between 1 and (1M-1).

### YAS-04231:ERR_ANS_PARSER_KEYWORD_EXPECTED

**Message**: keyword expected

**Action**: Missing keyword, please enter the keyword.

### YAS-04232:ERR_ANS_PARSER_DUPLICATED_COLUMN

**Message**: duplicate column name

**Action**: Please enter a non-repeating column name.

### YAS-04233:ERR_ANS_PARSER_DUPLICATED_PART

**Message**: duplicate partition/subpartition name

**Action**: Please enter a non-repeating partition name.

### YAS-04234:ERR_ANS_PARSER_PART_COL_NONEXIST

**Message**: non-existent partitioning key column

**Action**: Please enter a valid partition key column.

### YAS-04235:ERR_ANS_PARSER_TOO_MANY_PART_COL

**Message**: maximum number of partitioning key columns is %d

**Action**: The number of partition key columns has exceeded the maximum limit, please reduce the number of partition key columns.

### YAS-04236:ERR_ANS_PARSER_TOO_MANY_BOUND_ELE

**Message**: partition bound list contains too many elements

**Action**: Please enter the correct number of partition boundary columns.

### YAS-04237:ERR_ANS_PARSER_TOO_FEW_BOUND_ELE

**Message**: partition bound list contains too few elements

**Action**: Please enter the correct number of partition boundary columns.

### YAS-04238:ERR_ANS_PARSER_INVALID_BOUND_ELE

**Message**: partition bound element must be one of %s

**Action**: Please enter a valid partition boundary value.

### YAS-04239:ERR_ANS_PARSER_DEFAULT_LIST

**Message**: DEFAULT partition must be the last partition specified

**Action**: Please declare the DEFAULT partition last.

### YAS-04240:ERR_ANS_PARSER_BOUND_TOO_HIGH

**Message**: partition bound is too high

**Action**: Please enter a valid partition boundary.

### YAS-04241:ERR_ANS_PARSER_DUPLICATED_BOUND

**Message**: list value specified twice in partitions %s, %s

**Action**: Please enter non-repeating boundaries.

### YAS-04242:ERR_ANS_PARSER_INVALID_PART_COL_TYPE

**Message**: a %s column cannot serve as a partitioning column

**Action**: Please enter a valid partition column.

### YAS-04243:ERR_ANS_INVALID_IDENTIFIER

**Message**: invalid identifier %s

**Action**: Please enter a valid identifier.

### YAS-04244:ERR_ANS_PARSER_PARTBOUND_EXCEED

**Message**: maximum number of part bound text size is %u

**Action**: Please reduce the text size of the partition boundary.

### YAS-04245:ERR_ANS_PARSER_INVALID_INTERVAL

**Message**: invalid interval field

**Action**: Please enter a valid interval field.

### YAS-04246:ERR_ANS_PARSER_INCOMPLETE_JOIN

**Message**: incomplete join

**Action**: Please enter a complete join clause.

### YAS-04247:ERR_ANS_PARSER_INVALID_TABLE_TYPE

**Message**: invalid table type

**Action**: Please modify to a table type recognized by YashanDB: HEAP/TAC/LSC.

### YAS-04248:ERR_ANS_PARSER_UNEXPECTED_FOR_UPDATE

**Message**: unexpected for update

**Action**: Please check if the input for update clause is valid.

### YAS-04249:ERR_ANS_PARSER_AC_OUT_COLUMN_EXIST

**Message**: Ac outColumn exists in inColumn

**Action**: Please ensure Ac outColumn is not in inColumn.

### YAS-04250:ERR_ANS_PARSER_UNEXPECTED_SYMBOL

**Message**: unexpected symbol %c

**Action**: Please check if the symbol is valid.

### YAS-04251:ERR_ANS_PARSER_WORD_EXPECTED

**Message**: %s expected

**Action**: Missing keyword, please enter the keyword.

### YAS-04252:ERR_ANS_PARSER_DUPLICATED_VARIABLE

**Message**: duplicate field in record, argument list, variable list or exception

**Action**: Please enter non-repeating variables.

### YAS-04253:ERR_ANS_PARSER_PLSQL_COMPILING

**Message**: PL/SQL compiling errors:

**Action**: Please check the syntax errors in the PL statement and re-enter.

### YAS-04254:ERR_ANS_PARSER_BLOCKSTACK_OVERFLOW

**Message**: plsql block stack overflow

**Action**: PL nesting level too deep, please simplify the PL statement.

### YAS-04255:ERR_ANS_PARSER_TOO_MANY_LINES

**Message**: too many plsql lines

**Action**: Please reduce the number of lines in the PL statement.

### YAS-04256:ERR_ANS_PARSER_INVALID_INDEX_NAME

**Message**: missing or invalid index name

**Action**: Please enter a valid index name.

### YAS-04257:ERR_ANS_PARSER_INVALID_SYNONYM_NAME

**Message**: missing or invalid synonym identifier

**Action**: Please enter a valid synonym name.

### YAS-04258:ERR_ANS_PARSER_INVALID_VIEW_NAME

**Message**: missing or invalid view name

**Action**: Please enter a valid view name.

### YAS-04259:ERR_ANS_PARSER_INVALID_SEQUENCE_NAME

**Message**: missing or invalid sequence name

**Action**: Please enter a valid Sequence name.

### YAS-04260:ERR_ANS_PARSER_INVALID_AC_NAME

**Message**: missing or invalid access constraint name

**Action**: Please enter a valid AC constraint name.

### YAS-04261:ERR_ANS_PARSER_INVALID_PASSWORD

**Message**: invalid password: %s

**Action**: Please enter a valid password.

### YAS-04262:ERR_ANS_PARSER_INTERVAL_EXPR

**Message**: interval expression is not a constant of the correct type

**Action**: Please ensure the interval is specified with a constant value, and it matches the data type of the partition column.

### YAS-04263:ERR_ANS_PARSER_INTERVAL_KEY_CNT

**Message**: range partitioned table with INTERVAL clause has more than one column

**Action**: Check the SQL statement to ensure the interval partition column specifies only one column.

### YAS-04264:ERR_ANS_PARSER_INTERVAL_KEY_TYPE

**Message**: invalid data type for partitioning column of an interval partitioned table

**Action**: Please ensure that the partition column of the interval partitioned table is of numeric or date type.

### YAS-04265:ERR_ANS_PARSER_INTERVAL_MAXVALUE

**Message**: MAXVALUE cannot be specified for an interval partition table

**Action**: MAXVALUE is not allowed for interval partitioned tables.

### YAS-04266:ERR_ANS_PARSER_INVALID_DEFAULT_DIST

**Message**:  no valid column was found as the default partition column

**Action**: Please modify the table definition to ensure at least one suitable data type column can serve as a partition key.

### YAS-04267:ERR_ANS_PARSER_COLUMN_NAME_EXPECTED

**Message**: column name expected

**Action**: Missing column name, please enter a column name.

### YAS-04268:ERR_ANS_PARSER_INVALID_COMMENT_OBJ

**Message**: invalid object category for comment

**Action**: Please enter a valid comment type.

### YAS-04269:ERR_ANS_PARSER_COMMENT_LEN

**Message**: comment length exceeds limit %d

**Action**: Please reduce the length of the comment as prompted.

### YAS-04270:ERR_ANS_PARSER_INTERVAL_OUT_RANGE

**Message**: interval precision is out of range

**Action**: Please provide a valid interval precision.

### YAS-04271:ERR_ANS_PARSER_TOO_LARGE_EXTENT

**Message**: uniform size value must be less than file size

**Action**: Please enter a valid extent size.

### YAS-04272:ERR_ANS_PARSER_INVALID_CTRLFILE_NUM

**Message**: the number of control files specified must be between %u and %u

**Action**: Please configure the number of control files reasonably.

### YAS-04273:ERR_ANS_PARSER_INVALID_CLAUSE

**Message**: invalid clause for %s

**Action**: Please enter a valid clause.

### YAS-04274:ERR_ANS_PARSER_INTERVAL_NOT_POSITIVE

**Message**: the interval must be more than zero

**Action**: Please ensure the entered interval value is greater than 0.

### YAS-04275:ERR_ANS_PARSER_INVALID_DATE_STRING

**Message**: invalid date string

**Action**: Please enter a valid date string.

### YAS-04276:ERR_ANS_PARSER_PRAGMA_VAR_ILLEGAL

**Message**: the compile indication %s cannot be specified here

**Action**: Please place the parameter variable in a valid position.

### YAS-04277:ERR_ANS_PARSER_MISUSED_COMPARATOR

**Message**: this operator cannot be used with lists

**Action**: Please do not use lists within this operator.

### YAS-04278:ERR_ANS_PARSER_MULTI_FB_BEFORE

**Message**: unable to flashback to before drop or truncate for multiple tables

**Action**: Please do not use flashback to before drop or truncate on multiple tables.

### YAS-04279:ERR_ANS_PARSER_FLASHBACK_PART_DROP

**Message**: table partition cannot flashback to before drop

**Action**: Please do not use flashback to before drop on table partitions.

### YAS-04280:ERR_ANS_PARSER_TOO_MANY_DIST_COL

**Message**: maximum number of partitioned columns is %d

**Action**: Please reduce the number of partitioned columns as prompted.

### YAS-04281:ERR_ANS_PARSER_DIST_COL_NONEXIST

**Message**: non-existent partitioned column

**Action**: Please enter a valid partition column.

### YAS-04282:ERR_ANS_PARSER_INVALID_DIST_COL_TYPE

**Message**: a %s column cannot serve as a partition column

**Action**: The column of this data type cannot be used as a partition key, please modify the column type or re-select another column.

### YAS-04283:ERR_ANS_PARSER_CHUNK_BOUND_EXCEED

**Message**: maximum number of chunk bound text size is %u

**Action**: Please reduce the size of the chunk bound text.

### YAS-04284:ERR_ANS_PARSER_INVALID_SEQUENCE_NUM

**Message**: invalid sequence number of archive log or redo file

**Action**: Please enter a valid number of Sequences, the number of Sequences must be greater than 0.

### YAS-04285:ERR_ANS_PARSER_DIST_INVALID

**Message**: duplicate table cannot be distributed

**Action**: Please do not specify distributed for duplicated tables.

### YAS-04286:ERR_ANS_PARSER_SEPARATOR_EXPECTED

**Message**: constant string separator expected

**Action**: Modify the separator to a constant string.

### YAS-04287:ERR_ANS_PARSER_SPF_MULTI_DEFINITION

**Message**: multiple definition sort parameter

**Action**: Please delete redundant sort parameter definitions.

### YAS-04288:ERR_ANS_PARSER_CRICULAL_VIEW

**Message**: circular view definition encountered

**Action**: The base table or referenced view of the view has been deleted, please pay attention to the order of deletions.

### YAS-04289:ERR_ANS_PARSER_INVALID_PWD_STRING

**Message**: invalid password string

**Action**: Illegal password, please re-enter.

### YAS-04290:ERR_ANS_PARSER_TOO_MANY_EXPRS

**Message**: too many %s expressions

**Action**: Too many expressions, please check the number of expressions.

### YAS-04291:ERR_ANS_PARSER_INVALID_COMPRESSION

**Message**: invalid compression, %s

**Action**: Invalid compression format, please adjust and retry.

### YAS-04292:ERR_ANS_PARSER_INVALID_ENCODING

**Message**: invalid encoding, %s

**Action**: Invalid encoding format, please adjust and retry.

### YAS-04293:ERR_ANS_PARSER_INVALID_CREATE_TAB

**Message**: cannot create col table when charset is not UTF8 or GB18030

**Action**: Please switch the character set to UTF8 or GB18030.

### YAS-04294:ERR_ANS_PARSER_CHECK_CONDITION_LEN

**Message**: maximum length of check condition is %d

**Action**: Please ensure the custom constraints length of the entered column is less than the maximum limit.

### YAS-04295:ERR_ANS_PARSER_COLUMN_DEFAULT_LEN

**Message**: maximum length of column default is %d

**Action**: Please ensure the default value length of the entered column is less than the maximum limit.

### YAS-04296:ERR_ANS_PARSER_ONE_CLAUSE_ALLOW

**Message**: only one clause allowed per query block

**Action**: Please ensure that only one of this type of clause appears in each query block.

### YAS-04297:ERR_ANS_PARSER_INVALID_ALTER_TABLE

**Message**: invalid ALTER TABLE option

**Action**: Please check the statement syntax and retry after specifying a valid option.

### YAS-04298:ERR_ANS_PARSER_INVALID_CURSOR_CLAUSE

**Message**: ";" or "return" expected but missing

**Action**: Please enter a valid cursor clause.

### YAS-04299:ERR_ANS_PARSER_INVALID_UNIFAUDIT_OPT

**Message**: missing or invalid unified audit option

**Action**: Please check the SQL statement and modify the syntax errors in the statement.

### YAS-04300:ERR_ANS_PARSER_INVALID_MCOLTTL

**Message**: invalid MCOL TTL value, must be positive

**Action**: Please modify the lifecycle of Mutable Data to a valid INTERVAL YEAR TO MONTH or INTERVAL DAY TO SECOND type data.

### YAS-04301:ERR_ANS_VERIFY_AMBIGUOUS_COLUMN

**Message**: ambiguous column

**Action**: Please do not input an ambiguous column.

### YAS-04303:ERR_ANS_VERIFY_UNEXPECTED_OBJECT

**Message**: unexpected %s

**Action**: Please remove the extra object.

### YAS-04304:ERR_ANS_VERIFY_COLUMN_COUNT_MISMATCH

**Message**: the count of column is mismatched

**Action**: Please enter a matching count of columns.

### YAS-04305:ERR_ANS_VERIFY_ONE_COLUMN_EXPECTED

**Message**: one column expected

**Action**: Please remove the extra columns.

### YAS-04307:ERR_ANS_VERIFY_TOO_MANY_RS_COLUMNS

**Message**: too many result set columns

**Action**: Please remove the excess columns.

### YAS-04308:ERR_ANS_VERIFY_RS_COLUMN_MISMATCH

**Message**: result set column is mismatched

**Action**: Please enter a matching count of columns.

### YAS-04309:ERR_ANS_VERIFY_ARG_COUNT_RANGE

**Message**: arguments count must be between % PRIu32  and % PRIu32

**Action**: Please enter valid parameter counts within the output prompt range.

### YAS-04310:ERR_ANS_VERIFY_INVALID_ORDER_COLUMN

**Message**: invalid order column

**Action**: Please enter a valid sequence for ordering.

### YAS-04311:ERR_ANS_VERIFY_NOT_COMPARABLE

**Message**: cannot be compared

**Action**: Please enter comparable values.

### YAS-04312:ERR_ANS_VERIFY_ARG_INVALID

**Message**: invalid number of arguments

**Action**: Please enter a valid number of arguments.

### YAS-04313:ERR_ANS_VERIFY_MATERIAL_CHECK

**Message**: each query block allows only one clause

**Action**: Please remove the redundant clause.

### YAS-04314:ERR_ANS_VERIFY_NOSELECTED_EXPR

**Message**: no selected expression exists

**Action**: Missing query columns, please enter the query columns.

### YAS-04315:ERR_ANS_VERIFY_ARG_NULL

**Message**: expression argument null

**Action**: Please enter a non-NULL value.

### YAS-04316:ERR_ANS_VERIFY_INVALID_GROUP_COLUMN

**Message**: not a single-group group function

**Action**: Please enter a valid group column.

### YAS-04317:ERR_ANS_VERIFY_INVALID_AGGR_COLUMN

**Message**: aggregate function cannot be nested

**Action**: Please do not input nested aggregate functions.

### YAS-04318:ERR_ANS_VERIFY_SEQ_NOT_ALLOWED

**Message**: sequence number not allowed here

**Action**: Please enter a sequence at a valid position.

### YAS-04319:ERR_ANS_VERIFY_TOO_MANY_AGGR

**Message**: too many aggregations

**Action**: Please reduce the number of aggregate functions.

### YAS-04320:ERR_ANS_VERIFY_QUERY_COLUMN_UNMATCH

**Message**: query column data types(%s, %s) is mismatched

**Action**: Please ensure that the data types match.

### YAS-04321:ERR_ANS_VERIFY_CONST_EXPR_EXPECTED

**Message**: const expression expected

**Action**: Please enter a constant expression.

### YAS-04322:ERR_ANS_VERIFY_INVALID_DATATYPE

**Message**: invalid datatype

**Action**: Please enter a valid data type.

### YAS-04323:ERR_ANS_VERIFY_ARG_COUNT_VALUE

**Message**: arguments count must be % PRIu32

**Action**: Please enter the valid number of arguments as prompted.

### YAS-04324:ERR_ANS_VERIFY_NOT_SELECTED_EXPR

**Message**: not a selected expression

**Action**: When specifying the DISTINCT keyword, the ORDER BY clause must use columns that exist in the projection columns. For cases where the result is fixed to one row, the DISTINCT keyword and ORDER BY clause can be removed directly.

* Where the WHERE clause includes primary key equality constraints.

* There is no GROUP BY clause and the projection columns are aggregate functions.

### YAS-04325:ERR_ANS_VERIFY_SUBQUERY_ONLY_LIMIT

**Message**: unique limit is not allowed in subQuery

**Action**: Please do not use the limit clause alone in subqueries.

### YAS-04327:ERR_ANS_VERIFY_INVALID_TIMESTAMP

**Message**: invalid timestamp value

**Action**: Please enter a valid timestamp value.

### YAS-04328:ERR_ANS_VERIFY_SELECT_TABLES

**Message**: can only select from fixed tables/views

**Action**: Queries are only allowed on non-physical tables; modifications are not allowed.

### YAS-04329:ERR_ANS_VERIFY_CAST_DEFAULT

**Message**: conversion from %s to %s with default return value is not allowed

**Action**: Please remove the redundant default clause.

### YAS-04330:ERR_ANS_VERIFY_ALIAS_EXPECTED

**Message**: must name this expression with a column alias

**Action**: Please enter an alias for this expression.

### YAS-04331:ERR_ANS_VERIFY_AGGR_DISTINCT

**Message**: DISTINCT is forbidden in this function

**Action**: Please remove the DISTINCT clause.

### YAS-04332:ERR_ANS_VERIFY_SNAPSHOT_NOT_ALLOWED

**Message**: snapshot expression is not allowed here

**Action**: Please use the snapshot expression at a valid position.

### YAS-04333:ERR_ANS_VERIFY_TO_DATE_FMT_NOT_CONST

**Message**: this argument must be a literal or bind variable

**Action**: Please enter a constant expression.

### YAS-04334:ERR_ANS_VERIFY_DDL_NO_BIND

**Message**: bind variables are not allowed for data definition operations

**Action**: Please check the SQL statement and correct any syntax errors in the statement.

### YAS-04335:ERR_ANS_VERIFY_FORBID_FOR_UPDATE

**Message**: FOR UPDATE of this query expression is not allowed

**Action**: Please do not use the FOR UPDATE clause in this query.

### YAS-04336:ERR_ANS_VERIFY_INVALID_ALONEEXPR

**Message**: boolean expression expected

**Action**: Please enter a boolean type expression.

### YAS-04337:ERR_ANS_VERIFY_INVALID_NEGATIVE

**Message**: invalid negative expression

**Action**: Please enter a non-negative value for the expression.

### YAS-04340:ERR_ANS_VERIFY_SYNONYM_FOR_SELF

**Message**: cannot create a synonym with same name as object

**Action**: Please enter a synonym with a different name.

### YAS-04341:ERR_ANS_VERIFY_TOO_COMPLEX_SUB_QUERY

**Message**: subquery nested too deeply

**Action**: Please reduce the levels of nesting for subqueries.

### YAS-04342:ERR_ANS_VERIFY_INVALID_TYPEOF_ARG

**Message**: arg2 must be one of 0 or 1

**Action**: Please enter 0 or 1 for arg2.

### YAS-04343:ERR_ANS_VERIFY_MISSING_EXPR

**Message**: missing expression

**Action**: Missing expression, please enter the expression.

### YAS-04344:ERR_ANS_VERIFY_MT_UPDATE_NOT_SUPPORT

**Message**: multi-table update is not supported

**Action**: Multi-table update is not currently supported.

### YAS-04345:ERR_ANS_VERIFY_MT_DELETE_NOT_SUPPORT

**Message**: multi-table delete is not supported

**Action**: Multi-table delete is not currently supported.

### YAS-04346:ERR_ANS_VERIFY_AMBIGUOUS_TABLE

**Message**: ambiguous table

**Action**: Please enter a non-ambiguous table name.

### YAS-04347:ERR_ANS_VERIFY_SELECT_PSEUDO_COL

**Message**: cannot select %s from , or sample, a join view without a key-preserved table

**Action**: Please do not query ROWID from a join view that does not preserve keys, or use sample clause. 

### YAS-04348:ERR_ANS_VERIFY_TRANSPTT_NOT_ALLOWED

**Message**: transactional private temporary table %s is not allowed here

**Action**: Please use the transactional private temporary table at a valid position.

### YAS-04349:ERR_ANS_VERIFY_UNSUPPORT_ERROR

**Message**: This statement is temporarily unable to execute due to an internal error

**Action**: This statement cannot be executed temporarily due to an internal error.

### YAS-04350:ERR_ANS_VERIFY_WINDOW_FRAME_NOT_ALLOWED

**Message**: window frame is not allowed here

**Action**: Please use window functions at valid positions.

### YAS-04351:ERR_ANS_VERIFY_MISSING_WIN_SPEC

**Message**: missing window specification for this function

**Action**: Refer to the corresponding function documentation and verify if the specified window statement is correct.

### YAS-04352:ERR_ANS_VERIFY_NLS_INVALID

**Message**: invalid NLS parameter string used in SQL function

**Action**: Please enter a valid NLS parameter. 

### YAS-04353:ERR_ANS_VERIFY_OBJECT_MODIFIED

**Message**: cannot use an object modified in current transaction

**Action**: Please do not use objects modified in the current transaction.

### YAS-04355:ERR_ANS_VERIFY_ALTER_SLICE_NOT_ENABLE

**Message**: ALTER SLICE is not enabled

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04356:ERR_ANS_VERIFY_SO_NOT_ALLOWED_PTT

**Message**: private temporary table is not allowed here

**Action**: Please do not use Private temporary table here.

### YAS-04357:ERR_ANS_VERIFY_COLUMN_ALIAS_TOO_LONG

**Message**: %s length % PRIu32  exceed max string length % PRIu64

**Action**: Please reduce the length of the column alias as per the prompt.

### YAS-04358:ERR_ANS_VERIFY_WIN_ORDER_BY_MISSING

**Message**: missing ORDER BY expression in window frame

**Action**: Please enter an ORDER BY expression.

### YAS-04359:ERR_ANS_VERIFY_NEED_CONNECT_BY

**Message**: CONNECT BY clause required in this query block

**Action**: Please enter a CONNECT BY clause.

### YAS-04360:ERR_ANS_VERIFY_CONNECT_BY_ROOT_UNSPT

**Message**: CONNECT BY ROOT operator is not supported in the START WITH or in the CONNECT BY condition

**Action**: Please do not use CONNECT BY ROOT here.

### YAS-04361:ERR_ANS_VERIFY_OPER_NOT_ALLOW

**Message**: Specified pseudocolumn or operator is not allowed here

**Action**: Please do not specify pseudocolumns or operators here.

### YAS-04362:ERR_ANS_VERIFY_FUNC_NOT_ALLOW

**Message**: %s function is not allowed here

**Action**: Please do not use that function here.

### YAS-04363:ERR_ANS_VERIFY_FUNC_ILL_PARAM

**Message**: illegal parameter in %s function

**Action**: Please enter a valid parameter.

### YAS-04364:ERR_ANS_VERIFY_CBY_LACK_NOCYCLE

**Message**: NOCYCLE keyword is required with CONNECT_BY_ISCYCLE pseudocolumn

**Action**: Missing keyword, please enter the keyword NOCYCLE.

### YAS-04365:ERR_ANS_VERIFY_NOT_ALLOW

**Message**: %s is not allowed here

**Action**: Please remove the extraneous clause.

### YAS-04366:ERR_ANS_VERIFY_MULTIPLE_ASSIGN

**Message**: multiple assign to one parameter

**Action**: Please remove the extraneous assignment statements.

### YAS-04367:ERR_ANS_VERIFY_COLCHKCONS_REF_COLUMN

**Message**: Column check constraint cannot reference other columns

**Action**: Please do not reference other columns in the column check constraint.

### YAS-04368:ERR_ANS_VERIFY_CHECK_DATE

**Message**: date or system variable wrongly specified in CHECK constraint

**Action**: Please do not specify date or system variables in the check constraint.

### YAS-04369:ERR_ANS_VERIFY_INVALID_PERCENT

**Message**: SAMPLE percentage must be in the range [0.000001,100)

**Action**: Please ensure the sample percentage is valid and try again.

### YAS-04371:ERR_ANS_VERIFY_DSTB_UNSUPPORT

**Message**: unsupport %s in distributed database

**Action**: This statement is not supported in distribution.

### YAS-04372:ERR_ANS_VERIFY_CHUNK_EXCEED

**Message**: maximum number of chunk count is %u

**Action**: Please reduce the number of chunks as per the prompt.

### YAS-04373:ERR_ANS_VERIFY_CHUNK_LESS_THAN_GROUP

**Message**: chunks must be more than nodegroups

**Action**: Please increase the number of chunks as per the prompt.

### YAS-04374:ERR_ANS_VERIFY_DUPLICATED_NODE_GROUP

**Message**: duplicate nodegroup id

**Action**: Please remove the duplicate nodegroup id and re-enter.

### YAS-04375:ERR_ANS_VERIFY_INVALID_NODE_GROUP

**Message**: invalid nodegroup id

**Action**: The GROUP_ID is invalid, please query the GV$NODE view to obtain the correct GROUP_ID and re-enter.

### YAS-04376:ERR_ANS_VERIFY_SORT_COL_NONEXIST

**Message**: sort column does not exist

**Action**: Missing sort sequence, please enter sort sequence.

### YAS-04377:ERR_ANS_VERIFY_INVALID_SORT_COL_TYPE

**Message**: a %s column cannot serve as a sort column

**Action**: Please enter a valid data type column as sort sequence; types such as CLOB, BLOB, NCLOB, BIT, ROWID, CURSOR, JSON, UDT are not allowed as sort sequence.

### YAS-04378:ERR_ANS_VERIFY_AUD_ADD_DROP_SAME_OPT

**Message**: both added and dropped cannot be same auditing option

**Action**: Choose one operation from ADD and DROP.

### YAS-04379:ERR_ANS_VERIFY_INVALID_ALTER_DATATYPE

**Message**: invalid alteration of datatype

**Action**: Please enter a valid data conversion type.

### YAS-04380:ERR_ANS_VERIFY_ENABLE_XFMR

**Message**: cannot enable/disable transformer for %s

**Action**: Enable/Disable transformers allowed only for LSC tables.

### YAS-04381:ERR_ANS_VERIFY_DEFAULT_VALUE_INCOMPATIBLE

**Message**: modified column type is incompatible with column default value

**Action**: Please enter a data type that is compatible with the column default value.

### YAS-04382:ERR_ANS_VERIFY_AUD_PLY_NOT_SIMPLE_COND

**Message**: audit policy does not have a simple rule condition

**Action**: Please use simple rule expressions for audit policy conditions, refer to the audit_condition in the CREATE AUDIT POLICY syntax documentation.

### YAS-04383:ERR_ANS_VERIFY_TSS_MAXSIZE_LESS_THAN_SIZE

**Message**: tablespace set maxsize cannot be smaller than size

**Action**: The maximum auto-extensible space of the tablespace must be greater than or equal to the pre-allocated space.

### YAS-04384:ERR_ANS_VERIFY_UNSUPPORTED_EXPR_TYPE

**Message**: only const type supported

**Action**: The option is set to constant.

### YAS-04385:ERR_ANS_VERIFY_UNSUPPORTED_EXPR_ARGUMENT

**Message**: wrong number, type or constraint of argument in call to %s

**Action**: The number, type, or constraint of parameters is incorrect.

### YAS-04386:ERR_ANS_VERIFY_TOO_MANY_AC_COLUMNS

**Message**: too many columns in %s table

**Action**: Reduce access constraint defined columns.

### YAS-04387:ERR_ANS_VERIFY_BOUND_INVALID_TYPE

**Message**: bound should be a const number

**Action**: Change the bound value to a constant.

### YAS-04388:ERR_ANS_VERIFY_BOUND_OUT_OF_RANGE

**Message**: bound is out of range

**Action**: The bound value is out of range, please modify the bound value.

### YAS-04389:ERR_ANS_VERIFY_INVALID_AC_TYPE

**Message**: invalid AC type

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04390:ERR_ANS_VERIFY_AC_UNSUPPORTED_FILTER

**Message**: the filter used is not supported

**Action**: Modify the access constraint filter condition.

### YAS-04391:ERR_ANS_VERIFY_AC_FILTER_ARGS

**Message**: the arguments used in filter is not supported

**Action**: Modify the access constraint filter condition.

### YAS-04392:ERR_ANS_VERIFY_AC_INVALID_AGGR_TYPE

**Message**: invalid aggregate function used in AC

**Action**: Modify the aggregate function type of the aggregate column in the access constraint.

### YAS-04393:ERR_ANS_VERIFY_AC_DUPLICATED_AGGR

**Message**: duplicated aggregate function

**Action**: Duplicate aggregate columns, modify the aggregate columns in the access constraint.

### YAS-04394:ERR_ANS_VERIFY_AC_AGGR_ARGUMENT

**Message**: the argument of aggregate function in AC should be a column

**Action**: The aggregate column only supports a single column, modify the aggregate columns in the access constraint.

### YAS-04395:ERR_ANS_VERIFY_AC_TYPE_MISMATCH

**Message**: AC type %s does not match table type %s

**Action**: The table type does not support creating an access constraint.

### YAS-04396:ERR_ANS_VERIFY_SLICE_PARTITION

**Message**: query slice of part table without partition name

**Action**: The slice for the partition table is not specified with partition name.

### YAS-04397:ERR_ANS_VERIFY_INVALID_STANDBY_NAME

**Message**: missing or invalid standby name

**Action**: Please enter a valid Standby Database name.

### YAS-04398:ERR_ANS_VERIFY_DUPLICATE_NAME

**Message**: duplicate %s

**Action**: Duplicate names are not allowed.

### YAS-04399:ERR_ANS_VERIFY_FUNCIDX_FUNC

**Message**: only pure functions can be %s

**Action**: The entered expression does not conform to the specification for function-based indexes or virtual columns, please re-enter the correct expression.

### YAS-04400:ERR_ANS_VERIFY_DATA_TYPE_MISMATCH

**Message**: data type %s expected, but %s got

**Action**: Type mismatch found in the validation stage, input an effective type as per the prompt.

### YAS-04401:ERR_ANS_EXEC_DATA_TYPE_MISMATCH

**Message**: data type %s expected, but %s got

**Action**: Type mismatch found in the execution stage, input an effective type as per the prompt.

### YAS-04402:ERR_ANS_EXEC_QUERY_EXPR_MULTI_ROWS

**Message**: query expression return multiple rows

**Action**: Please enter a correct subquery statement.

### YAS-04403:ERR_ANS_EXEC_EXPLAIN_OVERFLOW

**Message**: explain too complex

**Action**: Please simplify the SQL statement.

### YAS-04404:ERR_ANS_EXEC_SRLZ_OVERFLOW

**Message**: serialize too complex

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04405:ERR_ANS_EXEC_AGGR_TOO_LARGE

**Message**: aggregation variant size too large

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04406:ERR_ANS_DSRLZ_BROKEN

**Message**: deserialize %s broken

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04407:ERR_ANS_DSRLZ_SUBQUERY_EXPR_NULL

**Message**: deserialize subquery expression null

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04408:ERR_ANS_DSRLZ_DATASET_NULL

**Message**: deserialize dataset null

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04409:ERR_ANS_DSRLZ_USER_ID_MISMATCH

**Message**: deserialize user id mismatch

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04410:ERR_ANS_DSRLZ_DICT_VER_MISMATCH

**Message**: deserialize dictionary version mismatch

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04412:ERR_ANS_VALUE_ERROR

**Message**: value error, %s

**Action**: Please enter a valid value according to the corresponding data type specification.

### YAS-04413:ERR_ANS_SRLZ_INVALID_STAGE

**Message**: serialize invalid stage

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04414:ERR_ANS_EXEC_STAGE_NOT_FOUND

**Message**: stage %u not found

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04415:ERR_ANS_EXEC_UPDATE_CROSS_PART

**Message**: updating partition key column would cause a partition change

**Action**: This operation is not allowed.

### YAS-04421:ERR_ANS_MODE_IS_NOT_SUPPORTED

**Message**: The mode is not supported

**Action**: This operation is not allowed.

### YAS-04422:ERR_ANS_EXEC_EXPR_TRAV

**Message**: too many expressions

**Action**: The nested depth of typeofFunction cannot exceed 6, please modify the SQL statement.

### YAS-04423:ERR_ANS_EXEC_INVALID_DATA_TYPE

**Message**: inconsistent datatypes: expected %s got %s

**Action**: Please input valid types as per the prompt.

### YAS-04424:ERR_ANS_EXEC_ARUMENT_OUT_OF_RANGE

**Message**: argument '%s' is out of range

**Action**: Please enter a valid value according to the parameter specification.

### YAS-04425:ERR_ANS_EXEC_DATA_EXCEED_LIMIT

**Message**: data size exceeds limit %ld

**Action**: Please enter valid values according to the prompt.

### YAS-04426:ERR_ANS_EXEC_ARGU_VALUE_OUT_OF_RANGE

**Message**: the argument value is out of range

**Action**: The input parameter is invalid, please refer to [Built-in Functions](../Development Guide/SQL Reference Manual/Built-in Functions/00Built-in Functions) or [Built-in Advanced PL Packages](../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/00Built-in Advanced PL Packages) to modify your parameters and re-execute.

### YAS-04427:ERR_ANS_EXEC_UNSTABLE_RS

**Message**: row set in the source tables is not stable

**Action**: This operation is not allowed.

### YAS-04428:ERR_ANS_EXEC_ILLEGAL_FOLLOWING_ESCAPE

**Message**: missing or illegal character following the escape character

**Action**: Regular expression usage error, please modify the SQL statement.

### YAS-04429:ERR_ANS_EXEC_ESCAPE_LEN_NOT_ONE

**Message**: escape character must be character string of length 1

**Action**: Regular expression usage error, please modify the SQL statement.

### YAS-04431:ERR_ANS_EXEC_REG_ERROR

**Message**: regexp function error: %s

**Action**: Regular expression usage error, please modify the SQL statement.

### YAS-04432:ERR_ANS_EXEC_PARAM_IS_NULL

**Message**: cannot find parameter

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04433:ERR_ANS_EXEC_WIN_FUNC_INVALID_FRAME

**Message**: invalid window frame value: %s

**Action**: Window function usage error, please modify the SQL statement.

### YAS-04434:ERR_ANS_SRLZ_ITEMCOUNT_TOO_LARGE

**Message**: serialize nameExpr itemCount should not exceed 4

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04435:ERR_ANS_EXEC_CONNECT_BY_LOOP

**Message**: connect by loop in user data

**Action**: Hierarchical query error, please check the input data.

### YAS-04436:ERR_ANS_DUPLICATE_KEY_UPDATE

**Message**: restart insert on duplicate

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04437:ERR_ANS_EXEC_PARALLEL_LACK_RES

**Message**: parallel execution resource %s is busy

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04438:ERR_ANS_EXEC_PARALLEL_STAGE

**Message**: parallel server error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04439:ERR_ANS_INVALID_SPACE_FILE

**Message**: invalid tempfile or datafile clause for creating tablespace

**Action**: Check if the file clause is consistent with the tablespace properties, i.e., temporary tablespace uses TEMPFILE, normal tablespace uses DATAFILE.

### YAS-04440:ERR_ANS_INVALID_SQL_STMT

**Message**: invalid sql statement

**Action**: Check if the SQL statement meets the requirements.

### YAS-04442:ERR_ANS_EXEC_PARTIAL_MULTIBYTE_CHAR

**Message**: partial multibyte character

**Action**: Partial multibyte character, please modify the SQL statement to re-execute.

### YAS-04443:ERR_ANS_INVALID_RETURN_RESULT_STMT

**Message**: Implicit result cannot be returned through this statement

**Action**: return_result usage error, please modify the SQL statement.

### YAS-04444:ERR_ANS_EXEC_EXPLAIN_ERROR

**Message**: explain error, %s

**Action**: Explain process error, the specific error depends on the error prompt.

### YAS-04445:ERR_ANS_EXEC_CBY_ERROR

**Message**: %s

**Action**: Connect by usage error, the specific error depends on the error prompt.

### YAS-04446:ERR_ANS_EXEC_DSTB_LACK_WORKER_RES

**Message**: not enough threads for distributed execution, please check MAX_PARALLEL_WORKERS and MAX_SESSIONS configuration

**Action**: Insufficient distributed threads, please check if MAX_PARALLEL_WORKERS and MAX_SESSIONS are configured too small, the number of threads required for a SQL statement on a node equals the number of PX multiplied by the degree of parallelism.

### YAS-04447:ERR_ANS_EXEC_MAX_STAGE_COUNT_LIMIT

**Message**: maximum number of stages count is %u

**Action**: The number of stages required for SQL statement execution exceeds the maximum value %u.

### YAS-04449:ERR_ANS_EXEC_TABQ_WRITE_DISORDER

**Message**: write table queue message id out of sequence. endpoint %u, isLocal %u, queue id %u, route %u->%u, expect %u, actual %u

**Action**: Table queue sending data, checking for disorder, please retry.

### YAS-04450:ERR_ANS_EXEC_TABQ_ACK_DISORDER

**Message**: acknowledge table queue message id out of sequence. endpoint %u, isLocal %u, queue id %u, route %u->%u, expect %u, actual %u

**Action**: Table queue responding data, checking for disorder, please retry.

### YAS-04451:ERR_ANS_EXEC_TABQ_DUP_CTRL

**Message**: receive duplicated table queue control message. endpoint %u, queue id %u, route %u->%u, cmd %s

**Action**: Table queue received duplicate control command, please retry.

### YAS-04452:ERR_ANS_EXEC_TABQ_HEAP_SORT_FULL

**Message**: table queue heap is full, cannot insert more

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04453:ERR_ANS_EXEC_TABQ_HEAP_SORT_EMPTY

**Message**: table queue heap is empty, cannot delete more

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04454:ERR_ANS_EXEC_PARALLEL_LACK_WORKER

**Message**: not enough threads for parallel execution

**Action**: Insufficient parallel threads, please increase MAX_PARALLEL_WORKERS, or reduce the degree of parallelism. If the error occurs on INSERT parallel, you can disable PARALLEL DML and rerun.

### YAS-04455:ERR_ANS_EXEC_INVALID_TABLE_TYPE

**Message**: unexpected table type, expect %s table, actual: %s table

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04457:ERR_ANS_SEND_RUNTIME_FILTER_TIMEOUT

**Message**: send runtime filter timeout

**Action**: Runtime filter sending timeout, please check network status.

### YAS-04458:ERR_ANS_EXEC_INVALID_MAPPING_COLUMN

**Message**: column mapping failed: %s

**Action**: Unsupported rewriting statement, please contact our technical support for resolution.

### YAS-04459:ERR_ANS_STRING_OVER_ERROR

**Message**: value error, %s size exceeds the max size of STRING

**Action**: Please enter a valid value according to the corresponding data type specification.

### YAS-04460:ERR_ANS_AUDIT_ERROR_OBJECT_ACTION

**Message**: invalid auditing option for %s

**Action**: Use valid object auditing options.

### YAS-04461:ERR_ANS_QUERY_VIEW_WHEN_NOT_OPEN

**Message**: query view is not allowed if database is not open

**Action**: Please switch all instances to OPEN state.

### YAS-04462:ERR_ANS_EXEC_GB18030_SEQUENCE_WRONG

**Message**: gb18030 sequence is wrong

**Action**: GB18030 character input error, please correct it and try again.

### YAS-04463:ERR_ANS_EXEC_GB18030_BUFFER_SMALL

**Message**: gb18030 character buffer is too small

**Action**: GB18030 character input error, please correct it and try again.

### YAS-04464:ERR_ANS_EXEC_PX_RES_UNAVAILABLE

**Message**: consumer group limit less than px resource request

**Action**: Please enlarge the current resource group's parallel_server_limit, or increase the corresponding instance's maxParallelWorkers and PQ_POOL_SIZE parameters.

### YAS-04465:ERR_ANS_EXEC_SDT_LEVEL_EXCEED

**Message**: sort tree level exceeds limit %u

**Action**: The sorting tree construction depth exceeds the limit.

### YAS-04466:ERR_ANS_EXEC_CIPHER_SUITE_INVALID

**Message**: invalid cipher type passed

**Action**: The passed cipher type is invalid.

### YAS-04467:ERR_ANS_EXEC_CRYPTO_PARAM_LENGTH

**Message**: %s length not match

**Action**: The length of the encryption advanced package parameters does not match.

### YAS-04468:ERR_ANS_EXEC_CRYPTO_SUITE_NULL

**Message**: no cipher type specified

**Action**: No cipher type was passed.

### YAS-04469:ERR_ANS_CLIENT_ID_TOO_LONG

**Message**: client identifier is too long

**Action**: The specified client identifier is too long, please ensure it does not exceed 64 bytes.

### YAS-04470:ERR_ANS_EXEC_RECURSIVE_LOOP

**Message**: recursive query with loop, the max recursion depth is %u

**Action**: The preset maximum recursion depth was exceeded, resulting in potential infinite loops or excessive depth, please check the original statement and dataset relationship.

### YAS-04471:ERR_ANS_JSON_BEYOND_OUT_LIMIT

**Message**: output limit should be between 1 and % PRIu64

**Action**: The length of the JSON converted to string does not meet the requirements.

### YAS-04472:ERR_ANS_JSON_CONSTANT_REQUIRE

**Message**: %s requires a constant %s

**Action**: A constant is required here.

### YAS-04473:ERR_ANS_JSON_BINARY_BEYOND_MAX_SIZE

**Message**: json binary data exceeds limit % PRIu64

**Action**: Exceeding the upper limit of processable JSON object data.

### YAS-04474:ERR_ANS_JSON_INVALID

**Message**: invalid json value

**Action**: JSON parsing failed, the object does not conform to standard JSON format, please check the JSON format you entered.

### YAS-04475:ERR_ANS_JSON_BEYOND_MAX_DEPTH

**Message**: json object or array depth exceeds limit 100

**Action**: The recursive nesting depth of the JSON object or array is limited to at most 100, please reduce your nesting depth.

### YAS-04476:ERR_ANS_JSON_BEYOND_MAX_SIZE

**Message**: json data exceeds limit 32MB

**Action**: The json_query query result exceeds the upper limit, please adjust your query statement to reduce the query result.

### YAS-04477:ERR_ANS_JSON_INVALID_DECODE_TYPE

**Message**: decode error, must be a JSON variable

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04478:ERR_ANS_JSON_PATH_SYNTAX_ERROR

**Message**: array step contains unexpected characters

**Action**: Please confirm if your path expression's array query step contains illegal characters.

### YAS-04479:ERR_ANS_JSON_PATH_ARR_STEP_MISSING

**Message**: missing array step option

**Action**: Please confirm if your path expression's array query step is missing an index.

### YAS-04480:ERR_ANS_JSON_ARR_INVALID_INDEX

**Message**: index step requires integer input

**Action**: Please confirm if your path expression's array query step contains non-integer numbers.

### YAS-04481:ERR_ANS_JSON_PATH_OBJ_STEP_MISSING

**Message**: object step contains unexpected characters

**Action**: Please confirm if your path expression's object query step contains illegal characters.

### YAS-04482:ERR_ANS_JSON_INVALID_KEY_CHAR

**Message**: invalid character %s in object field name

**Action**: Please confirm if your path expression's field query step contains illegal characters.

### YAS-04483:ERR_ANS_JSON_BRACKET_EXPECTED

**Message**: ',' or bracket expected but missing

**Action**: Please confirm if your JSON string ends with illegal characters.

### YAS-04484:ERR_ANS_JSON_RETURN_MULTIPLE

**Message**: json query return multiple result but without array wrapper

**Action**: The JSON query contains multiple results, please add with array wrapper to display all results.

### YAS-04485:ERR_ANS_JSON_INVALID_ESCAPE

**Message**: invalid json escape character

**Action**: Please confirm if your JSON string contains illegal escape characters.

### YAS-04486:ERR_ANS_JSON_STEP_AFTER_FUNC

**Message**: unexpected step after function step

**Action**: Please check your path expression, no steps are allowed after the function query step in path expression.

### YAS-04487:ERR_ANS_JSON_FUNC_NO_WRAPPER

**Message**: function step cannot return without wrapper

**Action**: JSON queries containing function steps need to add with array wrapper to display results.

### YAS-04488:ERR_ANS_JSON_INVALID_CONTROL

**Message**: control character must use \u to insert

**Action**: Please correct the control characters in your JSON string to use \u format.

### YAS-04489:ERR_ANS_JSON_INVALID_ARRAY_STEP

**Message**: invalid array step in path expression

**Action**: Please check if your path expression's array query steps conform to syntax.

### YAS-04490:ERR_ANS_JSON_INVALID_TOKEN

**Message**: unknown json token

**Action**: Please check your input for illegal JSON keywords.

### YAS-04491:ERR_ANS_JSON_INVALID_NUMBER

**Message**: invalid json number

**Action**: Please correct the number type members in your input data that do not comply with JSON format.

### YAS-04492:ERR_ANS_JSON_INVALID_UNICODE

**Message**: source unicode cannot transform to utf-8 character

**Action**: Please correct your input data that cannot be converted to UTF-8 format.

### YAS-04493:ERR_ANS_JSON_INVALID_FUNC_STEP

**Message**: json_exists or json_query without wrapper cannot contain function step

**Action**: Please remove function query steps in the path expression, json_exists function or json_query function without the with wrapper parameter does not support function queries.

### YAS-04495:ERR_ANS_JSON_SYNTAX_ERROR

**Message**: Json Syntax Error at line %d column %d

**Action**: Syntax error occurred while parsing the JSON object string, please adjust the string according to the indicated error position and reason.

### YAS-04496:ERR_ANS_JSON_MEMORY_POOL_ERROR

**Message**: create json large memory pool failed

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04497:ERR_ANS_JSON_MEMORY_CONTEXT_ERROR

**Message**: create json large memory context failed

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04498:ERR_ANS_JSON_RESULT_TOO_COMPLEX

**Message**: json result set too complex

**Action**: The JSON result set is too complex, please simplify the JSON query statement.

### YAS-04499:ERR_ANS_JSON_TABLE_UNSUPPORTED_TYPE

**Message**: unsupported datatype %s in json_table

**Action**: JSON_TABLE does not support this data type, please use a compatible one instead.

<span id="errno4501" name="errno4501"></span>

### YAS-04501:ERR_ANL_OPTMZ_TOO_MANY_MAT

**Message**: too many materialized area

**Action**: The number of materialized zones exceeds 65535. Reduce the materialized queries in the SQL statement.

### YAS-04503:ERR_ANL_ADOPT_MISS_PROJ_VAR

**Message**: adopting plan no project vars

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04504:ERR_ANL_ADOPT_TOO_MANY_PROJ_VAR

**Message**: too many project vars

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04505:ERR_ANL_OPTMZ_TOO_MANY_JOIN

**Message**: too many joins, maximum number is %u

**Action**: The number of joins exceeds the maximum limit. Rewrite the SQL to reduce the number of joined tables.

### YAS-04506:ERR_ANL_HASH_CREATION

**Message**: failed to create hash struct

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04507:ERR_ANL_HASH_NULL_POINTER

**Message**: hash input null pointer.

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04508:ERR_ANL_OPTMZ_DSTB_NO_DSTB_KEY

**Message**: missing value for partitioning column for sharded table

**Action**: DML operations must specify the value for the partition key, please check the update data.

### YAS-04509:ERR_ANL_OPTMZ_DSTB_UNSUPPORT_TABLE

**Message**: only support sharded or duplicated table

**Action**: Remove non-duplicated table and non-partition table from SQL statement.

### YAS-04510:ERR_ANL_OPTMZ_DSTB_UPDATE_DIST_KEY

**Message**: cannot update partitioning column for sharded table

**Action**: Updating the value of the partition key is not supported, please check the update data.

### YAS-04511:ERR_ANL_OPTMZ_DSTB_NO_PX_MODE

**Message**: cannot find corresponding px mode

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04512:ERR_ANL_OPTMZ_DSTB_NO_PX_MATCHED

**Message**: cannot find matched px

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04513:ERR_ANL_OPTMZ_DSTB_UNEXPECTED_RULE

**Message**: unexpect distributed rule apply to plan

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04514:ERR_ANL_OPTMZ_DSTB_NOT_SUPPORT

**Message**: distribute optimizer does not support with details '%s'

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04515:ERR_ANL_OPTMZ_TOO_MANY_WIN_FUNC

**Message**: too many window functions, maximum number is %u

**Action**: Reduce the number of window functions in the SQL statement.

### YAS-04516:ERR_ANL_OPTMZ_EST_STATS

**Message**: statistics estimation issue

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04517:ERR_ANL_OPTMZ_TOO_MANY_PARALLEL

**Message**: too many parallel

**Action**: The degree of parallelism specified is too high, the maximum degree is 255, please lower the degree of parallelism.

### YAS-04518:ERR_ANL_OPTMZ_TOO_MANY_MAT_COLUMN

**Message**: too many materialized columns

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04519:ERR_ANL_OPTMZ_MEMORY_EXCEED

**Message**: sql too complex,optimizer memory is insufficient

**Action**: The memory required for optimization is insufficient, please increase the size of SHARE_POOL_SIZE.

### YAS-04520:ERR_ANL_OPTMZ_TOO_MANY_FILTER

**Message**: maximum number of expanded filter conditions exceeded

**Action**: Optimizer internal error, please check the SQL statement, reduce the number of associated filters, and retry, or contact our technical support for resolution.

### YAS-04521:ERR_ANL_OPTMZ_NOT_IMPL_ROWNUM_FORM

**Message**: In columnstored table, %s

**Action**: Please modify the usage form of rownum in column-stored tables as per the error message.

### YAS-04522:ERR_ANL_OPTMZ_INVALID_LEADING

**Message**: invalid hint leading

**Action**: Invalid leading, please modify the hint.

### YAS-04523:ERR_ANL_OPTMZ_CBY_ERROR

**Message**: %s

**Action**: Modify the connect by usage according to the prompt.

### YAS-04524:ERR_ANL_OPTMZ_ADV_GROUP

**Message**: GROUPING SETS/ROLLUP/CUBE is out of range

**Action**: The grouping sets/rollup/cube exceeded memory limits when unified using grouping sets, reduce the number of columns after GROUP BY.

### YAS-04601:ERR_ANL_COMPAT_ERROR

**Message**: compatibility error, %s

**Action**: An error occurred in compatibility mode, please refer to the details of the error message to modify the SQL.

### YAS-04602:ERR_ANL_COLLATION_CONFLICT

**Message**: mysql compatibility error, COLLATION '%s' is not valid for CHARACTER SET '%s'

**Action**: An error occurred in compatibility mode, there is a conflict between the character setting and character set, please reset.

### YAS-04603:ERR_ANL_CHECK_NON_UPDATABLE

**Message**: mysql compatibility error, CHECK OPTION on non-updatable view %s

**Action**: An error occurred in compatibility mode, CHECK actions are prohibited on non-updatable views, please modify usage.

### YAS-04604:ERR_ANL_DROP_FOREIGN_KEY

**Message**: mysql compatibility error, cannot drop nonexistent foreign key

**Action**: An error occurred in compatibility mode, the specified foreign key to be deleted does not exist, please specify an existing foreign key.

### YAS-04606:ERR_ANL_VARIABLE_READ_ONLY

**Message**: mysql compatibility error, variable '%s' is a read only variable

**Action**: An error occurred in compatibility mode, read-only system variables cannot be changed, please modify usage.

### YAS-04607:ERR_ANL_VARIABLE_UNKNOWN

**Message**: mysql compatibility error, unknown system variable '%s'

**Action**: An error occurred in compatibility mode, input system variable is incorrect, please enter the correct system variable.

### YAS-04608:ERR_ANL_VARIABLE_GLOBAL_SET_ONLY

**Message**: mysql compatibility error, variable '%s' is a GLOBAL variable and should be set with SET GLOBAL

**Action**: An error occurred in compatibility mode, this variable is a global system variable and can only be assigned using SET GLOBAL, please modify usage.

### YAS-04609:ERR_ANL_VARIABLE_SESSION_READ_ONLY

**Message**: mysql compatibility error, SESSION variable '%s' is read only. Use SET GLOBAL to assign the value

**Action**: An error occurred in compatibility mode, this session system variable is read-only, please use SET GLOBAL to assign the corresponding global system variable.

### YAS-04610:ERR_ANL_VARIABLE_VALUE_INVALID

**Message**: mysql compatibility error, variable '%s' can't be set to the value of '%s'

**Action**: An error occurred in compatibility mode, this variable has an illegal assignment, please modify the assignment.

### YAS-04611:ERR_ANL_INCORRECT_ARGUMENT_TYPE

**Message**: mysql compatibility error, incorrect argument type to variable '%s'

**Action**: An error occurred in compatibility mode, argument assignment type for this variable is incorrect, please modify the assignment.

### YAS-04612:ERR_ANL_VARIABLE_GLOBAL_GET_ONLY

**Message**: mysql compatibility error, variable '%s' is a GLOBAL variable

**Action**: An error occurred in compatibility mode, this variable is a global system variable, please modify usage.

### YAS-04613:ERR_ANL_VARIABLE_ILLEGAL

**Message**: mysql compatibility error, %s variable name '%s' is illegal

**Action**: An error occurred in compatibility mode. The variable name is invalid. Please use the correct system variable name.

### YAS-04801:ERR_ANS_PARSER_NOAUDIT_WITH_EXCEPT

**Message**: noaudit command with except clause is not allowed

**Action**: Remove the EXCEPT clause from the NOAUDIT statement.

### YAS-04802:ERR_ANS_PARSER_MISSING_INVALID_OPT

**Message**: missing or invalid option

**Action**: Modify syntax errors in the statement.

### YAS-04803:ERR_ANS_PARSER_INVALID_OUTLINE_NAME

**Message**: invalid outline name

**Action**: Redefine the outline name according to naming rules.

### YAS-04804:ERR_ANS_PARSER_INVALID_OPTION

**Message**: only supported const options

**Action**: Please enclose the option values in single quotes.

### YAS-04805:ERR_ANS_PARSER_INVALID_DOUBLE_MD

**Message**: for float(M, D), double(M, D), M must be >= D

**Action**: Parameter M must be greater than or equal to parameter D.

### YAS-04806:ERR_ANS_PARSER_INVALID_DOUBLE_PRC

**Message**: for double(p), p must be %d <= p <= %d

**Action**: Parameter p must be in the range [0,53].

### YAS-04807:ERR_ANS_PARSER_INVALID_DOUBLE_PM

**Message**: invalid number for float/double width or precision

**Action**: Please enter valid numbers for width or precision of the float type.

### YAS-04808:ERR_ANS_PARSER_INVALID_SLICE_ID

**Message**: invalid slice id: %s slice id must be an integer less than 4294967295

**Action**: Please enter a slice id less than 4294967295.

### YAS-04809:ERR_ANS_PARSER_AUDIT_INCORRECT_ORDER

**Message**: incorrect order of unified audit options

**Action**: Please add different categories of audit items in the correct order.

### YAS-04810:ERR_ANS_PARSER_NOT_SAME_SQL_TYPE

**Message**: not the same sql type

**Action**: The two SQL statements must be of the same type.

### YAS-04811:ERR_ANS_VERIFY_SLICE_QUERY_TABLE

**Message**: only lsc table allow query slice

**Action**: Please use the LSC table for slice queries.

### YAS-04812:ERR_ANS_PARSER_INVALID_LOB_OPTION

**Message**: invalid lob storage option specification

**Action**: Please specify the correct LOB storage option, refer to the description of the lob_clause in the [CREATE TABLE](../Development Guide/SQL Reference Manual/SQL Statements/CREATE TABLE) documentation.

### YAS-04813:ERR_ANS_VERIFY_TOO_MANY_AUD_PLY

**Message**: maximum number of audit policy is %u

**Action**: The created audit policy exceeds the limit, please delete unnecessary audit policies and recreate.

### YAS-04814:ERR_ANS_VERIFY_WIN_ORDER_BY_NOT_ALLOW

**Message**: ORDER BY is forbidden in this function

**Action**: ORDER BY is prohibited in the over clause of the median window function.

### YAS-04815:ERR_ANS_PARSER_TEMPLATE_PART_NAME

**Message**: name missing for subpartition in template

**Action**: Please name the second level partition template created.

### YAS-04816:ERR_ANS_PARSER_INSERT_VALUES_LIMIT

**Message**: insert values cannot exceed %d groups

**Action**: Cannot insert more than 32768 groups of data at once.

### YAS-04817:ERR_ANS_PARSER_DUPLICATE_OPTION

**Message**: duplicate option specified

**Action**: Please remove duplicate options from the SQL statement.

### YAS-04818:ERR_ANS_VERIFY_DROP_MVIEW

**Message**: must use DROP MATERIALIZED VIEW to drop "%s.%s"

**Action**: Please use the DROP MATERIALIZED VIEW syntax to delete the specified materialized view.

### YAS-04819:ERR_ANS_PARSER_INVALID_BUCKET

**Message**: invalid %s bucket: %s"

**Action**: Please adjust the data bucket syntax according to the error message.

### YAS-04820:ERR_ANS_PARSER_INVALID_PART_NAME

**Message**: invalid partition name

**Action**: The specified partition name is not allowed, please use a valid partition name.

### YAS-04821:ERR_ANS_PARSER_INVALID_SUBPART_NAME

**Message**: invalid subpartition name

**Action**: The specified subpartition name is not allowed, please use a valid subpartition name.

### YAS-04822:ERR_ANS_PARSER_INVALID_SPLIT_NUM

**Message**: one partition must be split into two or more partitions

**Action**: The partition split must result in two or more partitions.

### YAS-04823:ERR_ANS_PARSER_INVALID_SPLIT_BOUNDS

**Message**: last resulting partition cannot contain bounds

**Action**: The last resulting partition in the partition split cannot define partition boundaries.

### YAS-04824:ERR_ANS_PARSER_MISSING_VALUES

**Message**: missing VALUES keyword

**Action**: Please add the VALUES keyword.

### YAS-04825:ERR_ANS_PARSER_MISSING_LESS_THAN

**Message**: expecting VALUES LESS THAN or AT clause

**Action**: Please add the LESS THAN clause.

### YAS-04826:ERR_ANS_PARSER_SPLIT_EXACTLY_TWO_PARTS

**Message**: a partition may be split into exactly two new partitions

**Action**: Please ensure the partition is split into two partitions.

### YAS-04827:ERR_ANS_VERIFY_ROWID_SAMPLE_FROM_VIEW

**Message**: cannot select ROWID from, or sample, a view with DISTINCT, GROUP BY, etc.

**Action**: Cannot query ROWID or sample from certain views that include set operations or clauses like DISTINCT and GROUP BY.

### YAS-04828:ERR_ANS_PARSER_INVALID_PARAM_COUNT

**Message**: invalid parameter count

**Action**: Please confirm if the number of bind parameters is correct.

### YAS-04829:ERR_ANS_PARSER_INVALID_LOB_OPTION_FOR_HASH_OR_SUBPART

**Message**: invalid option specified for a HASH partition or subpartition of a LOB column

**Action**: This property is not allowed for a Hash Partition or a LOB second-level partition. 

### YAS-04830:ERR_ANS_PARSER_INVALID_FLOAT_PRC

**Message**: for float(p), p must be %d <= p <= %d

**Action**: Parameter p's range must be between [0,126].

### YAS-04831:ERR_ANS_PARSER_CTE_WITH_PIVOT

**Message**: PIVOT clause is not allowed in recursive query

**Action**: In a recursive CTE, the columns used in the PIVOT operation are not allowed to be used as the recursive projection columns in the recursive query. Please check the input for this part.

### YAS-04832:ERR_ANS_PARSER_DUPLICATED_PARAM

**Message**: invalid duplicated param: %s

**Action**: Duplicate bind parameters with the same name are prohibited in anonymous blocks.

### YAS-04833:ERR_ANS_VERIFY_CLUSTER_UNSUPPORTED

**Message**: unsupported %s in cluster database

**Action**: This statement is not applicable to YACs.

### YAS-04834:ERR_ANS_PARSER_COL_ENCRYPT_TYPE

**Message**: encryption is not allowed for this data type

**Action**: Ensure the column encryption is suitable for the data type.

### YAS-04835:ERR_ANS_PARSER_ENCRYPT_ALG

**Message**: missing or invalid encryption algorithm, %s

**Action**: The encryption algorithm is invalid, please choose the appropriate encryption algorithm based on the error content.

### YAS-04836:ERR_ANS_PARSER_NOT_SAME_COL_ENCRYPT

**Message**: A different encryption algorithm has been chosen for the table or tablespace

**Action**: The encryption algorithm used during the creation or access of the table is inconsistent, the same algorithm must be used for different columns within the same table.

### YAS-04837:ERR_ANS_PARSER_NOT_FK_COL_ENCRYPT

**Message**: referenced or referencing FK constraint column cannot be encrypted

**Action**: Columns used in foreign key constraints cannot be encrypted.

### YAS-04838:ERR_ANS_PARSER_INDEX_ENCRYPT_ALG

**Message**: encryption is not allowed for this index type

**Action**: Ensure the index type is suitable for column encryption, only btree and compound indexes are allowed for encrypted columns.

### YAS-04839:ERR_ANS_PARSER_INDEX_COL_ENCRYPT

**Message**: the specified index may not be defined on an encrypted column

**Action**: Ensure the index being added is suitable for the encrypted column.

### YAS-04840:ERR_ANS_VERIFY_PART_COL_ENCRYPT

**Message**: an encrypted column cannot serve as a partitioning column

**Action**: Columns related to the partition key cannot be encrypted.

### YAS-04841:ERR_ANK_ENCRYPT_SYS

**Message**: cannot encrypt SYS owned objects

**Action**: User SYS cannot perform encryption.

### YAS-04842:ERR_YAC_ENCRYPT_OBJ

**Message**: cannot exp encrypted objects

**Action**: Binary data export of encrypted columns using the *exp* tool is not allowed.

### YAS-04843:ERR_ANS_PARSER_MULTI_SQL_PARAM

**Message**: multiple SQL do not support binding parameter

**Action**: Binding parameters are not supported in multiple SQL statements.

### YAS-04844:ERR_ANS_PARSER_MULTI_SQL_NOT_ALLOW

**Message**: the mode do not support multiple SQL

**Action**: Please check if the multi-SQL mode of the MySQL JDBC Driver is enabled, which must be enabled to perform the current operation; it can be enabled by altering the connection parameter allowMultiQueries to TRUE.

### YAS-04845:ERR_ANS_PARSER_ERROR_SQLID_OL

**Message**: sql id outline syntax error: %s

**Action**: There is a syntax error in the SQL_ID outline; modify the SQL statement based on the information provided.

### YAS-04846:ERR_ANS_PARSER_UNSUPPORT_SQLID_OL

**Message**: sql id outline unsupport option: %s

**Action**: The SQL_ID outline syntax is not applicable; please change the object of operation to SQL_TEXT outline syntax.

### YAS-04847:ERR_ANK_FK_CONTRAINTS_ON_PARTITIONED

**Message**: foreign key on update clause is not yet supported in conjunction with partitioning

**Action**: It is not allowed to use foreign key on update cascade/on update set null for tables with user-defined partitions, including both parent and child tables.

### YAS-04848:ERR_ANS_VERIFY_HA_STANDBY_NOT_SUPPORT

**Message**: the standby database not support: %s

**Action**: The current standby machine does not support this functionality.

### YAS-04849:ERR_ANS_DATE_ADD_INTERVAL

**Message**: invalid interval argument, reason: %s

**Action**: Please troubleshoot the reason for the second parameter error in the DATE_ADD function as indicated.

### YAS-04850:ERR_ANS_PARSER_IDENTITY_COLUMN_ALWAYS

**Message**: cannot %s a generated always identity column

**Action**: Cannot insert values into a column that always generates values automatically.

### YAS-04851:ERR_ANL_SQLLDR_INVALID_COL_TERMINATE

**Message**: invalid column terminator %s

**Action**: Only single-byte CSV file delimiters are supported.

### YAS-04852:ERR_ANL_SQLLDR_INVALID_COL_ENCLOSURE

**Message**: invalid column enclosure %s

**Action**: Only single-byte CSV file enclosures are supported.

### YAS-04853:ERR_ANL_SQLLDR_INVALID_FILE_FORMAT

**Message**: invalid file format %s

**Action**: The Data File used for import needs a suffix.

### YAS-04854:ERR_ANL_SQLLDR_PARAMETER_OUT_OF_RANGE

**Message**: parameter %s is out of range

**Action**: Please modify the parameter in the options statement to meet the required values, refer to [LOAD DATA](../Development Guide/SQL Reference Manual/SQL Statements/LOAD DATA).

### YAS-04855:ERR_ANL_SQLLDR_INVALID_FIELDS_FORMAT

**Message**: invalid fields format

**Action**: The attribute definition keywords found in the Data File are invalid.

### YAS-04856:ERR_ANL_SQLLDR_INVALID_INFILE

**Message**: invalid infile

**Action**: Specify an existing CSV file name for INFILE.

### YAS-04857:ERR_ANL_SQLLDR_DEF_COL_DUPLICATED

**Message**: column %s duplicated

**Action**: Each column in the import table should be declared at most once.

### YAS-04858:ERR_ANL_SQLLDR_PARTKEY_NOT_EXISTS

**Message**: partKey does not exist

**Action**: When importing data into the partition table, all partition keys must be declared in the table.

### YAS-04859:ERR_ANL_SQLLDR_COLUMN_NUM_MISMATCH

**Message**: table column mismatch csv column

**Action**: The expected position in the Data File for this column has no data, consider using the TRAILING NULLCOLS parameter.

### YAS-04860:ERR_ANL_SQLLDR_UNKNOWN_PARAMETER

**Message**: unknown parameter name %s

**Action**: The input parameter is invalid, please refer to [LOAD DATA](../Development Guide/SQL Reference Manual/SQL Statements/LOAD DATA).

### YAS-04861:ERR_ANL_SQLLDR_EXCEED_CSV_COL_LIMIT

**Message**: the count of csv column exceeds limit %u

**Action**: The maximum number of columns for the Data File used for importing is 4096.

### YAS-04862:ERR_ANL_SQLLDR_MAX_TABLE_COUNT

**Message**: the count of table exceeds limit %u

**Action**: A single statement can support at most 16 tables for import.

### YAS-04863:ERR_ANL_SQLLDR_MAX_FILE_COUNT

**Message**: the count of input file exceeds limit %u

**Action**: A single statement can support at most 250 Data Files for import.

### YAS-04864:ERR_ANL_SQLLDR_CONDI_COL_NOT_EXISTS

**Message**: column %s does not exist in the load table %s

**Action**: The column specified in the condition clause does not appear in the table declaration of the statement.

### YAS-04865:ERR_ANL_SQLLDR_DEF_TABLE_DUPLICATED

**Message**: table %s duplicated

**Action**: Each table used for import should be declared at most once.

### YAS-04866:ERR_ANL_SQLLDR_DEF_INFILE_DUPLICATED

**Message**: input file %s duplicated

**Action**: Each Data File used for import should be declared at most once.

### YAS-04867:ERR_ANL_SQLLDR_DUPLICATE_PARAMETER

**Message**: duplicate parameter %s

**Action**: Each parameter supported in options should be declared at most once.

### YAS-04868:ERR_ANL_SQLLDR_INVALID_PARAM_VALUE

**Message**: invalid options parameter value

**Action**: The parameter value in options is invalid.

### YAS-04869:ERR_ANL_SQLLDR_CLI_GET_USER

**Message**: invalid user/password format

**Action**: Check the command statement and ensure to input the user and password in the format user/password.

### YAS-04870:ERR_ANL_SQLLDR_CLI_GET_PASSWORD

**Message**: the password length is too long

**Action**: Please enter a valid password.

### YAS-04871:ERR_ANL_SQLLDR_CLI_GET_URL

**Message**: invalid server URL

**Action**: Invalid URL path.

### YAS-04872:ERR_ANL_SQLLDR_CLI_UNKNOWN_KEYWORD

**Message**: unrecognized keyword '%s'

**Action**: Invalid keyword.

### YAS-04873:ERR_ANL_SQLLDR_CLI_UNSUPPORT_DATATYPE

**Message**: datatype %s of column %s is unsupported yet

**Action**: Unsupported data type.

### YAS-04874:ERR_ANL_SQLLDR_CLI_CTRL_NOT_FOUND

**Message**: control text not found

**Action**: Control file not found.

### YAS-04875:ERR_ANL_SQLLDR_CLI_INVALID_MODE

**Message**: invalid mode %s

**Action**: Invalid client import mode.

### YAS-04876:ERR_ANL_SQLLDR_CLI_INVALID_PACK_SIZE

**Message**: packet size is out of range

**Action**: Refer to the tool manual command guidance, modify the packet size to a value within range and rerun.

### YAS-04877:ERR_ANL_SQLLDR_CLI_INVALID_BATCH_SIZE

**Message**: batch size is out of range

**Action**: Refer to the tool manual command guidance, modify the batch size value and rerun.

### YAS-04878:ERR_ANL_SQLLDR_CLI_INIT_MEM

**Message**: memory initialization error

**Action**: Memory initialization error, please retry or contact our technical support for resolution.

### YAS-04879:ERR_ANL_SQLLDR_CLI_INIT_CODEPAGE

**Message**: init codepage error

**Action**: Codepage initialization error. Please check if the environment variables are correct and verify that the codepage files exist.

### YAS-04880:ERR_ANL_SQLLDR_DSTB_INVALID_NODE_TYPE

**Message**: invalid node type

**Action**: Invalid node type, please use CN service address or DN service address.

### YAS-04881:ERR_ANL_SQLLDR_NOT_SPLIT

**Message**: cannot set directory in nonsplit way

**Action**: The directory option is not allowed when the run_level in options is not specified as split or split_to_part.

### YAS-04882:ERR_ANL_SQLLDR_DIR_TOO_LONG

**Message**: directory path is too long, must less than %u

**Action**: The specified directory path length exceeds limits, please modify it and rerun.

### YAS-04883:ERR_ANL_SQLLDR_CLI_INVALID_VALUE

**Message**: invalid value, %s is %d

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04884:ERR_ANL_SQLLDR_INVALID_TABLE_TYPE

**Message**: invalid table type: %s

**Action**: This operation is not allowed.

### YAS-04885:ERR_ANL_SQLLDR_ILLEGAL_BATCH_CTX

**Message**: batch mode inserts illegal context

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04886:ERR_ANL_SQLLDR_ILLEGAL_BATCH_NODE

**Message**: batch mode inserts illegal node type

**Action**: Internal error, please contact our technical support for resolution.

### YAS-04887:ERR_ANL_SQLLDR_CLI_ABNORMAL_DN_NODE

**Message**: abnormal dn node exists

**Action**: Import should be done when all distributed DN master nodes are alive.

### YAS-04888:ERR_ANL_SQLLDR_CSV_LOAD_ERROR_DATA

**Message**: infile %s loads table %s error data offset %lu

**Action**: An error occurred at this offset while importing the file into the table.

### YAS-04889:ERR_ANL_SQLLDR_CSV_ERROR_DATA_POS

**Message**: infile %s exists illegal data and offset is %lu

**Action**: The data at this offset in the file is invalid.

### YAS-04890:ERR_ANL_SQLLDR_CSV_ENCLOSE_INCOMPLETE

**Message**: csv enclose incomplete

**Action**: The CSV file's enclosing characters do not match, please check for unescaped double quoted data in fields and the integrity of the enclosing characters.

### YAS-04891:ERR_ANL_SQLLDR_CSV_LINE_TERM_EXPECTED

**Message**: no line break found in csv block buffer

**Action**: No valid line break was found in the CSV file. Please check if there is any single line of data exceeding csv_line_size or if there are lines ending with only '/r'.

### YAS-04892:ERR_ANL_SQLLDR_CSV_LINE_TERM_EMBEDDED

**Message**: csv line terminates expect embedded

**Action**: Check if there are newline characters in the data, it is recommended to use with embedded.

### YAS-04893:ERR_ANL_SQLLDR_CSV_LINE_TOO_LONG

**Message**: csv line size exceeding limit %u

**Action**: A single line in the CSV file exceeds the limit of %u.

### YAS-04894:ERR_ANL_SQLLDR_CSV_COL_SPLIT_INVALID

**Message**: csv column splits invalid %s

**Action**: Please enter a valid CSV delimiter.

### YAS-04895:ERR_ANL_SQLLDR_CSV_COLUMN_BEYOND

**Message**: beyond csv file column range

**Action**: The ID of the specified CSV column must be a positive integer, please check the specified CSV column reference.

### YAS-04896:ERR_ANL_SQLLDR_CLI_INVALID_CONN_POOL_SIZE

**Message**: connection pool size is out of range

**Action**: Refer to the tool manual command guidance, modify the connection pool size value and rerun.

### YAS-04897:ERR_ANL_SQLLDR_CLI_EXCEED_ERROR_LIMIT

**Message**: %s rows number exceed limit

**Action**: Refer to the tool manual command guidance, modify the errors parameter value or fix the data errors and rerun.

### YAS-04898:ERR_ANL_SQLLDR_CLI_INFILE_EMPTY

**Message**: no files found for any INFILE specification

**Action**: The specified file name wildcard did not match any files, please confirm the file name and rerun.

### YAS-04899:ERR_ANL_SQLLDR_CSV_COL_ENCLOSURE_TERMINATOR_SAME

**Message**: csv column enclosure/terminator must be different

**Action**: Please ensure that the CSV enclosing character and delimiter are not the same.

### YAS-04900:ERR_ANL_SQLLDR_CSV_COL_SPLIT_INVALID2

**Message**: invalid csv split, contains %s

**Action**: Please enter a valid CSV delimiter.

### YAS-04901:ERR_ANS_VERIFY_OUTERREL_MANY_TABLE

**Message**: a predicate may reference only one outer-joined table

**Action**: A predicate can only reference one outer-joined table.

### YAS-04902:ERR_ANS_VERIFY_OUTERREL_MANY_COLUMN

**Message**: only one column is allowed on the size of (+)

**Action**: A predicate with the (+) syntax is only allowed to have one column on one side.

### YAS-04903:ERR_ANS_VERIFY_OUTERREL_INVALID_REL

**Message**: two tables cannot be outer-joined to each other

**Action**: Two tables cannot be outer-joined.

### YAS-04904:ERR_ANS_VERIFY_OUTERREL_INVALID_USAGE

**Message**: invalid usage of (+)

**Action**: The (+) outer join operator is only allowed to appear in the comparison predicates of where condition clauses and join on condition clauses.

### YAS-04905:ERR_ANS_VERIFY_OUTERREL_INVALID_ANSIJOIN

**Message**: old style outer join (+) cannot be used with ANSI joins

**Action**: The (+) outer join operator is not allowed to be used with ANSI joins.

### YAS-04906:ERR_ANS_VERIFY_OUTERREL_INVALID_COLUMN

**Message**: an outer join cannot be specified on a correlation column

**Action**: An outer join cannot be specified on a correlating column.

### YAS-04907:ERR_ANS_VERIFY_NOT_UDT_TABLE_COLUMN

**Message**: specified column or attribute is not a nested table type

**Action**: Define a valid Nested Table column or attribute.

### YAS-04908:ERR_ANS_VERIFY_NOT_NTAB_NO_PARTED

**Message**: cannot specify local on a non-partitioned table

**Action**: Change Nested Table to GLOBAL.

### YAS-04909:ERR_ANS_VERIFY_USER_UNSUPPORT

**Message**: users other than sys are not allowed to execute %s

**Action**: Please switch to the sys user to perform this operation.

### YAS-04910:ERR_ANS_VERIFY_MULTISET_MULTITABS

**Message**: one multi-columns clause only reference one table

**Action**: A single set expression can only reference one table.

### YAS-04911:ERR_ANS_VERIFY_MULTISET_UDT

**Message**: udt is not allowed in multi-columns update clause

**Action**: The syntax of updating multiple columns using a single set expression does not support udt types.

### YAS-04912:ERR_ANS_VERIFY_MULTI_COLS_UPDATE_SET

**Message**: multi-columns update set expression must be subquery or list

**Action**: The multi-columns update set expression must be a subquery or list.

### YAS-04913:ERR_ANS_VERIFY_RTREE_COLUMN_COUNT

**Message**: cannot create rtree index on more than one column

**Action**: Change the index column to one column and try again.

### YAS-04914:ERR_ANS_VERIFY_RTREE_COLUMN_TYPE

**Message**: unsupported data type for rtree index

**Action**: Change the specified RTree index column and try again.

### YAS-04915:ERR_ANS_VERIFY_RTREE_REVERSE

**Message**: cannot build rtree index with option reverse

**Action**: Remove reverse and try again.

### YAS-04916:ERR_ANS_VERIFY_DBLINK_TABLE_EXCL

**Message**: operations on dblink table are not allowed

**Action**: Multi-table delete, delete with subquery, delete with returning, multi-table update, update with subquery, multi-table insert scenarios cannot operate on remote db link tables. Please use db link correctly.

### YAS-04917:ERR_ANS_VERIFY_DBLINK_UNSUPPORTED

**Message**: unsupported feature on dblink, "%s"

**Action**: db link does not support this functionality. Please use db link correctly.

### YAS-04918:ERR_ANS_VERIFY_DBLINK_PARTITION

**Message**: partition extended table name cannot refer to a remote object

**Action**: Partition extended table names must not refer to remote objects via db link.

### YAS-04919:ERR_ANS_VERIFY_DBLINK_INSERT_VALUES

**Message**: insert values more than one group is invalid in %s database

**Action**: In this database, it is not supported to insert more than one group of data through insert into values.

### YAS-04920:ERR_ANS_VERIFY_DBLINK_AUTH_WITHOUT_SHARED

**Message**: only shared database links can specify authentication

**Action**: Only database links specified as SHARED can specify AUTHENTICATED.

### YAS-04921:ERR_ANS_VERIFY_MVIEW_REWRITE_EXPR_UNSUPPORT

**Message**: expression not supported for query rewrite

**Action**: This expression is not supported under materialized view rewrite options, it needs to be removed.

### YAS-04922:ERR_ANS_VERIFY_INVALID_FUN_EXPRESSION

**Message**: invalid function expression

**Action**: Invalid function expression, please refer to the correct function usage.

### YAS-04923:ERR_ANS_VERIFY_GROUPING_SETS_TYPE

**Message**: unsupported null and const in grouping sets

**Action**: Constants and NULLs are not supported in grouping sets/rollup/cube expressions.

### YAS-04924:ERR_ANS_VERIFY_ILLEGAL_SUBPARTITION_SYNTAX

**Message**: illegal subpartition-extended table name syntax

**Action**: Illegal syntax for the subpartition extended table name, please check if the table type is a secondary partition table.

### YAS-04925:ERR_ANS_VERIFY_NON_AGGR_IN_PIVOT

**Message**: non-aggregate expressions inside the PIVOT clause

**Action**: Non-aggregated functions are prohibited from appearing in the PIVOT clause, please check that part of the input.

### YAS-04926:ERR_ANS_VERIFY_NON_CONST_IN_PIVOT

**Message**: non-constant expression in PIVOT values clause

**Action**: Non-constant expressions are prohibited from appearing in the in list of the PIVOT clause, please check that part of the input.

### YAS-04927:ERR_ANS_VERIFY_PARAM_IN_PIVOT

**Message**: bind variable is not supported inside PIVOT operation

**Action**: Bind variables are not allowed to appear in the in list of the PIVOT clause, please check that part of the input.

### YAS-04928:ERR_ANS_VERIFY_PIVOT_WITH_TABLE_FUNC

**Message**: cannot PIVOT table function

**Action**: PIVOT does not allow operations with table functions, please check that part of the input.

### YAS-04929:ERR_ANS_VERIFY_SPACE_ENCRYPT

**Message**: cannot create encrypted tablespace, reason: %s

**Action**: Please check the reason for failure based on the prompt, if still unresolved, please contact our technical support for resolution.

### YAS-04930:ERR_ANS_VERIFY_DBLINK_DIFFERENT_LINK

**Message**: cannot use different database link in this situation

**Action**: While updating or inserting into a remote table, it is not allowed to use objects from different database links.

### YAS-04931:ERR_ANS_VERIFY_DBLINK_INVALID_UPDATE

**Message**: cannot use local sequence update remote table

**Action**: You are not allowed to use local sequences while updating remote tables.

### YAS-04932:ERR_ANS_VERIFY_DATAMASK_NAME_VALUES

**Message**: invalid name: %s

**Action**: The object name of the desensitization strategy rule is illegal.

### YAS-04933:ERR_ANS_VERIFY_DATAMASK_PARALIST_VALUES

**Message**: invalid paralist format

**Action**: The parameter list in the desensitization strategy rule is illegal. For PARTIAL, the number of parameters can only be 2 and cannot be null.

### YAS-04934:ERR_ANS_VERIFY_DATAMASK_ARG_NAME_VALUES

**Message**: null error: %s

**Action**: The parameter list in the desensitization strategy rule is illegal and cannot be null.

### YAS-04935:ERR_ANS_VERIFY_PERC_INVALID_ARG

**Message**: argument of function should be a constant or a function of expressions in %s

**Action**: Change the parameter to a constant or function of expressions in the GROUP BY clause.

### YAS-04936:ERR_ANS_VERIFY_COLUMN_EXPECTED

**Message**: column expression expected

**Action**: Please enter an expression of column type.

### YAS-04937:ERR_ANS_VERIFY_INVALID_INSERT_CLAUSE

**Message**: insert faild, reason: %s

**Action**: Please check the reason for failure based on the prompt, if still unresolved, please contact our technical support for resolution.

### YAS-04938:ERR_ANS_VERIFY_DATAMASK_NULL_VALUE

**Message**: invalid datamask policy,column is not null

**Action**: The desensitization strategy is erroneous; the current column cannot be desensitized to NULL, please modify the desensitization strategy accordingly.

### YAS-04939:ERR_ANS_VERIFY_VIRTUAL_COLUMN_NOT_ALLOWED

**Message**: virtual column not allowed here

**Action**: Virtual columns formed by combined expressions cannot be used here.

### YAS-04940:ERR_ANS_VERIFY_MODIFY_MULTI_VIEW_TABLE

**Message**: cannot modify more than one base table through a join view

**Action**: A view created from join statements cannot simultaneously operate on columns from different tables.

### YAS-04941:ERR_ANS_VERIFY_MODIFY_READONLY_VIEW

**Message**: cannot perform a DML operation on a read-only view

**Action**: DML operations are prohibited on read-only views.

### YAS-04942:ERR_ANS_VERIFY_MODIFY_NON_KEY_PRESERVE_VIEW

**Message**: cannot modify a column which maps to a non key-preserved table

**Action**: The column being modified does not meet the conditions for DML operations of the view.

### YAS-04943:ERR_ANS_VERIFY_VIEW_DATA_MANIPULATION_ILLEAGLE

**Message**: data manipulation operation not legal on this view

**Action**: The view statement does not meet the conditions for view DML.

### YAS-04944:ERR_ANS_VERIFY_DATAMASK_FILTER

**Message**: the datamask used in filter is not supported

**Action**: Filter operations on desensitized columns are not supported.

### YAS-04945:ERR_ANS_VERIFY_CONSTRAINT_TIMESTAMP_TZ

**Message**: column of datatype %s cannot be unique or a primary key

**Action**: This data type is not supported as a primary key or for unique constraints.

### YAS-04946:ERR_ANS_PARSER_KST_INVALID_LOCATION

**Message**: invalid or missing keystore file name

**Action**: The wallet path information is invalid; please specify a valid path.

### YAS-04947:ERR_ANS_PARSER_KST_INVALID_KEY_STR

**Message**: missing or invalid master key string

**Action**: The master key is invalid; please specify a valid master key.

### YAS-04948:ERR_ANS_PARSER_KST_INVALID_BAK_IDENT

**Message**: no backup identifier specified

**Action**: The backup path information is invalid; please specify a valid backup path.

### YAS-04949:ERR_ANS_PARSER_INVALID_IDENTITY_COLUMN_DEFINED

**Message**: table can have only one identity column

**Action**: A table can only have one identity column; please check the settings for the identity column.

### YAS-04950:ERR_ANS_PARSER_INVALID_IDENTITY_COLUMN_TYPE

**Message**: identity column must be a numeric type

**Action**: The identity column must be of numeric type; please check the data type settings of the identity column.

### YAS-04951:ERR_ANL_SQLLDR2_FILLER_NOT_EXISTS

**Message**: column %s in table %s references a non-existent field

**Action**: The field referenced by this column does not exist; please check the field.

### YAS-04952:ERR_ANL_SQLLDR2_FILLER_IS_NULL

**Message**: the field that column %s in table %s references is empty

**Action**: The field referenced by this LOB column's corresponding CSV data is empty; please check the corresponding CSV data.

### YAS-04953:ERR_ANL_SQLLDR2_FILLER_IS_CONDITION

**Message**: filler cannot be used for comparison

**Action**: Fields referenced by the LOB column cannot be used for comparison; please check the syntax for use.

### YAS-04954:ERR_ANL_SQLLDR2_LLS_INVALID_FILE

**Message**: failed to read lob file %s by lob location specifier for column %s

**Action**: Unable to read the LOB file via the column's LOB location specifier; please check the data corresponding to the LOB location specifier.

### YAS-04955:ERR_ANL_SQLLDR2_LLS_INVALID_OFFSET_SIZE

**Message**: lob location specifier for column %s invalid offset or length

**Action**: The offset and length of the LOB column are invalid; please check the LOB column's data offset and length.

### YAS-04956:ERR_ANL_SQLLDR2_LLS_IS_INVALID

**Message**: invalid lob location specifier for column %s

**Action**: Invalid LOB location specifier; please check the data corresponding to the LOB location specifier.

### YAS-04957:ERR_ANL_SQLLDR2_NOT_A_DIRECTORY

**Message**: %s is not a directory

**Action**: The specified path for the split files is not a directory; please check the import statement.

### YAS-04958:ERR_ANL_SQLLDR2_GEN_SAME_FILE

**Message**: infile %s runs the risk of generating files with same name

**Action**: The import statement may generate files with the same name; please check the import statement.

### YAS-04959:ERR_ANL_SQLLDR2_LOB_SIZE_LIMIT

**Message**: string length is %d, import it as an outline lob

**Action**: The string to be imported is too long; please use the outline LOB method for import.

### YAS-04960:ERR_ANL_SQLLDR2_DATA_SIZE_LIMIT

**Message**: string length is %d, exceeding limit %d

**Action**: The string being imported is too long, exceeding the maximum string limit.

### YAS-04961:ERR_ANL_SQLLDR2_LOB_PATH_RELATIVE

**Message**: the mode of lob path relative does not allow absolute lob path

**Action**: Using absolute paths for LOB files in lob_path_relative mode is not supported.

### YAS-04962:ERR_ANL_SQLLDR2_DUP_TAB_SPLIT

**Message**: infile can not be split according to duplicated table on cn node

**Action**: At the CN node, file splitting cannot be done based on duplicated tables.

### YAS-04963:ERR_ANL_SQLLDR2_CLI_INVALID_INPUT_NUM

**Message**: %s is out of range

**Action**: Refer to the tool manual command guidance, modify the parameter value and try again.

### YAS-04964:ERR_ANL_SQLLDR2_CLI_SENDER_INVALID

**Message**: sender count %d exceeds max thread count %d(CPU_EXTEND_FACTOR:8, DEGREE_OF_PARALLELISM:%d)

**Action**: The number of sender threads set exceeds the maximum configured thread count; reduce the sender thread count and rerun.

### YAS-04965:ERR_ANL_SQLLDR2_CLI_INVALID_PARAMETER

**Message**: invalid parameter %s, expect %s

**Action**: Please enter valid client import parameters.

### YAS-04966:ERR_ANL_SQLLDR2_CSV_COL_ENCLOSURE_INVALID

**Message**: csv column enclosure invalid %s

**Action**: Please enter a valid CSV enclosing character.

### YAS-04967:ERR_ANL_SQLLDR2_CLI_INVALID_PARAMETER_ACTION

**Message**: invalid parameter: %s(Hint: %s)

**Action**: Invalid parameter settings; refer to the hints for appropriate configurations and try again.

### YAS-04968:ERR_ANL_SQLLDR2_CLI_INVALID_CLOB_DATA

**Message**: clob data is invalid with charset %s

**Action**: CLOB data content is invalid; please verify if the character set matches.

### YAS-04969:ERR_ANL_SQLLDR2_CLI_INVALID_DATA

**Message**: invalid data because %s, offset: %ld, file: %s

**Action**: Data error, please correct the data and try importing again.

### YAS-04970:ERR_ANL_SQLLDR2_CLI_RUN_ERROR

**Message**: %s

**Action**: Modify according to the described information.

### YAS-04971:ERR_ANL_SQLLDR2_CLI_INVALID_INFILE

**Message**: invalid %s because the file is not in the directory of secure file

**Action**: Please check if the import file is in the specified secure directory; you can use SHOW PARAMETER SECURE_FILE_PRIV to view the secure directory.

### YAS-04972:ERR_ANL_SQLLDR2_CLI_DATA_CONVERSION

**Message**: column(%s) data parse failed: %s

**Action**: Please check the data content according to the error message, correct the data, and re-import.

### YAS-04973:ERR_ANL_SQLLDR2_USER_NEED_ALTER_SESSION

**Message**: user need alter session privilege

**Action**: Please grant ALTER SESSION permission to the yasldr connection user.

<span id="errno5001" name="errno5001"></span>

### YAS-05001:ERR_CRAB_MEM_ALLOC_ERROR

**Message**: can not allocate %s bytes from columnar vm buffer

**Action**: Increase the total memory configuration for columnar calculations COLUMNAR_VM_BUFFER_SIZE, COLUMNAR_BUFFER_SIZE or reduce COLUMNAR_DATA_BUFFER_PERCENT.

### YAS-05002:ERR_CRAB_INVALID_ARGUMENT

**Message**: invalid argument: %s

**Action**: Check the parameters based on the provided information.

### YAS-05003:ERR_CRAB_COLUMN_DOWNCAST_ERROR

**Message**: failed to downcast Column

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05004:ERR_CRAB_BIND_ERROR

**Message**: %s

**Action**: Modify according to the described information.

### YAS-05005:ERR_CRAB_EVALUATE_ERROR

**Message**: %s

**Action**: Modify according to the described information.

### YAS-05006:ERR_CRAB_OPERATOR_BIND_ERROR

**Message**: %s

**Action**: Modify according to the described information.

### YAS-05007:ERR_CRAB_OPERATOR_EXECUTE_ERROR

**Message**: %s

**Action**: Modify according to the described information.

### YAS-05008:ERR_CRAB_UNSUPPORTED_FEATURE

**Message**: unsupported feature: %s

**Action**: The current operation is not supported; please check the SQL statement or the SQL execution environment.

### YAS-05009:ERR_CRAB_INTERRUPTED

**Message**: interrupted: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05010:ERR_CRAB_CODEC_ERROR

**Message**: codec error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05011:ERR_CRAB_DECIMAL_ERROR

**Message**: %s

**Action**: Modify according to the described information.

### YAS-05012:ERR_CRAB_DATE_TIME_ERROR

**Message**: %s

**Action**: Modify according to the described information.

### YAS-05013:ERR_CRAB_IO_ERROR

**Message**: io error

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05014:ERR_CRAB_CAST_ERROR

**Message**: %s

**Action**: Modify according to the described information.

### YAS-05015:ERR_CRAB_JSON_ERROR

**Message**: %s

**Action**: Modify according to the described information.

### YAS-05016:ERR_CRAB_ALLOC_MATERIAL_QUOTA_ERROR

**Message**: %s

**Action**: Insufficient materialization memory; consider increasing COLUMNAR_VM_BUFFER_SIZE or COLUMNAR_MATERIAL_PERCENT to resolve.

### YAS-05017:ERR_CRAB_FORMAT_ERROR

**Message**: format error

**Action**: System available memory is insufficient; adjust memory configuration to reduce memory usage, or contact our technical support for resolution.

### YAS-05199:ERR_CRAB_INFALLIBLE

**Message**: infallible

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05201:ERR_PL_IMPCUR_INVALID

**Message**: invalid implicit cursor

**Action**: Check the syntax of the implicit cursor.

### YAS-05202:ERR_PL_BLOCK_EMPTY

**Message**: empty block

**Action**: The control flow block cannot be empty; it must have executable statements or be filled with null.

### YAS-05203:ERR_PL_ASSIGN_INVALID

**Message**: %s cannot be used as an assignment target

**Action**: The parameter cannot be assigned a value.

### YAS-05204:ERR_PL_INVALID_BOUND

**Message**: invalid bound of %s

**Action**: Check if the index for specific rows like the for loop is illegal or the range is illegal.

### YAS-05205:ERR_PL_TOO_MANY_ROWS

**Message**: exact fetch returns more than requested number of rows

**Action**: The data specified in the PL select query cannot return multiple rows; modify the statement to ensure it returns a single row result, or use cursor-based queries.

### YAS-05206:ERR_PL_NO_DATA_FOUND

**Message**: no data found

**Action**: The data query in the stored procedure returned no results.

### YAS-05207:ERR_PL_USER_EXCEPTION

**Message**: unhandled user defined exception

**Action**: An undefined exception occurred.

### YAS-05208:ERR_PL_INVALID_EXCEPTION

**Message**: invalid exception

**Action**: Invalid exception definition syntax; please check the statement.

### YAS-05209:ERR_PL_UNEXPECTED_SO_BLOCK

**Message**: the current block is %s, the previous block should not be the %s

**Action**: Syntax error in the control flow statement used.

### YAS-05210:ERR_PL_CASE_NOT_FOUND

**Message**: case not found

**Action**: The case when statement used in the stored procedure must have at least one branch that meets the condition.

### YAS-05211:ERR_PL_INVALID_SO_NAME

**Message**: missing or invalid stored-object name

**Action**: The name of the function or procedure does not conform to syntax requirements.

### YAS-05213:ERR_PL_INVALID_ARGUMENT

**Message**: invalid argument

**Action**: The parameter needs to match.

### YAS-05215:ERR_PL_CONCURRENCY_DDL

**Message**: stored-object has been dropped or altered

**Action**: The executed stored procedure has changed or has been deleted; re-execute or recreate it.

### YAS-05217:ERR_PL_MISS_ARGUMENT

**Message**: missing no-default argument %s

**Action**: This parameter does not have a default value.

### YAS-05218:ERR_PL_INVALID_OUTPARAM

**Message**: variable or bind variable expected for out/inout parameter

**Action**: Use a variable or binding variable as the output parameter.

### YAS-05219:ERR_PL_TOO_MANY_ARGS

**Message**: too many arguments

**Action**: The number of parameters for stored procedures or user-defined function javaFunction should not exceed the maximum value, which is currently 4096.

### YAS-05220:ERR_PL_RESOURCE_IS_BUSY

**Message**: resource is busy, try later

**Action**: The resource being used in a concurrent process is occupied by another session; please try again later.

### YAS-05221:ERR_PL_DDL_OTHERTYPE

**Message**: cannot %s %s type duplicate stored-object

**Action**: According to the error information, the name conflicts with an existing stored procedure object.

### YAS-05222:ERR_PL_INVALID_OUTPARAM_DFT

**Message**: assignment target does not allow default expression

**Action**: A variable declared as an out/int out type cannot have a default value.

### YAS-05223:ERR_PL_INVALID_GOTO

**Message**: invalid goto statement cannot jump to label %s

**Action**: Check the syntax of the goto statement; it does not meet the requirements.

### YAS-05224:ERR_PL_INVALID_LABEL

**Message**: invalid label %s

**Action**: The position of the label is illegal; please check the label constraints.

### YAS-05225:ERR_PL_DUPLICATED_LABEL

**Message**: duplicated label %s

**Action**: There cannot be duplicate label names.

### YAS-05226:ERR_PL_INVALID_RETURN

**Message**: invalid return

**Action**: Only the body of a function is allowed to have a return statement.

### YAS-05227:ERR_PL_INVALID_OUTARG

**Message**: invalid out argument function

**Action**: Check if variables or binding variables are declared in valid positions; position constraints refer to the relevant documents.

### YAS-05228:ERR_PL_NULL_RETURN

**Message**: function must has return sql

**Action**: The defined function lacks a return statement.

### YAS-05229:ERR_PL_EXCEPTION_NAME_DUP

**Message**: the %s only appears once in same exception

**Action**: Check if the name appears multiple times within the same exception.

### YAS-05230:ERR_PL_EXCEPTION_OTHERS_EXIST

**Message**: no choices may appear with choice OTHERS in an exception handler

**Action**: OTHERS cannot be in the same handler as any other exceptions; either remove other exceptions or split them into different handlers.

### YAS-05231:ERR_PL_EXCEPTION_OTHERS_POS

**Message**: OTHERS must be at the end of the exception

**Action**: The exception syntax does not meet the requirements; OTHERS must appear at the end of the exception.

### YAS-05232:ERR_PL_ACTIVE_AUTON_TRANS

**Message**: active autonomous transaction detected and rolled back

**Action**: A commit or rollback must be entered when the autonomous transaction ends.

### YAS-05233:ERR_PL_INVALID_VAR_BINDING

**Message**: %s variable binding to %s position

**Action**: In-out parameter binding does not match.

### YAS-05234:ERR_PL_PARAM_NUM_DISMATCH

**Message**: parameter number %d mismatch using number %d

**Action**: Change to matching binding parameters.

### YAS-05235:ERR_PL_UDF_FUNC_DML_DDL

**Message**: cannot perform a DML or DDL or DCL inside a query or DML

**Action**: The body of the procedure body does not support non-dml and queries. Alternatively, display commit or rollback in an autonomous transaction containing dml and queries.

### YAS-05236:ERR_PL_UDF_FUNC_DML_TABLE

**Message**: the table has changed and triggers/functions cannot read it

**Action**: The table structure has changed, and it cannot be updated or deleted.

### YAS-05237:ERR_PL_INVALID_DYN_SQL

**Message**: dynamic sql should be string and not null

**Action**: Please check if the result of executing the dynamic SQL expression is a non-empty string.

### YAS-05238:ERR_PL_TOO_MANY_SO

**Message**: maximum number of stored-objects is % PRIu32

**Action**: The used number of stored procedures cannot exceed the maximum value.

### YAS-05239:ERR_PL_INVALID_RECURSIVE_SO

**Message**: invalid recursive call stored-object %s

**Action**: Check the syntax for recursive call stored procedures.

### YAS-05241:ERR_PL_INVALID_VALUES_CLAUSE

**Message**: invalid values clause of an insert statement

**Action**: The insert statement that inserts one record does not meet the specifications.

### YAS-05242:ERR_PL_INVALID_CURSOR

**Message**: invalid cursor

**Action**: Check the cursor syntax.

### YAS-05243:ERR_PL_INVALID_OPEN

**Message**: invalid open

**Action**: Check the open syntax.

### YAS-05244:ERR_PL_TOO_MANY_OPEN_CURSOR

**Message**: open too many cursor

**Action**: Too many cursors are opened simultaneously; the limit for open cursors at one time is 300. Please rewrite PL statements to close unused cursors or reduce the number of open cursors.

### YAS-05245:ERR_PL_RAISE_WITHOUT_EXCEP

**Message**: The raise statement without an exception must be set in exception handler

**Action**: The SQL statement "raise;" must have an exception module to handle the thrown exception.

### YAS-05246:ERR_PL_INVALID_TYPE

**Message**: invalid %s%%%s

**Action**: Invalid use of %type or %rowtype syntax, please check the statement.

### YAS-05247:ERR_PL_INVALID_CURSOR_RETURN

**Message**: the value cursor return mismatch the value sql return

**Action**: The number of record members returned by the cursor does not match the number of projections in the SQL statement bound to the cursor; please check the used statement.

### YAS-05248:ERR_PL_INVALID_CURSOR_ASSIGN

**Message**: explicit cursor cannot assign or be assigned

**Action**: Implicit cursors cannot be assigned or assigned to other objects.

### YAS-05249:ERR_PL_CURSOR_ALREADY_OPEN

**Message**: cursor already open

**Action**: The cursor is already open; do not open it again.

### YAS-05250:ERR_PL_PARAM_NOT_FOUND

**Message**: %s does not exist

**Action**: Check if the binding parameters exist.

### YAS-05251:ERR_PL_SUBTYPE_NOT_NULL

**Message**: subtype of a NOT NULL type must also be NOT NULL

**Action**: A NOT NULL type's subtype cannot have a NULL constraint.

### YAS-05252:ERR_PL_EXPECTED_INITIALIZATION

**Message**: a variable declare not null must have an not null initialization assignment

**Action**: A variable declared as NOT NULL must have an initial value which cannot be null.

### YAS-05253:ERR_PL_INVALID_CURSOR_SYSVAR

**Message**: invalid system variable for cursor

**Action**: The cursor uses an invalid system value.

### YAS-05254:ERR_PL_CURSOR_NOT_OPEN

**Message**: cursor not open

**Action**: The cursor cannot be used if it is not open.

### YAS-05255:ERR_PL_INVALID_LOOP_CTRL

**Message**: invalid EXIT/CONTINUE statement, it must appear inside a loop

**Action**: The syntax for using EXIT/CONTINUE is incorrect; it must be used within a loop.

### YAS-05256:ERR_PL_INVALID_LOOP_CTRL_LABEL

**Message**: invalid EXIT/CONTINUE label %s

**Action**: Check if the EXIT/CONTINUE label has the same name as another label or is positioned illegally.

### YAS-05257:ERR_PL_INVALID_END_LOOP_LABEL

**Message**: invalid END LOOP label %s, it must match the loop

**Action**: Check if the label name after END LOOP matches the correct loop.

### YAS-05258:ERR_PL_INVALID_UDT_VALUE

**Message**: invalid user define type value %s

**Action**: User-defined types appear in illegal positions; user-defined types cannot be used for comparisons, operator actions, etc.

### YAS-05259:ERR_PL_CURSOR_ARG_MISMATCH

**Message**: cursor argument mismatch

**Action**: Please check if the cursor parameters match.

### YAS-05260:ERR_PL_INVALID_RECORD_MEMTYPE

**Message**: invalid record member type %s

**Action**: Cursor types cannot be members of record type definitions.

### YAS-05261:ERR_PL_CURSOR_DUP_ALIAS

**Message**: alias required in select list of cursor to avoid duplicate column

**Action**: There are duplicate columns in the list defining the cursor; please define an alias if there are duplicate columns.

### YAS-05262:ERR_PL_INVALID_TRIGGER_NAME

**Message**: missing or invalid trigger name

**Action**: Redefine the trigger name according to naming conventions.

### YAS-05263:ERR_PL_TRIGGER_WHEN_NOTMATCH

**Message**: WHEN clause cannot be used with table level triggers

**Action**: Remove the WHEN clause or define the trigger at the row level.

### YAS-05264:ERR_PL_TRIGGER_DEF_TOO_LONG

**Message**: trigger description too long, move comments into triggering code

**Action**: Shorten the definition description of the trigger. If there are comments, they can be placed in the action of the trigger.

### YAS-05265:ERR_PL_TRIGGER_REF_INVALID

**Message**: NEW or OLD references not allowed in table level triggers

**Action**: Remove NEW or OLD references or define the trigger at the row level.

### YAS-05266:ERR_PL_TRIGGER_REF_UNDEF

**Message**: invalid NEW or OLD specification %s

**Action**: Use the correct correlation name for NEW or OLD.

### YAS-05267:ERR_PL_TRIGGER_LN_UNSUPPORT

**Message**: cannot %s in a trigger

**Action**: Rewrite the trigger without including commit, rollback, or savepoint.

### YAS-05268:ERR_PL_INVALID_CURSOR_DEFAULT

**Message**: invalid system variable for cursor default

**Action**: Check the cursor parameters; the default value uses disallowed system variables.

### YAS-05269:ERR_PL_TRIGGER_DEF_COMPILING

**Message**: trigger compiling errors:

**Action**: Fix the errors according to the specific information provided.

### YAS-05270:ERR_PL_TRIGGER_OF_TYPE

**Message**: cannot specify this column in UPDATE OF clause

**Action**: Remove unsupported LOB columns from the UPDATE OF clause.

### YAS-05271:ERR_PL_JOB_ALREADY_RUNNING

**Message**: job is running

**Action**: Wait for the current job to finish or use the DBMS_SCHEDULER.STOP_JOB interface to stop the job before rerunning the command.

### YAS-05272:ERR_PL_INVALID_JOB_ATTR_NAME

**Message**: invalid attribute name %s

**Action**: Use the correct job attribute name.

### YAS-05273:ERR_PL_INVALID_JOB_ATTR_VALUE

**Message**: invalid value %s for attribute %s

**Action**: Use a valid job attribute value.

### YAS-05274:ERR_PL_INVALID_JOB_TYPE

**Message**: invalid job type %s

**Action**: Use the correct job type.

### YAS-05275:ERR_PL_TOO_MANY_PACK_ITEMS

**Message**: too many package children

**Action**: The number of PL package internal variables, types, subprogram bodies etc. cannot exceed the maximum of 1024, please reduce the number of members defined in the package.

### YAS-05276:ERR_PL_INVALID_PACKHEAD

**Message**: package head definition invalid or conflict

**Action**: Please check the syntax of the package header definition.

### YAS-05277:ERR_PL_INVALID_PACK_DECLARE

**Message**: declaration cannot after sub program

**Action**: Definitions need to be before subprogram bodies.

### YAS-05278:ERR_PL_PACKBODY_COMPILE

**Message**: cannot compile body without its specification

**Action**: The package body must be defined before it can be compiled.

### YAS-05279:ERR_PL_PACKHEAD_INITSECTION

**Message**: package head cannot have initialized section

**Action**: The package head cannot have initializations.

### YAS-05280:ERR_PL_PACK_CONFLICT_HEADBODY

**Message**: conflicting declaration and definition found for package item "%s"

**Action**: There cannot be conflicting definitions and definitions in the package head and body.

### YAS-05281:ERR_PL_INVALID_SOVAR

**Message**: invalid stored-object variable

**Action**: Invalid stored procedure variable.

### YAS-05282:ERR_PL_SUBPROGRAM_COMPILING

**Message**: package %s compiling "%s" errors:

**Action**: Check for compilation errors in the subprogram or body according to the prompts.

### YAS-05283:ERR_PL_PACK_INVALID_CURSOR

**Message**: invalid package cursor variable "%s" declared

**Action**: Do not define cursor variables inside the package.

### YAS-05284:ERR_PL_PACK_NOSUBPROGRAM_BODY

**Message**: cannot find sub program "%s" in body

**Action**: The subprogram body is not found in the body.

### YAS-05285:ERR_PL_PACK_INVALID_REF_NAME

**Message**: cannot reference "%s" in package

**Action**: Only supports [schema.][package.]subprogram_name; check the usage syntax.

### YAS-05286:ERR_PL_ILLEGAL_TYPECONV

**Message**: illegal conversion

**Action**: Modify the assignment statement according to [Data Type Conversion](../Development Guide/SQL Reference Manual/Data Types/Data Type Conversion).

### YAS-05287:ERR_PL_TRIGGER_OUT_OLD

**Message**: cannot change the value of an OLD reference variable

**Action**: Remove modifications to OLD variable values.

### YAS-05288:ERR_PL_TRIGGER_OUT_NEW_AFTER

**Message**: cannot change NEW values for this trigger type

**Action**: Change the trigger type or remove modifications to NEW variable values.

### YAS-05289:ERR_PL_INVALID_TRIGGER_SYNTAX

**Message**: invalid trigger specification

**Action**: Modify the syntax for creating the trigger correctly.

### YAS-05290:ERR_PL_PACK_DUP_ITEM

**Message**: duplicate item "%s" in package

**Action**: There cannot be duplicate elements in the package.

### YAS-05291:ERR_PL_INVALID_EXT_JAVA_DATA_TYPE

**Message**: invalid external java procedure dataType %s

**Action**: An unsupported data type was used when creating a procedure or function with udf java.

### YAS-05292:ERR_PL_INVALID_EXT_JAVA_SIG

**Message**: invalid external java procedure signature %s

**Action**: The signature declared for the function in the stored procedure supporting Java has illegal syntax.

### YAS-05293:ERR_PL_JNI_CREATE_JAVA_VM_ERROR

**Message**: JNI create java VM error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05294:ERR_PL_JAVA_LIB_NOT_EXISTS

**Message**: java lib %s is non-existent

**Action**: The specified lib path used in udf java has not been loaded with loadjava; please use loadjava to load it first.

### YAS-05295:ERR_PL_JAVA_METHOD_NOT_EXISTS

**Message**: java method %s is non-existent

**Action**: javaFunction does not exist; please use the correct function.

### YAS-05296:ERR_PL_DESTROY_JAVA_VM_ERROR

**Message**: JNI destroy java VM error

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05297:ERR_PL_JAVA_VM_ATTACH_ERROR

**Message**: java VM failed to attach current thread

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05298:ERR_PL_JAVA_VM_LOAD_CLASS_ERROR

**Message**: java VM load class %s error

**Action**: Check if the class being used has already been loaded, or if there is an internal error, please contact our technical support for resolution.

### YAS-05299:ERR_PL_JAVA_EXCPTION

**Message**: Java call terminated by uncaught Java exception:

**Action**: Captured Java exception; please see exception print for details.

### YAS-05300:ERR_PL_JAVA_LOAD_CLASS_ERROR

**Message**: %s is already loaded, unload class first before reload it

**Action**: The same class file or jar cannot be loaded repeatedly; if needed, first call dropjava to unload it, and then reload.

### YAS-05301:ERR_PL_INVALID_NULL_ARG

**Message**: %s, argument cannot be null

**Action**: Parameters for loadjava and dropjava must be non-empty strings.

### YAS-05302:ERR_PL_JAVA_DROP_ERROR

**Message**: invalid dropjava %s, not loaded

**Action**: DBMS_STANDARD.dropjava cannot be called to unload a class file or jar that has not been loaded.

### YAS-05303:ERR_PL_JAVA_INVALID_LIB_PATH

**Message**: invalid lib path, length should be more than 0, no more than %d

**Action**: The parameters for the class or jar path for loadjava and dropjava must be less than the specified maximum.

### YAS-05304:ERR_PL_JAVA_ENV_NOT_FOUND

**Message**: java run environment is not found, install first before using

**Action**: The user environment has not installed the JDK; please install the Java runtime environment to support external UDF in the Java language.

### YAS-05305:ERR_PL_JAVA_LOAD_TOO_MANY

**Message**: load class or jar exceeding %d at same time

**Action**: The number of classes or jars loaded simultaneously exceeds 64; please unload some before loading again.

### YAS-05306:ERR_PL_JAVA_INVALID_LOAD_ERROR

**Message**: invalid java lib path %s

**Action**: Set the correct Java library path.

### YAS-05307:ERR_PL_PACK_INVALID_SERIALLY

**Message**: pragma SERIALLY_REUSABLE must be declared in package specification and body

**Action**: The SERIALLY_REUSABLE property must be defined in both the head and body.

### YAS-05308:ERR_PL_PACK_INVALID_ENTITY

**Message**: invalid package %s "%s"

**Action**: The body is invalid; please attempt to recreate the body. If the head is invalid, check if the PL POOL usage is too high.

### YAS-05309:ERR_PL_CURSOR_EXPECTED

**Message**: cursor expected when using cursor attribute

**Action**: Using cursor attributes must be cursor variables; please check the input statement.

### YAS-05310:ERR_PL_PACK_INVALID_TYPEDEF

**Message**: package type definition has been dropped or altered

**Action**: The package type definition has been deleted or modified; please re-execute the SQL statement.

### YAS-05311:ERR_PL_JOB_INVALID_ENDDATE

**Message**: job has an invalid END_DATE

**Action**: The job's END_DATE is invalid (may have expired); please set the END_DATE first and then re-execute the SQL statement.

### YAS-05312:ERR_PL_TOO_MANY_OPEN_CURSOR_REFS

**Message**: maximum number of open cursor references is %d

**Action**: Too many references pointing to the same cursor; please check the input statement.

### YAS-05313:ERR_PL_JOB_INVALID_REENABLE

**Message**: failed to re-enable "%s"."%s" after making requested change

**Action**: Modify the job's attributes to valid values based on the prompts, and then enable it.

### YAS-05314:ERR_PL_PACK_COMPILING

**Message**: package %s compiling "%s" errors:

**Action**: Enter legal package statements based on the prompt information and re-execute.

### YAS-05315:ERR_PL_DYNAMIC_OPEN_INVALID

**Message**: cursor with return cannot open dynamic sql

**Action**: Cursor variables explicitly defined with return values cannot be used to open dynamic SQL; please change the cursor variable used to open the statement.

### YAS-05316:ERR_PL_INVALID_DIRECT_FOR_CURSOR

**Message**: out, in out not allowed when open dynamic sql for cursor

**Action**: Please modify the parameter direction in the using clause according to the prompts; they do not meet the restrictions.

### YAS-05317:ERR_PL_CURSOR_INVALID_SQL_TYPE

**Message**: target of open must be a query

**Action**: The type of the open statement is invalid; do not open non-query statements in the open line.

### YAS-05318:ERR_PL_EXT_PROC_INVALID_DEFAULT

**Message**: CALL Specification parameters cannot have default

**Action**: External UDF does not support default expressions; please remove the default expression and retry.

### YAS-05319:ERR_PL_INVALID_METADATA_DDL_TYPE

**Message**: invalid create ddl type

**Action**: The create DDL type is invalid; please enter a valid type.

### YAS-05320:ERR_PL_JAVA_INVALID_XM_SIZE

**Message**: init JVM error: Initial heap size set to a larger value than the maximum heap size

**Action**: Configuration parameter JVM_XMS needs to be smaller than JVM_XMX; please reset the configuration.

### YAS-05321:ERR_PL_WRAP_CORRUPT_WRAPPED_UNIT

**Message**: "malformed or corrupted wrapped unit"

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05322:ERR_PL_JAVA_INVALID_CLASS_NAME

**Message**: invalid class name, length should be less than %d

**Action**: The class name length must be less than the stipulated maximum value when creating external UDF.

### YAS-05323:ERR_PL_CONSTANT_INITIALIZATION

**Message**: a variable declare constant must have an initialization assignment

**Action**: Please initialize the defined constant.

### YAS-05324:ERR_PL_CONSTANT_CURSOR

**Message**: cursor cannot use constant variable

**Action**: Please do not use constants for cursors.

### YAS-05325:ERR_PL_OBJTYPE_DUP_METHOD

**Message**: duplicate method "%s"

**Action**: The UDT contains duplicate methods.

### YAS-05326:ERR_PL_OBJTYPE_DEPEND_ERROR

**Message**: cannot drop or replace a type with type or table dependents

**Action**: Please delete the tables and types that depend on the current type first and try again. You can execute `SELECT * FROM DBA_DEPENDENCIES WHERE TYPE IN ('TABLE', 'TYPE') AND REFERENCED_TYPE = 'TYPE'` to get related tables or types.

- If the error occurs when dropping a USER, the WHERE clause also needs to add the `REFERENCED_OWNER = username` condition.

- If the error occurs when dropping/replacing a TYPE, the WHERE clause also needs to add the `REFERENCED_OWNER = username AND REFERENCED_NAME = typename` condition.

### YAS-05327:ERR_PL_OBJTYPE_INVALID_METHOD

**Message**: subprogram "%s" is declared in an object type body and must be defined in the object type specification

**Action**: Methods declared in the body of an OBJECT type must be declared in the head section.

### YAS-05328:ERR_PL_INVALID_OBJTYPE_NAME

**Message**: missing or invalid object type name

**Action**: The defined object type name is illegal.

### YAS-05329:ERR_PL_OBJTYPE_ATTRLIST_NULL

**Message**: no attributes found in object type "%s"

**Action**: Accessing a non-existent OBJECT member.

### YAS-05330:ERR_PL_OBJTYPE_NOTEXIT_DROP

**Message**: attribute or method by name "%s" does not exist

**Action**: Accessed an invalid OBJECT member variable or method name.

### YAS-05331:ERR_PL_OBJTYPE_CASCADE_REQUIRED

**Message**: must specify either CASCADE or INVALIDATE option

**Action**: Missing the CASCADE or INVALIDATE keyword.

### YAS-05332:ERR_PL_OBJECT_INCOMPLETE

**Message**: object "%s" is invalid

**Action**: Accessed an invalid OBJECT type.

### YAS-05333:ERR_PL_OBJTYPE_ISFINAL

**Message**: attempting to create a subtype UNDER a FINAL type

**Action**: Please do not inherit from an OBJECT type declared as FINAL.

### YAS-05334:ERR_PL_OBJECT_NOTEXIST

**Message**: not executed, %s "%s" does not exist

**Action**: Used an object that has only been declared.

### YAS-05335:ERR_PL_OBJTYPE_MEDNAME_INVALID

**Message**: only constructor method name can match type name

**Action**: Built-in methods cannot have the same name as non-constructor methods.

### YAS-05336:ERR_PL_OBJTYPE_NODECLARED

**Message**: identifier "%s" must be declared

**Action**: Accessed a member that has not been declared.

### YAS-05337:ERR_PL_OBJTYPE_CONSTRUCTOR_NOMCH

**Message**: constructor method name must match type name

**Action**: The constructor method of an OBJECT type must have the same name as the type itself.

### YAS-05338:ERR_PL_OBJECT_NOTFIND

**Message**: could not find program unit being called "%s"

**Action**: Create a method corresponding to the name.

### YAS-05339:ERR_PL_OBJTYPE_TAB_DEPEND_ERROR

**Message**: cannot replace a type with table dependents

**Action**: First, delete the tables using the TYPE before executing the operation.

### YAS-05340:ERR_PL_OBJTYPE_MAP_ORDER_NULL

**Message**: cannot ORDER objects without MAP or ORDER method

**Action**: Define MAP or ORDER methods for the OBJECT type.

### YAS-05341:ERR_PL_INVALID_VARRAY_LIMIT

**Message**: varray limit require a positive constant input in [1, %d]

**Action**: Provide a number that fits within the specified range for the limit clause of the VARRAY type.

### YAS-05342:ERR_PL_INVALID_VARRAY_INIT

**Message**: The number of initializations for varray exceeds the limit

**Action**: Reduce the parameters of the VARRAY construction function, not exceeding the VARRAY limit.

### YAS-05343:ERR_PL_INVALID_VARRAY_METHOD

**Message**: varray member function %s is not allowed here

**Action**: Use a function with a return value.

### YAS-05344:ERR_PL_INVALID_VARRAY_INDEX

**Message**: varray index %s

**Action**: Use valid index values for VARRAY.

### YAS-05345:ERR_PL_UNINIT_OBJECT

**Message**: Reference to uninitialized %s

**Action**: Initialize OBJECT via the constructor function before use.

### YAS-05346:ERR_PL_INVALID_VARRAY_EXTEND

**Message**: varray extend invalid, %s

**Action**: Adjust the parameters of the EXTEND method according to the information.

### YAS-05347:ERR_PL_INVALID_VARRAY_TRIM

**Message**: varray trim invalid, %s

**Action**: Adjust the parameters of the TRIM method based on the information.

### YAS-05348:ERR_PL_INVALID_VARRAY

**Message**: varray assign invalid

**Action**: Use valid VARRAY values for assignment.

### YAS-05349:ERR_PL_OBJTYPE_ORDER_ROOT

**Message**: MAP or ORDER method must be defined in the root of the subtype hierarch

**Action**: Define MAP or ORDER methods in the ROOT type.

### YAS-05350:ERR_PL_OBJTYPE_MAP_SCALAR

**Message**: MAP methods must return a scalar type

**Action**: Modify the MAP method to return a scalar type.

### YAS-05351:ERR_PL_OBJTYPE_ORDER_ONE

**Message**: ORDER methods must be declared with 1 (one) parameter in addition to (optional) SELF

**Action**: Modify the ORDER method to have only one parameter besides SELF.

### YAS-05352:ERR_PL_OBJTYPE_ORDER_RET

**Message**: ORDER methods must return an INTEGER

**Action**: Modify the ORDER method to return an INTEGER type.

### YAS-05353:ERR_PL_OBJTYPE_ORDER_TYPE

**Message**: The parameter type in an ORDER method must be the containing object type

**Action**: Change the type of the SELF parameter in the ORDER method to be the currently defined OBJECT type.

### YAS-05354:ERR_PL_OBJTYPE_OBJECT_BODY

**Message**: not an OBJECT type

**Action**: Create a BODY with the same name as the OBJECT type or create a BODY for the OBJECT type.

### YAS-05355:ERR_PL_OBJTYPE_MAP_ZERO

**Message**: MAP methods must be declared without any parameters other than (optional) SELF

**Action**: Modify the MAP methods to only include SELF as the parameter.

### YAS-05356:ERR_PL_OBJTYPE_ORDERMED_ONE

**Message**: An object type may have only 1 MAP or 1 ORDER method

**Action**: Remove the MAP or ORDER method until only one remains.

### YAS-05357:ERR_PL_OBJTYPE_UNDEF_METHOD

**Message**: subprogram or cursor "%s" is declared in an object type specification and must be defined in the object type body

**Action**: Implement all methods of the OBJECT in the TYPE BODY.

### YAS-05358:ERR_PL_OBJTYPE_SELF_DIRECT

**Message**: the SELF parameter can be declared only as IN or as IN OUT

**Action**: Change the SELF variable to be either IN or IN OUT.

### YAS-05359:ERR_PL_OBJTYPE_SELF_TYPE

**Message**: expression is of wrong type

**Action**: Change the type of the SELF variable to be the currently defined type.

### YAS-05360:ERR_PL_OBJTYPE_SUPER_OBJ

**Message**: supertype must be an object type

**Action**: Create a corresponding OBJECT type as the supertype or create a subtype under the OBJECT type.

### YAS-05361:ERR_PL_OBJTYPE_OBJTAB_NULL

**Message**: cannot insert NULL object into object tables or nested tables

**Action**: Insert a non-null value.

### YAS-05362:ERR_PL_OBJTYPE_INVALID_CONSTUCT

**Message**: invalid constructor

**Action**: Enter a valid VARRAY constructor function.

### YAS-05363:ERR_PL_OBJTYPE_OBJECT_LIMIT

**Message**: maximum number of %s is %d

**Action**: Reduce the number of attributes or methods.

### YAS-05364:ERR_PL_OBJTYPE_MODIFIER_CONFICTS

**Message**: modifier "%s" conflicts with prior "%s" specification

**Action**: Remove one of the conflicting definitions.

### YAS-05365:ERR_PL_OBJTYPE_MODIFIER_DUP

**Message**: duplicate modifier specification "%s"

**Action**: Remove the duplicate definition.

### YAS-05366:ERR_PL_OBJTYPE_MODIFIER_INST

**Message**: type with NOT INSTANTIABLE methods must be declared NOT INSTANTIABLE

**Action**: Define the OBJECT as NOT INSTANTIABLE or remove the NOT INSTANTIABLE methods.

### YAS-05367:ERR_PL_OBJTYPE_FINAL_INST

**Message**: creating a FINAL NOT INSTANTIABLE type

**Action**: Remove either the FINAL or NOT INSTANTIABLE keyword.

### YAS-05368:ERR_PL_OBJTYPE_STATIC_SELF

**Message**: a static method cannot declare a parameter named SELF

**Action**: Remove the SELF parameter from static methods.

### YAS-05369:ERR_PL_OBJTYPE_PARAM_DEFAULT

**Message**: default value of parameter "%s" in body must match that of spec

**Action**: Modify the default values of parameters in the BODY to match those declared in the OBJECT.

### YAS-05370:ERR_PL_OBJTYPE_ARG_DIRECT

**Message**: The parameters to an %s function must have %s mode

**Action**: Change the parameters of the ORDER method to be either IN or IN OUT.

### YAS-05371:ERR_PL_OBJTYPE_CSTTR_INOUT

**Message**: SELF parameter of constructor method must be IN OUT

**Action**: Change the SELF parameter of the custom constructor function to IN OUT.

### YAS-05372:ERR_PL_OBJTYPE_CSTTR_RETURN

**Message**: RETURN statement in a CONSTRUCTOR cannot include an expression

**Action**: Remove the RETURN clause from the custom constructor function.

### YAS-05373:ERR_PL_OBJTYPE_MEM_SELF_NULL

**Message**: method dispatch on NULL SELF argument is disallowed

**Action**: Provide a non-null value for the SELF parameter when calling the OBJECT method.

### YAS-05374:ERR_PL_VARRAY_EMBED_LOB

**Message**: cannot create VARRAY column with embedded LOB

**Action**: Array elements in VARRAY type columns or columns containing VARRAY type attributes cannot be LOB, BFILE, JSON, or XMLTYPE types.

### YAS-05375:ERR_PL_UDT_RECURSIVE

**Message**: type "%s" is malformed because it is a non-REF mutually recursive type

**Action**: Remove the recursive reference to the current type.

### YAS-05376:ERR_PL_INVALID_VARRAY_CONSTRUCT

**Message**: expression datatype does not match varray member type

**Action**: Modify the parameters of the VARRAY constructor function to match the definitions of the VARRAY members.

### YAS-05377:ERR_PL_UDT_DEP_CYCLE

**Message**: "%s" formed a non-REF mutually-dependent cycle

**Action**: Remove the usage of cyclical types.

### YAS-05378:ERR_PL_INTERNAL_ERROR

**Message**: PL/SQL internal error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05379:ERR_PL_INVALID_FOR_CURSOR

**Message**: explicit cursor expected

**Action**: An explicit cursor (non-implicit) must be used after using cursors in the for loop.

### YAS-05380:ERR_PL_INVALID_ALTER_ATTR

**Message**: invalid alter compiler parameters clause "%s" error %s

**Action**: Modify the invalid configuration parameters according to the prompt.

### YAS-05381:ERR_PL_DUPLICATED_ALTER_ATTR

**Message**: duplicate alter compiler parameters clause "%s"

**Action**: Remove duplicate compilation options.

### YAS-05382:ERR_PL_INVALID_OPTIMIZE_LEVEL

**Message**: invalid value %d for parameter plsql_optimize_level, must be from among 0, 1, 2, 3

**Action**: Enter valid PL optimization levels 0, 1, 2, or 3.

### YAS-05383:ERR_PL_DUPLICATE_SETTING_DEBUG

**Message**: duplicate setting for PL/SQL compiler parameter plsql_debug

**Action**: Remove duplicate DEBUG options.

### YAS-05384:ERR_PL_INVALID_PLSCOPE_SETTINGS

**Message**: Invalid value for parameter plscope_settings. Value must be from among 'IDENTIFIERS:[ALL|NONE|PLSQL|SQL|PUBLIC], STATEMENTS:[ALL|NONE]'.

**Action**: Enter valid PLSCOPE_SETTINGS compilation options.

### YAS-05385:ERR_PL_INVALID_CCFLAGS

**Message**: invalid parameter for plsql_ccflags

**Action**: Enter valid PLSQL_CCFLAGS compilation options.

### YAS-05386:ERR_PL_INVALID_WARNINGS

**Message**: invalid parameter for plsql warnings flag

**Action**: Enter valid PLSQL_WARNINGS compilation options.

### YAS-05387:ERR_PL_INVALID_LENGTH_SEMANTICS

**Message**: invalid value %s for parameter nls_length_semantics, must be from among BYTE, CHAR

**Action**: Enter valid NLS_LENGTH_SEMANTICS options.

### YAS-05388:ERR_PL_INVALID_PLSQL_DEBUG

**Message**: invalid value %s for parameter plsql_debug, must be from among TRUE, FALSE

**Action**: Enter valid PLSQL_DEBUG options.

### YAS-05389:ERR_PL_INVALID_CODE_TYPE

**Message**: invalid value %s for parameter plsql_code_type, must be from among INTERPRETED, NATIVE

**Action**: Enter valid PLSQL_CODE_TYPE options.

### YAS-05390:ERR_PL_INVALID_COL_STAT_TYPE

**Message**: invalid col_stat_type "%s": must be ALL | HISTOGRAM

**Action**: Please enter ALL or HISTOGRAM.

### YAS-05391:ERR_PL_CLAUSE_INCORRECT_PLACE

**Message**: incorrect placement of %s clause

**Action**: Remove the corresponding clause.

### YAS-05392:ERR_PL_SUBTYPE_AUTHID_DIFF

**Message**: supertypes's AUTHID is different from subtype's AUTHID

**Action**: Create the subtype with the AUTHID of the supertype or remove the AUTHID clause from the subtype.

### YAS-05393:ERR_PL_INVALID_PREFS_NAME

**Message**: invalid input values for preference name

**Action**: Please check if the Statistics option name input is correct.

### YAS-05394:ERR_PL_INVALID_PREFS_VALUE

**Message**: invalid input values for preference value

**Action**: Please check if the input values for the Statistics options meet specifications.

### YAS-05395:ERR_PL_ILLEGAL_ARR_CONSTRUCTOR

**Message**: illegal varray constructor, %s

**Action**: Please check if the used VARRAY constructor function is valid.

### YAS-05396:ERR_PL_INVALID_FUNC_ACCESS

**Message**: cannot access this function return

**Action**: Please check if the return value of the function is allowed for direct access.

### YAS-05397:ERR_PL_VARRAY_TYPE_REQUIRED

**Message**: datatype mismatch, VARRAY required

**Action**: Please check if the input parameters are VARRAY type values.

### YAS-05398:ERR_PL_INVALID_PROCEDURE

**Message**: invalid procedure %s

**Action**: Please check if the procedure body is valid.

### YAS-05399:ERR_PL_INVALID_TABLE_ITEM

**Message**: cannot access rows from a non-nested table item

**Action**: Change the TABLE function's parameter to be of Nested Table type.

### YAS-05401:ERR_AXC_CLUSTER_MODE

**Message**: cannot start %s database in %s mode

**Action**: Please check if the CLUSTER_DATABASE configuration parameter is reasonable.

### YAS-05402:ERR_AXC_REQ_LOCK_TIMEOUT

**Message**: global lock serivce request lock timeout, lock %lu-%u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05403:ERR_AXC_UNKNOWN_PARAM

**Message**: %s is an unknown parameter

**Action**: Please check if the parameter name in the configuration file is valid.

### YAS-05404:ERR_AXC_ILLEGAL_PARAM_VALUE

**Message**: illegal value of parameter %s

**Action**: Please check if the parameter value in the configuration file is valid.

### YAS-05405:ERR_AXC_FETCH_REDO_PACK

**Message**: cannot fetch redo pack of instance %d

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05406:ERR_AXC_INVALID_MSG_TYPE

**Message**: invalid message type %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05407:ERR_AXC_REMOTE_WAIT_TIMEOUT

**Message**: gcs latch remote wait timeout, block %u-%u-%u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05410:ERR_AXC_INTERVAL_NOT_SYNC

**Message**: the interval broadcast is not complete

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05411:ERR_AXC_ATTACH_MASTER_RES

**Message**: failed to attach master resource, type: %s, name: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05412:ERR_AXC_SEARCH_MASTER_RES

**Message**: cannot find master resource, type: %s, id: %lu

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05413:ERR_AXC_INVALID_DEPOSIT_XACT

**Message**: invalid deposit transaction %u-%u-%u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05414:ERR_AXC_EXPIRED_RECOVER_MSG

**Message**: the version of grc recover message is %lu, but now topo version is %lu

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05415:ERR_AXC_GRC_RECOVER_WORKING

**Message**: grc recover is working, try again later

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05416:ERR_AXC_INTERNAL_ERROR

**Message**: axc internal error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05417:ERR_AXC_GRC_NOT_MATCHED

**Message**: grc version %u does not match with recover version %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05418:ERR_AXC_TOPO_NOT_MATCHED

**Message**: topo version mismatch

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05419:ERR_AXC_REFORM_NOT_MATCHED

**Message**: reform status mismatch

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05420:ERR_AXC_SWITCH_GHT_RULE

**Message**: ght rule don't need to switch

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05421:ERR_AXC_INVALID_MSG_SIZE

**Message**: invalid cluster message size %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05422:ERR_AXC_DB_MUST_MOUNTED

**Message**: database must be mounted in this instance and not mounted in other instance

**Action**: The cluster must ensure that the current node is in MOUNTED mode while other nodes are in NOMOUNT mode or shut down.

### YAS-05423:ERR_AXC_INVALID_INSTANCE_ID

**Message**: invalid instance id %u in cluster

**Action**: Invalid instance ID, please check if the number of instances in YAC database matches the actual number of deployed instances. If there is a mismatch, you can try executing "alter database add instance" to restore consistency.

### YAS-05424:ERR_AXC_DB_JOIN_TIMEOUT

**Message**: instance join timeout

**Action**: Join to the cluster has timed out, please check if the first started instance is in open state.

### YAS-05425:ERR_AXC_INST_EXIT_CLUSTER

**Message**: instance %u has exited the cluster

**Action**: This instance has exited the cluster unexpectedly. Review the alert logs, operation logs, and operating system logs of this instance to identify the reason for the unexpected exit.

### YAS-05426:ERR_AXC_CLUSTER_IN_REFORM

**Message**: the cluster is undergoing reform

**Action**: The cluster is currently performing node reorganization; please wait for this operation to complete before trying again.

### YAS-05427:ERR_AXC_INST_MUST_OPEN

**Message**: instance %u must be opened

**Action**: The current operation requires the instance to be in open state; please start and open the instance before retrying the operation.

### YAS-05428:ERR_AXC_NOT_MOUNT_WHEN_DEMOTE

**Message**: instance cannot be mounted when switchover

**Action**: Please wait until the switchover is complete before opening this instance.

### YAS-05429:ERR_AXC_GRC_RES_NOT_EXIST

**Message**: grc resource (%s) not exist

**Action**: GRC resource does not exist, typically due to the XA transaction not being started, please start the XA transaction first.

### YAS-05430:ERR_AXC_XA_ALREADY_EXIST

**Message**: distributed transaction is already exist in the cluster

**Action**: The XA transaction already exists in the cluster, which may mean it has been started on this instance or another instance. You cannot start the same XA transaction again; please finish the existing XA transaction first.

### YAS-05431:ERR_AXC_XA_RECOVERING

**Message**: distributed transaction is recovering, please retry later

**Action**: In the process of distributed Transaction Recovery, starting new XA transactions or performing DDL operations is not allowed; please try again later.

### YAS-05432:ERR_AXC_XA_ALREADY_END

**Message**: distributed transaction is already finished by other session(%u-%u)

**Action**: The distributed transaction has already been committed or rolled back by another session, please restart the distributed transaction.

### YAS-05433:ERR_AXC_TEMP_SPC_DDL_FORBID

**Message**: temporary tablespaces ddl is forbidden during recovery

**Action**: The cluster is currently executing fault recovery; please wait for the recovery to complete before attempting this operation.

### YAS-05434:ERR_AXC_PARAM_NOT_MATCH

**Message**: instance parameter "%s" doesn't match across cluster

**Action**: Parameter configurations across cluster instances are inconsistent; please modify them and retry the operation.

### YAS-05435:ERR_AXC_PARAM_VALUE_NOT_MATCH

**Message**: parameter "%s" value on current instance does not match the active instances

**Action**: In YAC/Distributed Cluster Deployment, parameters that must be consistent across multiple instances must be configured identically. This error typically occurs when you attempt to modify such parameters using SCOPE=SPFILE without restarting the entire cluster (e.g., only restarting some instances), resulting in configuration inconsistencies across instances in the shared cluster. If you encounter this error, you may choose one of the following solutions:

- Common Solution: Restart the entire cluster to resolve the issue.

- Alternative Solution: If business operations cannot be interrupted, temporarily revert the parameter configuration to its previous value, then adjust the parameter settings and restart the entire cluster during a maintenance window.

### YAS-05436:ERR_AXC_INST_NOT_IN_CLUSTER

**Message**: instance %u is not in cluster

**Action**: This instance is invalid in the cluster. Please re-scale out and retry.

### YAS-05437:ERR_AXC_REMOVE_INST_IS_FORBIDDEN

**Message**: remove instance %u is forbidden, the reason is %s

**Action**: The scale-in operation cannot be performed. Please troubleshoot based on the error message and try again.

### YAS-05451:ERR_AXC_MSG_CREATE_CELL

**Message**: failed to create cluster message cell

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05452:ERR_AXC_MSG_GET_TIMEOUT

**Message**: get cluster message timeout

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05453:ERR_AXC_MSG_SEND_FAILED

**Message**: send cluster message failed

**Action**: Internal error, please contact our technical support for resolution.

<span id="errno5501" name="errno5501"></span>

### YAS-05501:ERR_YFS_NOT_OPEN

**Message**: YFS instance is not OPEN

**Action**: The YFS instance is not ready; there is no need to process it. The error will disappear once the YFS instance starts successfully.

### YAS-05502:ERR_YFS_MASTER_NOREADY_FOR_JOIN

**Message**: YFS master does not ready for standby joining

**Action**: The YFS instance is not ready to accept standby joining; please retry.

### YAS-05503:ERR_YFS_DUMP_BACKTRACE_TIMEOUT

**Message**: dump backtrace timeout

**Action**: Please check the machine of the stack printing session and retry.

### YAS-05504:ERR_YFS_DG_REDUN_NOT_SATISFIED 

**Message**: the redundancy of the diskgroup %s is not satisfied

**Action**: The diskgroup cannot meet the redundancy requirements due to a disk failure, and data may have been lost.

### YAS-05505:ERR_YFS_DISK_OFFLINE

**Message**: the disk %s is offline

**Action**: The disk is offline; please check if the disk has failed and ensure the privileges meet the requirements.

### YAS-05506:ERR_YFS_NO_SYSTEM_DISKGROUP

**Message**: operation is not allowed before creating system diskgroup

**Action**: Please create the system diskgroup first.

### YAS-05511:ERR_YFS_UPDATE_DISKPATH_ERROR

**Message**: The current operation will update the value of the parameter named %s to exceed the maxinum length %u

**Action**: The current operation will update the value of the parameter YFS_DISKSTRING, causing it to exceed the maximum length. Please properly plan the disk path.

### YAS-05512:ERR_YFS_DISK_CLOSED

**Message**: device %s is not open

**Action**: The device is not open, please contact our technical support for resolution.

### YAS-05513:ERR_YFS_OPEN_DISK

**Message**: failed to open device %s, error message %s

**Action**: Failed to open the device, please confirm if the device path and privilege are correct.

### YAS-05514:ERR_YFS_INVALID_NAME

**Message**: %s is an invalid %s name: %s

**Action**: Please confirm if the DiskGroup/disk/directory/file name is correct.

### YAS-05515:ERR_YFS_TOO_MANY_FILES

**Message**: the maximum number of diskgroup files is %lu

**Action**: Please ensure the number of files meets YFS specifications.

### YAS-05517:ERR_YFS_DUPLICATE_DIR_NAME

**Message**: duplicate directory name %s

**Action**: Duplicate directory name, please confirm if the directory name is correct.

### YAS-05518:ERR_YFS_DIR_NOT_EMPTY

**Message**: the directory %s is not empty

**Action**: The directory is not empty and cannot execute the operation.

### YAS-05519:ERR_YFS_ILLEGAL_CONN_ERROR

**Message**: illegal connection

**Action**: Illegal connection request.

### YAS-05520:ERR_YFS_INVALID_PARAM_SIZE

**Message**: the specified size is invalid, %s

**Action**: The request parameter is invalid, please enter a valid request parameter.

### YAS-05521:ERR_YFS_OBJ_EXIST

**Message**: file or directory '%s' already exists

**Action**: The file or directory already exists, please do not create it again.

### YAS-05522:ERR_YFS_OBJ_NOT_EXIST

**Message**: file or directory '%s' does not exist

**Action**: Please confirm if the file or directory exists before accessing.

### YAS-05523:ERR_YFS_TOO_MANY_SESSION

**Message**: too many YFS sessions 

**Action**: Please increase the MAX_SESSION configuration option of YFS, recommended to modify it to the MAX_SESSION configuration of YashanDB + 100.

### YAS-05524:ERR_YFS_ENTRY_CNT_INVALID

**Message**: the count of sub directory and file except %u but real %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05525:ERR_YFS_FILE_TOO_LARGE

**Message**: "maximum size of YFS file is %"lu"

**Action**: The file is too large, exceeding the maximum limit.

### YAS-05526:ERR_YFS_INVALID_HOME

**Message**: invalid YFS home: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05527:ERR_YFS_BUF_IS_NOT_ALIGNED

**Message**: IO buffer of YFS must be aligned with %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05528:ERR_YFS_DUPLICATE_DG

**Message**: duplicate diskgroup name %s

**Action**: Duplicate DiskGroup name, please use a different name.

### YAS-05529:ERR_YFS_RENAME_CROSS_DG

**Message**: cann't rename file/dir cross diskgroups 

**Action**: Cannot execute mv operation across different DiskGroups.

### YAS-05530:ERR_YFS_NO_FREE_SPACE

**Message**: no free space in diskgroup %s

**Action**: The current DiskGroup has insufficient disk space, please add disks.

### YAS-05531:ERR_YFS_DISK_BLOCK_CORRUPTED

**Message**: YFS disk block is corrupted, diskgroup: %s, disk: %s, block type: %u, block id: %lu

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05532:ERR_YFS_FILE_BLOCK_CORRUPTED

**Message**: YFS file block is corrupted, diskgroup: %s, fd: %u, block type: %u, block id: %lu

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05533:ERR_YFS_INVALID_BOOT_DISK

**Message**: invalid YFS boot disk 

**Action**: The YFS boot disk is invalid, please configure the correct boot disk.

### YAS-05534:ERR_YFS_UNSUPPORT_OPER

**Message**: unsupport operation: %s  

**Action**: Unsupported operation command.

### YAS-05535:ERR_YFS_DISK_TOO_SMALL

**Message**: the minimum size of disk is %lu   

**Action**: The disk space does not meet the minimum requirement, please use a larger disk.

### YAS-05536:ERR_YFS_INVALID_PARAM_NAME

**Message**: invalid YFS parameter name %s  

**Action**: Configuration parameter error, please configure the correct configuration parameters.

### YAS-05537:ERR_YFS_DROP_NONEMPTY_DG

**Message**: diskgroup %s is not empty  

**Action**: There is data on the DiskGroup, it cannot be deleted.

### YAS-05538:ERR_YFS_DG_IS_OFFLINE

**Message**: diskgroup %s is offline  

**Action**: The DiskGroup is offline, operations cannot be executed.

### YAS-05539:ERR_YFS_DG_IS_UNMOUNTED

**Message**: diskgroup %s is unmounted  

**Action**: The DiskGroup is not mounted, operations cannot be executed.

### YAS-05540:ERR_YFS_DG_NOT_EXISTS

**Message**: diskgroup %s does not exist 

**Action**: The DiskGroup does not exist, please use the correct DiskGroup.

### YAS-05541:ERR_YFS_INVALID_PARAM_VALUE

**Message**: the value of YFS parameter %s is invalid 

**Action**: The value of the configuration parameter is incorrect, please configure it correctly.

### YAS-05542:ERR_YFS_DG_IS_MOUNTED

**Message**: diskgroup %s is already mounted  

**Action**: The DiskGroup is already mounted, please do not mount it again.

### YAS-05543:ERR_YFS_DUPLICATE_FG

**Message**: duplicate failgroup name %s  

**Action**: Duplicate FailureGroup name, please choose a different name.

### YAS-05544:ERR_YFS_DUPLICATE_DISK

**Message**: duplicate disk name %s 

**Action**: Duplicate disk name, please use a different disk name or use the system default name.

### YAS-05545:ERR_YFS_DISK_ALREADY_USED

**Message**: disk '%s' is already used

**Action**: The current disk is already in use, do not mount a disk to different DiskGroups.

### YAS-05546:ERR_YFS_INVALID_FG_COUNT

**Message**: the minimum count of failgroup is %u for diskgroup redundancy  

**Action**: The number of FailureGroups in the DiskGroup does not match its redundancy. Please configure enough FailureGroups.

### YAS-05547:ERR_YFS_NAME_TOO_LONG

**Message**: the maximum length of YFS name is %u 

**Action**: The YFS file path name is too long, please configure an appropriate path length.

### YAS-05548:ERR_YFS_TOO_MANY_ELEMENTS

**Message**: the maxnumim number of %s in %s is %u 

**Action**: Please confirm that the number of YFS disks, FailureGroups, and DiskGroups meets YFS specifications.

### YAS-05549:ERR_YFS_INVALID_DISK_NUM

**Message**: failgroups in diskgroup %s have different number of disks 

**Action**: In the same DiskGroup, the number of disks in different FailureGroups is inconsistent, please configure the same number of disks.

### YAS-05550:ERR_YFS_INVALID_DISK_SIZE

**Message**: the size of disk %s must be equal to or less than its actual size

**Action**: Illegal size parameter, the size should be less than or equal to the actual size of the disk. If the size parameter is not specified, YFS will use the entire disk space.

### YAS-05551:ERR_YFS_END_OF_FILE

**Message**: read or wirte offset exceeds the file size

**Action**: Read/write exceeds the end of the file, please extend the file first.

### YAS-05552:ERR_YFS_ALLOC_FROM_SHM_POOL

**Message**: can't allocate %lu bytes from shared memory pool  

**Action**: Insufficient shared memory, please configure a larger YFS_SHM_POOL_SIZE.

### YAS-05553:ERR_YFS_FAULT_POINT

**Message**: this is a yfs fault point, id: %u  

**Action**: Triggered internal fault point, please contact our technical support for resolution.

### YAS-05554:ERR_YFS_DISK_IN_USE

**Message**: disk %s has already been formatted by YFS, specify 'FORCE' option to reuse

**Action**: The disk has been added to YFS and cannot be added again; it can be forced to add to YFS using 'FORCE'.

### YAS-05555:ERR_YFS_NOT_INIT

**Message**: YFS is not initialized by current instance

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05556:ERR_YFS_PARA_VALUE_INVALID

**Message**: the value of parameter %s is invalid, %s

**Action**: Fault point parameter error, please contact our technical support for resolution.

### YAS-05557:ERR_YFS_INVALID_REMOTE_VAR

**Message**: invalid remote var: %s

**Action**: Illegal remote variable, please check the variable name.

### YAS-05558:ERR_YFS_BAD_FALT_POINT

**Message**: bad fault_point: %s

**Action**: Illegal fault point, please contact our technical support for resolution.

### YAS-05559:ERR_YFS_DISK_GET_SIZE

**Message**: can't get %s size: %s

**Action**: Unable to get the disk size, please handle according to the error code.

### YAS-05560:ERR_YFS_BAD_ATTRIBUTE

**Message**: bad attribute: %s, %s

**Action**: Illegal attribute, please refer to the document to set the attribute.

### YAS-05561:ERR_YFS_INVALID_FILE_IDENTIFY

**Message**: invalid YFS file identify:%u

**Action**: Invalid YFS file identifier, please contact our technical support for resolution.

### YAS-05562:ERR_YFS_INVALID_DISKGROUP_ID

**Message**: invalid YFS diskgroup id:%u

**Action**: Invalid YFS DiskGroup number, please contact our technical support for resolution.

### YAS-05563:ERR_YFS_UNSPPORTED_DISK

**Message**: unsupported disk: %s

**Action**: Unsupported disk, please ensure the disk meets the specifications specified by YFS.

### YAS-05564:ERR_YFS_IS_CLOSED

**Message**: YFS is already closed

**Action**: YFS has been closed.

### YAS-05565:ERR_YFS_UNSUITABLE_PARAM_VALUE

**Message**: unsuitable param value, the value of %s must be greater than or equal to %s

**Action**: Parameter setting is inappropriate, one parameter must be greater than or equal to another parameter.

### YAS-05566:ERR_YFS_ARG_TOO_LONG

**Message**: arg too long, max length is %u: %s

**Action**: Input parameter is too long, exceeding the limit, please check the calling parameters.

### YAS-05567:ERR_YFS_PARSER_STACK_OVERFLOW

**Message**: parser stack overflow, try to push %lu bytes

**Action**: Insufficient parser stack space, please increase the PARSER_STACK_SIZE configuration.

### YAS-05568:ERR_YFS_HANDLER_CANCEL

**Message**: current YFS session has been killed or canceled

**Action**: The connection has been disconnected, try to reconnect.

### YAS-05569:ERR_YFS_SHM_POOL_INSUFFICIENT

**Message**: YFS shm pool insufficient, expecting=%lu, SHM_POOL_SIZE=%lu

**Action**: The YFS shared memory pool has reached its limit, please increase SHM_POOL_SIZE.

### YAS-05570:ERR_YFS_SYS_AREA_INSUFFICIENT

**Message**: YFS sys area insufficient, expecting=%lu, SYS_AREA_SIZE=%lu

**Action**: The YFS sys area has reached its limit, please increase SYS_AREA_SIZE.

### YAS-05571: ERR_YFS_VERIFY_FILE

**Message**: file verify failed: dgid=%u, fd=%d, offset=%lu, len=%u

**Action**: YFS file verification failed, all data copies are corrupted, please confirm if the storage device is normal and try to recover data from the backup.

### YAS-05572: ERR_YFS_FILE_NOT_CLOSE_IN_DG

**Message**: cannot dismount or drop diskgroup %s when file is being used

**Action**: There are files in the DiskGroup that are not closed, operations cannot continue, please close the open files in the DiskGroup, or use `FORCE` to forcibly execute the operation.

### YAS-05573: ERR_YFS_DISK_SIZE_NOT_MATCH

**Message**: the size of disk %s is not equal to the size of other disks in the current diskgroup

**Action**: A disk of size not matching expectations has been added to the DiskGroup; YFS requires all disks in the same DiskGroup to have the same size; please select disks of the appropriate size and retry.

### YAS-05574: ERR_YFS_YCS_DISK

**Message**: ycr/voting disk detected: %s, you cannot use it as YFS data disk

**Action**: Please do not use the ycr or voting disk as a YFS data disk; if you are sure you want to use them as data disks, please ensure that no YCS service is using them and specify `FORCE` to forcibly execute the operation.

### YAS-05575: ERR_YFS_MSG_PROCESSING

**Message**: YFS is processing request message, please retry a later

**Action**: YFS is processing the message, please retry later.

### YAS-05576: ERR_YFS_ALLOC_MEMORY

**Message**: "cannot allocate %lu bytes from %s for %s"

**Action**: YFS memory allocation failed.

- If allocated from the operating system, please check if the operating system's memory meets system requirements.
- If allocated from the YFS system area, please check if the configuration parameter SYS_AREA_SIZE meets the requirements.
- If allocated from the YFS shared memory pool, please check if the configuration parameter SHM_POOL_SIZE meets the requirements.

### YAS-05577: ERR_YFS_DISK_DEVICE_LOST

**Message**: "the disk device lost in diskgroup %s, %s"

**Action**: The storage device in use has been removed or is inaccessible, please check and retry.

### YAS-05578: ERR_YFS_UNEXCEPTED_DISK

**Message**: "the disk %s is not in current YFS instance"

**Action**: The disk does not belong to the current instance, please check if the disk in the YFS_DISKSTRING configuration path belongs to the current instance.

### YAS-05579: ERR_YFS_BOOT_DISK_BROKEN

**Message**: "the boot disk broken in diskgroup %s"

**Action**: The boot disk in the DiskGroup has been damaged, please contact technical support for resolution.

### YAS-05580: ERR_YFS_PARAM_UNSUPPORT_UPDATE

**Message**: "the parameter %s do not support modify online"

**Action**: This parameter does not support online modification, please confirm that the output parameters are correct.

### YAS-05581: ERR_YFS_INVALID_OP_SYS_DG

**Message**: "invalid operation to system diskgroup: %s"

**Action**: Unsupported operation on the system DiskGroup, please adjust and retry according to the prompts.

### YAS-05582: ERR_YFS_COPY_INDEX_OUT_OF_RANGE

**Message**: "file copy index out of range: %u"

**Action**: Attempted to access a non-existent copy; please ensure the file copy number is less than the number of copies corresponding to the redundancy of the DiskGroup where the file is located.

### YAS-05583: ERR_YFS_CANNOT_SHUTDOWN_IN_CLUSTER

**Message**: "cannot shutdown yasfs, please use 'ycsctl' tool"

**Action**: In YAC mode, please stop the service using `ycsctl`.

### YAS-05584: ERR_YFS_CHANGE_MODE

**Message**: "YFS can not change to %s mode for %s"

**Action**: YFS currently does not support changing modes; please confirm the reason and retry.

### YAS-05585: ERR_YFS_CREATE_METAFILE

**Message**: "YFS create meta file failed for %s"

**Action**: YFS failed to create the meta file. Retry according to the failure reason.

### YAS-05586: ERR_YFS_SYSDG_DISK_PATH

**Message**: "the config value YFS_DISKSTRING:%s must contain the disk path:%s"

**Action**: Please ensure that the current disk path is included in the YFS_DISKSTRING configuration parameter, then retry.

### YAS-05587: ERR_YFS_DROP_DISK

**Message**: "cannot drop disk: %s, %s"

**Action**: Unable to drop the disk, please retry according to the prompts.

### YAS-05588:ERR_YFS_DISK_NOT_IN_DG

**Message**: "disk %s is not a member of diskgroup %s"

**Action**: The disk is not a member of the DiskGroup.

### YAS-05589:ERR_YFS_FG_NOT_IN_DG

**Message**: "failgroup %s is not a member of diskgroup %s"

**Action**: The FailureGroup is not a member of the DiskGroup.

### YAS-05590:ERR_YFS_DISK_REUSED

**Message**: "disk %s has already been used by current YFS instance"

**Action**: The disk is already in use by the current instance, please use a different disk and retry.

### YAS-05591:ERR_YFS_DG_COUNT_INCONSISTENT

**Message**: "the diskgroups count is %u in current node, but it is %u in master node"

**Action**: The counts of DiskGroups between the cluster's Primary and Standby nodes are inconsistent. Please check if the disks on the Primary and Standby nodes are properly mounted and can be read and written normally. If unresolved, please contact our technical support for resolution.

### YAS-05592:ERR_YFS_DG_INFO_INCONSISTENT

**Message**: "the diskgroup information is different with master node, the DiskGroup name is %s"

**Action**: The DiskGroup status and number of disks between the cluster's Primary and Standby nodes are inconsistent. Please check if the disks on the Primary and Standby nodes are properly mounted and can be read and written normally. If unresolved, please contact our technical support for resolution.

### YAS-05593:ERR_YFS_VALUE_OUT_OF_RANGE

**Message**: "%s out of range, min: %u, max: %u, new value: %u"

**Action**: The variable is out of the valid range. Please adjust it and try again.

### YAS-05594: ERR_YFS_DISK_ALREADY_ONLINE

**Message**: "disk %s in diskgroup %s is already online"

**Action**: The disk is already online. There is no need to repeat this operation.

### YAS-05595: ERR_YFS_RBL_CANCELED

**Message**: "rebalance job %s canceled"

**Action**: The rebalance operation has been cancelled. Please confirm the reason for cancellation and try again.

### YAS-05596: ERR_YFS_RBL_FAILED

**Message**: "rebalance job %s failed, error: %s"

**Action**: The rebalance operation has failed. Please confirm the cause of failure and try again.

### YAS-05597: ERR_YFS_RBL_TERM

**Message**: "rebalance job %s terminated"

**Action**: The rebalance operation has been interrupted. If the interruption was caused by the DiskGroup being unmounted, the task will automatically restart when the DiskGroup is remounted. If the interruption was caused by cluster shutdown, the task will automatically restart when the cluster is restarted.

### YAS-05598: ERR_YFS_RBL_AGAIN

**Message**: "rebalance job %s try again"

**Action**: An internal error occurred. High - priority tasks will be processed first, and the rebalance operation will be retried.

### YAS-05701:ERR_YCS_CONSOLE_COMMAND_ERROR

**Message**: invalid ycs command, reason: %s

**Action**: Please enter the correct ycs command options.

### YAS-05702:ERR_YCS_JOIN_FAILURE

**Message**: failed to join ycs, reason: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05703:ERR_YCS_PROTOCOL_ERROR

**Message**: ycs protocol error

**Action**: Please check if the software versions match, or contact our technical support for resolution.

### YAS-05704:ERR_YCS_INVALID_CLUSTER_TERM

**Message**: invalid cluster term

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05705:ERR_YCS_RAISE_EXCEPTION

**Message**: ycs throw an exception, id:%d, msg:%s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05706:ERR_YCS_RPC_TIMEOUT

**Message**: ycs communication timeout

**Action**: Please check the network of the deployment environment, check connection information, or contact our technical support for resolution.

### YAS-05707:ERR_YCS_RESOURCE_NOT_OPEN

**Message**: ycs resource %d not open, cannot process inter message

**Action**: Ensure the ycs resource status is open before retrying the operation.

### YAS-05708:ERR_YCS_CLUSTER_NOT_EXIST

**Message**: cluster hasn't been created yet

**Action**: The YAC/Distributed Cluster has not been created yet. Please create the cluster first before performing other operations.

### YAS-05709:ERR_YCS_ADD_NETWORK

**Message**: add network failed, reason: %s

**Action**: Please troubleshoot the cause of the network addition failure according to the prompts. If the issue still cannot be resolved, please contact our technical support for assistance.

### YAS-05710:ERR_YCS_CONNECTION_ERROR

**Message**: ycs connection error, %s

**Action**: Please check the network or version compatibility in the deployment environment, check connection information, or contact our technical support for resolution.

### YAS-05711:ERR_YCS_CLUSTER_EXIST_ERROR

**Message**: cluster %s exists, please try with overwrite option

**Action**: Please use the ycr show config command to check if the cluster already exists, and if you want to overwrite the cluster, please add the -O option when creating the cluster.

### YAS-05712:ERR_YCS_CREATE_CLUSTER_ERROR

**Message**: create cluster failed, reason: %s

**Action**: Please check the prompts for the reason for cluster creation failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05713:ERR_YCS_ADD_NODE_ERROR

**Message**: add node failed, reason: %s

**Action**: Please check the prompts for the reason for node creation failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05714:ERR_YCS_REMOVE_NETWORK

**Message**: remove network failed, reason: %s

**Action**: Please troubleshoot the cause of the network deletion failure according to the prompts. If the issue still cannot be resolved, please contact our technical support for assistance.

### YAS-05715:ERR_YCS_INVALID_NETWORK

**Message**: network(%s) is invalid, reason: %s

**Action**: Please check whether the network input format is valid, whether the subnet address and mask information are correct, and whether the network is reachable according to the prompts. If the issue still cannot be resolved, please contact our technical support for assistance.

### YAS-05716:ERR_YCS_NODE_NOT_FOUND_ERROR

**Message**: node '%s' not found in ycr

**Action**: Please use the ycr show config command to check the node name and ensure it is consistent with the server name displayed by the hostnamectl command.

### YAS-05717:ERR_YCS_ADD_DBINSTANCE_ERROR

**Message**: failed to add yasdb instance %s to node %s, reason: %s

**Action**: Please check the prompts for the reason for instance creation failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05718:ERR_YCS_DBINSTANCEID_EXHAUST

**Message**: the number of db instances under node %s has reached the maximum limit

**Action**: The number of instances on the current node has reached the limit, please contact our technical support for resolution.

### YAS-05719:ERR_YCS_REMOVE_DBINSTANCE_ERROR

**Message**: failed to remove yasdb instance %s from node %s, reason: %s

**Action**: Please check the prompts for the reason for instance removal failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05720:ERR_YCS_CHECKSUM_ERROR

**Message**: checksum error, disk damage

**Action**: Please contact our technical support for resolution.

### YAS-05721:ERR_YCS_NOMATCH_ERROR

**Message**: input parameter %s is invalid, reason: %s

**Action**: Please check if the cluster name, node name, or script name meets the following standards:

- The name must contain 4 to 64 characters.
- The name must start with a letter and may contain letters, numbers, dashes (-), and underscores (_).
- Script names must have a .sh file extension.

### YAS-05723:ERR_YCS_REMOVE_NODE_ERROR

**Message**: failed to remove node %s, reason: %s

**Action**: Please check the prompts for the reason for node removal failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05725:ERR_YCS_READ_WRITE_FILE_ERROR

**Message**: failed to access voting file or ycr file, reason: %s

**Action**: Please check the prompts for the reason for failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05726:ERR_YCS_SHELL_NOT_FOUND

**Message**: ycs shell: %s not exist

**Action**: Please check the prompts for the reason for failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05728:ERR_YCS_DB_JOIN_ERROR

**Message**: failed to join ycs, reason: %s

**Action**: Please check the prompts for the reason for database instance joining YCS failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05729:ERR_YCS_PARA_INVALID_BETWEEN

**Message**: the value of YCS parameter %s must be between % PRIu64  and % PRIu64

**Action**: Please check the prompts for the reason for failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05730:ERR_YCS_TOOL_SHAKE_HAND_ERROR

**Message**: ycs tool shake hand error, more than max times at a moment: %d

**Action**: Please check the prompts for the reason for failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05731:ERR_YCS_TOOL_CHANGE_STATE_ERROR

**Message**: ycs tool get lock timeout: %d s, failed to change node state

**Action**: Please check the prompts for the reason for failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05732:ERR_YCS_TOOL_SET_PARA_ERROR

**Message**: ycs tool failed to set parameter: %s, reason: %s

**Action**: Please check the prompts for the reason for failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05733:ERR_YCS_TOOL_GET_PARA_ERROR

**Message**: ycs tool failed to get parameter: %s

**Action**: Please check the prompts for the reason for failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05734:ERR_YCS_PARA_VALUE_INVALID

**Message**: the value of parameter %s is invalid, %s

**Action**: Please check the prompts for the reason for failure; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05735:ERR_YCS_FILE_NOT_EXISTS

**Message**: ycs file '%s' does not exist

**Action**: Please check the prompts for the reason for file absence; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05736:ERR_YCS_INVALID_HOME_PATH

**Message**: ycs invalid environment variable 'YASCS_HOME': %s, reason: %s

**Action**: Invalid environment variable "YASCS_HOME", please reset the environment variable correctly.

### YAS-05739:ERR_YCS_ACK_TOO_LATE

**Message**: ycs ack message is too late, room id is %u

**Action**: Acknowledge message is invalid, please try again.

### YAS-05740:ERR_YCS_INIT_LOG_FAILED

**Message**: ycs init %s log failed

**Action**: Log initialization failed, please check the prompts for the reason.

### YAS-05741:ERR_YCS_CANCEL_WAITING_ACK

**Message**: ycs cancel waiting ack, room id is %u

**Action**: Canceling waiting for message response, the current node is stopping.

### YAS-05742:ERR_YCS_FAULT_POINT

**Message**: this is a ycs fault point, id: %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-05745:ERR_YCS_STOP_TIMEOUT

**Message**: ycs stop %s timeout %u s

**Action**: Timeout waiting for the process to terminate, please check the status of the process on the current node.

### YAS-05747:ERR_YCS_YCR_ISRUNNING

**Message**: ycr is running

**Action**: The YCR configuration command is being executed, please try again later.

### YAS-05751:ERR_YCS_HOST_EVICTED

**Message**: host %u has been evicted from cluster

**Action**: The server has been evicted from the cluster; please check if automatic fault recovery has been completed; if not, please check for network communication issues or shared Shared StorageIO.

### YAS-05752:ERR_YCS_YCR_SET_CONFIG

**Message**: ycsctl set ycr config failed, reason: %s

**Action**: Tool configuration YCR failed, please check the prompts for the reason; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05753:ERR_YCS_YCR_IMPORT

**Message**: ycsctl import ycr config failed, reason: %s

**Action**: Tool import YCR configuration failed, please check the prompts for the reason; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05754:ERR_YCS_YCR_EXPORT

**Message**: ycsctl export ycr config failed, reason: %s

**Action**: Tool export YCR configuration failed, please check the prompts for the reason; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05755:ERR_YCS_INTERNAL_BUSY

**Message**: ycs is busy(%s), please try again later   

**Action**: There are conflicts in the internal business process of YCS, please determine if to cancel the operation or retry later based on the prompts.

### YAS-05756:ERR_YCS_RES_START_STOP_FAIL

**Message**: ycs failed to %s %s, reason: %s   

**Action**: YCS failed to start or stop resources, please check the prompts for the reason; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05757:ERR_YCS_NODE_NOT_MASTER

**Message**: node(%u) not master, unable to %s

**Action**: This server may have experienced a fault and is no longer the master server. Please manually stop YCS and then start it.

### YAS-05758:ERR_YCS_MSG_ID_MISMATCH

**Message**: received msg id(%lu) mismatch with waiting id(%lu)

**Action**: YCS received a message sequence number mismatch response, which is an internally tolerable communication anomaly.

### YAS-05759:ERR_YCS_DB_BEGINWRITE_FAIL

**Message**: yasdb disk write conflict

**Action**: Yasdb disk IO conflict, please contact our technical support for resolution.

### YAS-05760:ERR_YCS_MONITOR_START_YCS_FAIL

**Message**: ycs monitor start ycs failed, reason: %s

**Action**: YCS monitoring failed to start YCS, please check the prompts for the reason; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05761:ERR_YCS_CLUSTER_BLOCK_INVALID

**Message**: ycs cluster block is invalid

**Action**: The cluster block in the voting disk has been incorrectly overwritten, YCS can detect and recover this data anomaly internally, please ignore.

### YAS-05762:ERR_YCS_TOOL_EXECUTE

**Message**: ycs tool failed to %s, reason: %s

**Action**: The YCS client tool execution command failed, please check the prompts for the reason.

### YAS-05764:ERR_YCS_ROOT_OPERATION

**Message**: ycs root agent failed to execute %s, reason: %s

**Action**: YCSRA failed to execute root-level operations, please check the prompts for the reason; if it still cannot be resolved, please contact our technical support for resolution.

### YAS-05765:ERR_YCS_ROOT_AGENT_ABNORMAL

**Message**: failed to interact with ycs root agent, reason: %s

**Action**: YCS and YCSRA interaction failed, please check the prompts for the reason; this is usually due to abnormal YCSRA process status.

### YAS-05766:ERR_YCS_MODIFY_YCR_ERROR

**Message**: failed to modify ycr, reason: %s

**Action**: Failed to modify YCR attributes; this is usually due to duplicate database instance names.

### YAS-05767:ERR_YCS_INVALID_VIRTUAL_IP

**Message**: VIP(%s) is invalid, reason: %s

**Action**: The VIP configuration information is not valid, please check the prompts for the reason.

### YAS-05768:ERR_YCS_OBJ_NOT_ENABLED

**Message**: %s is disabled

**Action**: The corresponding functionality is not configured or not started. Please ensure that configuration and startup have been completed.

### YAS-05769:ERR_YCS_RES_RELOCATION

**Message**: failed to relocate %s, reason: %s

**Action**: Manual resource migration (or automatic failover) failed, please check the prompts for the reason.

### YAS-05770:ERR_YCS_NO_RES_RUNNING_ON

**Message**: failed to %s because no %s was running on the given node

**Action**: No managed resources are available for operation on the specified node.

### YAS-05771:ERR_YCS_ADD_VIP

**Message**: failed to add VIP to node %s, reason: %s

**Action**: Failed to add VIP resource, please check the prompts for the reason.

### YAS-05772:ERR_YCS_REMOVE_VIP

**Message**: failed to remove VIP from node %s, reason: %s

**Action**: Failed to remove VIP resource, please check the prompts for the reason.

### YAS-05773:ERR_YCS_ADD_SCAN

**Message**: failed to add SCAN, reason: %s

**Action**: Failed to add SCAN resource. Please troubleshoot the cause according to the prompts.

### YAS-05774:ERR_YCS_INVALID_SCAN_NUMBER

**Message**: SCAN number %d is invalid

**Action**: Invalid SCAN sequence number. Please ensure the entered sequence number does not exceed the number of SCAN VIPs.

### YAS-05775:ERR_YCS_REMOVE_SCAN

**Message**: failed to remove SCAN, reason: %s

**Action**: Failed to delete SCAN resource. Please troubleshoot the cause according to the prompts.

### YAS-05776:ERR_YCS_DB_INSTANCE_ADDED

**Message**: instance '%s' has already been added to node %s

**Action**: For the same database resource, at most one instance can be added to each cluster node.

### YAS-05777:ERR_YCS_DATABASE_NOT_EXIST

**Message**: database not exist

**Action**: No database resources have been added to the cluster, so related operations cannot be performed.

### YAS-05778:ERR_YCS_DB_INSTANCE_NOT_EXIST

**Message**: no instance configured to node %s

**Action**: No instances of the database resource have been added to the cluster nodes, so related operations cannot be carried out.

<span id="errno6001" name="errno6001"></span>

### YAS-06001:ERR_ANS_PARAM_INVALID_VALUE

**Message**: the value of parameter %s is invalid, %s

**Action**: The input parameter is invalid, please enter a valid parameter according to the prompt.

### YAS-06003:ERR_ANS_MZONE_OVERFLOW

**Message**: the barrier between %s and %s is corrupted

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06004:ERR_ANS_TOO_MANY_SESSIONS

**Message**: maximum number of sessions exceeded

**Action**: The number of sessions has reached the limit (session information can be obtained through the V$SESSION view); please choose the processing method as needed:

- Adjust the MAX_SESSIONS parameter upwards, provided resources allow.
- Close unnecessary connections.
- Wait for other clients to disconnect before attempting to connect to the database again.

### YAS-06005:ERR_ANS_INSTANCE_NOT_INIT

**Message**: instance is not initialized

**Action**: Database is abnormal, please check the database status.

### YAS-06006:ERR_ANS_INSTANCE_STARTED

**Message**: instance has been started by another process

**Action**: Please close the database first and then restart or connect to the already started database.

### YAS-06007:ERR_ANS_MISS_SESSION_ID

**Message**: missing or invalid session ID

**Action**: Incorrect session ID, please query the V$SESSION view to check if the ID is correct.

### YAS-06008:ERR_ANS_SHUTDOWN

**Message**: failed to shutdown: %s

**Action**: Please resolve the error according to the prompt and execute again.

### YAS-06009:ERR_ANS_INVALID_PATH

**Message**: invalid path: %s, reason: %s

**Action**: Please modify the path to the correct one according to the prompt and execute again.

### YAS-06010:ERR_ANS_DB_NOT_READWRITE

**Message**: the database is not in readwrite mode

**Action**: Please execute the statement on the primary node.

### YAS-06011:ERR_ANS_DB_NOT_READONLY

**Message**: the database is not in readonly mode

**Action**: Please execute the statement on the standby node.

### YAS-06012:ERR_ANS_SESSION_NOT_EXIST

**Message**: user session does not exist

**Action**: Please log out and reconnect to the server.

### YAS-06013:ERR_ANS_TOO_MANY_SQL_HANDLERS

**Message**: too many sql handlers

**Action**: Please close unused connections or adjust the MAX_SESSIONS configuration parameter and retry.

### YAS-06014:ERR_ANS_PARAM_INVALID_RANGE

**Message**: the value of parameter %s must be between % PRIu64  and % PRIu64

**Action**: Please enter the parameter within the allowed range according to the output prompt.

### YAS-06015:ERR_ANS_PARAM_SYNC_STANDBYS

**Message**: QUORUM_SYNC_STANDBYS cannot be empty

**Action**: Please provide correct values for the parameters based on the relevant descriptions in [Configuration Parameters](Configuration Parameters).

### YAS-06016:ERR_ANS_KILL_CURR

**Message**: cannot kill current session

**Action**: The current connection is not allowed to close this session, please use another connection to execute.

### YAS-06017:ERR_ANS_ENV_VARIABLE

**Message**: failed to get environment %s

**Action**: Please set the corresponding environment variable according to the prompt and retry.

### YAS-06018:ERR_ANS_SQLID_NOT_EXIST

**Message**: invalid or non-existent SQL ID

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06019:ERR_ANS_INVALID_LIBRARY

**Message**: the linking library %s is invalid

**Action**: Please check if the linking library exists according to the prompt.

### YAS-06020:ERR_ANS_SET_SESSION_PARAM

**Message**: set session parameter: %s failed, %s

**Action**: Setting the specified session parameter failed, please check according to the prompt.

### YAS-06021:ERR_ANS_WR_NOT_FOUND

**Message**: workload repository record is not found

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06022:ERR_ANS_INIT_THREADLOCK_FAILED

**Message**: failed to initialize thread lock when %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06023:ERR_ANS_DB_ABNORMAL_READ_ONLY

**Message**: database is set to read-only because of database abnormal

**Action**: Please have a DBA intervene to check the cause of the fault, resolve the issue, and continue executing the business after eliminating the fault state.

### YAS-06024:ERR_ANS_AUTO_TRACE_INVALID

**Message**: invalid AUTOTRACE option

**Action**: Please correct the AUTOTRACE related option to a valid value according to the command introduction of [SET AUTOTRACE](../Tools Guide/yasql/User Guide for yasql.md#autotrace).

### YAS-06025:ERR_ANS_VALID_URL_EXPECTED

**Message**: valid URL of service expected

**Action**: Please configure the correct IP list.

### YAS-06026:ERR_ANS_INVALID_IPADDR

**Message**: failed to load netconfig: address %s is invalid

**Action**: Please change the IP list.

### YAS-06027:ERR_ANS_INSTANCE_SERVICE_UNAVAILABLE

**Message**: instance service unavailable, reason: %s

**Action**: Please retry after the database is started or after the Primary/Standby switch successes.

### YAS-06028:ERR_ANS_INVALID_EVENTS

**Message**: failed to process event statement, [%s] is not a valid argument

**Action**: Please configure the correct parameters and execute again.

### YAS-06029:ERR_ANS_LICENSE_EXPIRED

**Message**: license is expired

**Action**: The license has expired, please update the license.

### YAS-06030:ERR_ANS_LICENSE_UPDATE_FAILED

**Message**: failed to update license, reason: %s

**Action**: License update failed, please check the error message for the reason and retry.

### YAS-06031:ERR_ANS_CANCEL_CURR

**Message**: cannot perform the current operation on the same session

**Action**: The current session is not allowed to terminate the currently running SQL operation. Please use another session to execute.

### YAS-06401:ERR_RT_INVALID_ARGUMENT

**Message**: Invalid argument: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06412:ERR_RT_TRANSFORM

**Message**: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06414:ERR_RT_NULL

**Message**: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06415:ERR_RT_UNSUPPORTED

**Message**: feature %s has not been implemented yet

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06418:ERR_RT_IO_ERROR

**Message**: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06420:ERR_MEMORY_ALLOC

**Message**: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06421:ERR_RT_RE_EXECUTION

**Message**: failed to allocate memory, please try again

**Action**: Please check if the parameter COLUMNAR_VM_BUFFER_SIZE configuration is reasonable and retry.

<span id="errno6501" name="errno6501"></span>

### YAS-06501:ERR_RT_AN_INVALID_ARGUMENT

**Message**: invalid argument: %s

**Action**: Please check the parameters according to the information.

### YAS-06502:ERR_RT_AN_EXCEED_LIMIT

**Message**: exceed limit (expect>%d,<%d): now size is %d

**Action**: The input value is not within the specified range, please adjust the input value as prompted and retry.

### YAS-06503:ERR_RT_AN_IO

**Message**: io error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06504:ERR_RT_AN_NULL

**Message**: null error: %s

**Action**: Cannot have a null value, please adjust the input value as prompted and retry.

### YAS-06508:ERR_RT_AN_UTF8_ERROR

**Message**: utf8 error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06510:ERR_RT_AN_START_THREAD_ERROR

**Message**: start thread error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06511:ERR_RT_AN_ALLOC_ERROR

**Message**: failed to allocate %d bytes, %s is not enough

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06512:ERR_RT_AN_FORMAT_ERROR

**Message**: format error

**Action**: The system has insufficient available memory, please adjust memory configurations to reduce memory usage or contact our technical support for resolution.

### YAS-06601:ERR_XML_PARSE_MISS_KEYWORD

**Message**: missing columns keyword

**Action**: Missing keyword COLUMNS.

### YAS-06602:ERR_XML_PARSE_MISS_XQUERY_STRING

**Message**: xquery string literal not expected

**Action**: The XQuery content does not conform to syntax expectations.

### YAS-06603:ERR_XML_PARSE_INVALID_NAMESPACES

**Message**: namespaces string not expected

**Action**: The NAMESPACES content is not as expected.

### YAS-06604:ERR_XML_PARSE_ORD_ONLY_ONE

**Message**: xmltable support only one ordinality column

**Action**: XMLTABLE can have at most one FOR ORDINALITY column.

### YAS-06605:ERR_NOT_SUPPORT_MYSQL

**Message**: %s not support on mysql mode

**Action**: This data type is not supported in mysql mode.

### YAS-06606:ERR_XMLTABLE_MISSING_PATH

**Message**: missing path grammar

**Action**: The path syntax is missing. Please enter the correct syntax.

### YAS-06607:ERR_XMLGEN_RESOURCE_INSUFFICIENT

**Message**: xmlgen handle resource insufficient

**Action**: There is an insufficient XMLGEN system package handle resource. Please check whether the resources are released properly.

### YAS-06608:ERR_XMLGEN_UNSUPPORTED_DATATYPE

**Message**: xmlgen package unsupported datatype: %s

**Action**: The DBMS_XMLGEN package does not support generating XML data using the current data type.

### YAS-06609:ERR_XMLGEN_GENERATE_XMLDATA_FAILED

**Message**: failed to generate XML data from sql query results

**Action**: Failed to generate XML - formatted data. A common cause might be insufficient system resources. Please try again later.

### YAS-06610:ERR_XMLGEN_RESOURCE_INITIALIZED_FAILED

**Message**: failed to initialied xmlgen resource, reason: %s

**Action**: Initialization of XMLGEN system resources failed. Please check whether the currently executed statements are correct according to the prompts.

### YAS-06611:ERR_XMLGEN_PROCESSING_ERROR

**Message**: Error occurred in XML processing

**Action**: An error occurred in the processing of the DBMS_XMLGEN package. Please check whether the currently executed statements are correct.

### YAS-06612:ERR_XML_EXCEEDS_MAX_SIZE

**Message**: xml size exceeds the limit 64MB

**Action**: The size of the XML data exceeds the maximum limit of 64MB.

### YAS-06801:ERR_PL_INVALID_LIBRARY_NAME

**Message**: missing or invalid library name

**Action**: Please enter a valid library name.

### YAS-06802:ERR_PL_INVALID_LIBRARY

**Message**: invalid library %s

**Action**: Please check if the called library is valid.

### YAS-06803:ERR_PL_DBG_ALREADY_ON

**Message**: PL/SQL debugger is already open

**Action**: Please exit the ongoing debugging session and start again.

### YAS-06804:ERR_PL_FETCHLN_TYPE_MISMATCH

**Message**: type mismatch found at "%s" between FETCH cursor and INTO variables

**Action**: Please ensure that the type of FETCH data matches the type of INTO variables.

### YAS-06805:ERR_PL_RAISE_APP_ERR_OUT_OF_RANGE

**Message**: error number argument to raise_application_error of %d is out of range

**Action**: Please enter an error code within a valid range.

### YAS-06806:ERR_PL_JOB_INVALID_INSTACNE_ID

**Message**: job associated instance number %d is not valid

**Action**: Please enter the INSTANCE_NUMBER of the running instance (can query V$INSTANCE for obtaining), or set the FORCE parameter to TRUE.

### YAS-06807:ERR_PL_JOB_NOT_RUNNING

**Message**: job %s.%s is not running

**Action**: The scheduled job has stopped; please select a currently running scheduled job to execute the STOP_JOB interface.

### YAS-06808:ERR_PL_SUBTYPE_INVALID_SCHEMA

**Message**: supertypes's AUTHID is DEFINER but supertype and subtype are not in same schema

**Action**: Please create the subtype in the schema of the supertype.

### YAS-06809:ERR_PL_INVALID_DIRECTORY_NAME

**Message**: invalid or missing directory name

**Action**: Please enter a valid directory object name.

### YAS-06810:ERR_PL_TYPECOMPILE_TAB_DEPERR

**Message**: cannot alter with COMPILE option a valid type with table dependents

**Action**: No need to perform the COMPILE action.

### YAS-06811:ERR_PL_FORALL_EXCEPTION_INIT_ERRORS

**Message**: error(s) in reference to an uninitialized collection

**Action**: Please check if the FORALL statement has been executed.

### YAS-06812:ERR_PL_FORALL_IN_BIND_ERRORS

**Message**: DML statement without BULK In-BIND cannot be used inside FORALL

**Action**: Please check whether index i appears as an index of the collection under the FORALL statement.

### YAS-06813:ERR_PL_FORALL_I_INVALID_CONTEXT_ERRORS

**Message**: FORALL iteration variable string is not allowed in this context

**Action**: Please check whether index i appears in isolation (not as an index of the collection) or as part of an expression under the FORALL statement.

### YAS-06814:ERR_PL_FORALL_BIND_FORM_ERRORS

**Message**: references to fields of BULK In-BIND table of records or objects must have the form A(I).F

**Action**: Please replace FORALL statement with a FOR loop or modify references to fit the allowed form.

### YAS-06815:ERR_PL_FORALL_MUTIPLE_I_ERRORS

**Message**: index specified to access SQL bulk attribute is not allowed

**Action**: Please use a single index of integer data type.

### YAS-06816:ERR_PL_FORALL_INTO_INDEX_ERRORS

**Message**: FORALL bulk index cannot be used in INTO clause

**Action**: Please remove the FORALL index or related expressions from the INTO statement.

### YAS-06817:ERR_PL_FORALL_DML_ERRORS

**Message**: error(s) in array DML

**Action**: Please refer to the error messages in the FORALL exception cursor to modify the DML statements.

### YAS-06818:ERR_PL_VARRAY_OF_CURSOR

**Message**: index tables of cursor variables are disallowed

**Action**: Please modify the definition to avoid using cursors as elements in varray or table types.

### YAS-06819:ERR_PL_INVALID_TEMP_LOB

**Message**: invalid temporary lob

**Action**: Invalid temporary LOB cannot be used.

### YAS-06820:ERR_PL_REPEATED_OPEN_LOB

**Message**: cannot open a lob that has already been opened

**Action**: Cannot reopen an already opened LOB.

### YAS-06821:ERR_PL_CLOSE_UNOPENED_LOB

**Message**: cannot close an unopened lob

**Action**: Cannot close a LOB that is not opened.

### YAS-06822:ERR_PL_UPDATE_READONLY_LOB

**Message**: cannot update a lob opened in readonly mode

**Action**: Cannot update a LOB that is opened in read-only mode.

### YAS-06823:ERR_PL_NO_SQLTYPE

**Message**: expressions have to be of SQL types

**Action**: Please modify the expression type to SQL types.

### YAS-06824:ERR_PL_BAD_BIND_VARIABLE

**Message**: bad bind variable %s

**Action**: Improperly used bind parameters.

### YAS-06825:ERR_PL_WRONG_TYPE

**Message**: expression is of wrong type

**Action**: Please change the data type of the expression; you may use the corresponding data type conversion function as needed.

### YAS-06826:ERR_PL_LOCAL_COLLECTION_TYPE

**Message**: local collection types not allowed in SQL statements

**Action**: Please define collection types in the schema, not in PL subprograms.

### YAS-06827:ERR_PL_RUN_JOB_IN_UPGRADE_MODE

**Message**: cannot run job in upgrade mode

**Action**: Scheduled jobs cannot be executed in upgrade mode.

### YAS-06828:ERR_PL_INVALID_OUT_MODES

**Message**: out and in/out modes cannot be used in this context

**Action**: Please adjust the parameter direction in the USING clause according to the prompt.

### YAS-06829:ERR_PL_CMP_LOB_VARRAY_ERR

**Message**: cannot compare VARRAY or LOB type

**Action**: Create MAP or ORDER methods for the OBJECT type, remove related clauses for VARRAY and TABLE types.

### YAS-06830:ERR_PL_INVALID_TYPE_USE

**Message**: invalid use of type name

**Action**: Invalid usage of type name.

### YAS-06831:ERR_PL_INVALID_VARRAY_DELETE

**Message**: varray delete invalid, %s

**Action**: Please adjust the parameters of the DELETE method as indicated.

### YAS-06832:ERR_PL_INVALID_INDEX_BY_TYPE

**Message**: table index by type is invalid

**Action**: Unsupported table index type.

### YAS-06833:ERR_PL_INVALID_INDICES_SOVAR

**Message**: collection with index by char is not allowed here

**Action**: Collection type variables indexed by strings are prohibited as objects of INDICES OF or VALUES OF.

### YAS-06834:ERR_PL_LBAC_CHECK_ENABLE

**Message**: label Security not enabled

**Action**: Please check if LBAC switch is turned on.

### YAS-06835:ERR_PL_LBAC_POLICY_INVALID_OPT

**Message**: invalid policy option: %s

**Action**: Please modify the control type to a supported type.

### YAS-06836:ERR_PL_LBAC_POLICY_NOT_EXIST

**Message**: policy %s not found

**Action**: Please specify an existing security policy name and adjust accordingly.

### YAS-06837:ERR_PL_LBAC_LEVEL_NOT_EXIST

**Message**: undefined level %d for policy %s

**Action**: Please specify a level that exists in the policy and adjust accordingly.

### YAS-06838:ERR_PL_LBAC_COMPART_NOT_EXIST

**Message**: undefined compartment %d for policy %s

**Action**: Please specify a range that exists in the policy and adjust accordingly.

### YAS-06839:ERR_PL_LBAC_LABEL_NOT_EXIST

**Message**: label %ld does not exist for policy %s

**Action**: Please specify a label that exists in the policy and adjust accordingly.

### YAS-06840:ERR_PL_LBAC_INVALID_LABEL

**Message**: invalid label string: %s

**Action**: The label content must be valid; please adjust accordingly.

### YAS-06841:ERR_PL_LBAC_LABEL_TAG_EXIST

**Message**: label tag %ld already exists

**Action**: The label value cannot be duplicated; please adjust accordingly.

### YAS-06842:ERR_PL_LBAC_LABEL_EXIST

**Message**: label %s already exists

**Action**: The label content cannot be repeated; please adjust accordingly.

### YAS-06843:ERR_PL_LBAC_PLY_ALREADY_APP_TB

**Message**: policy already applied to table

**Action**: Do not apply the same policy to the table again.

### YAS-06844:ERR_PL_LBAC_MAX_RW_LEVEL_UNEQUAL

**Message**: max write level does not equal max read level

**Action**: The level of the maximum write label must be the same as that of the maximum read label; please adjust accordingly.

### YAS-06845:ERR_PL_LBAC_GOMPNT_NOAUTHORIZE

**Message**: not authorized for read or write on specified groups or compartments

**Action**: The content of the default read or write label does not meet the requirements; please adjust accordingly.

### YAS-06846:ERR_PL_LBAC_MINLABEL_CONTAIN

**Message**: minimum label can contain a level only

**Action**: The minimum write label can only contain levels; please adjust accordingly.

### YAS-06847:ERR_PL_LBAC_DEF_LEVEL_TOO_LARGE

**Message**: default level is greater than the user's maximum

**Action**: The level of the default label cannot exceed the user's maximum read/write label level; please adjust accordingly.

### YAS-06848:ERR_PL_LBAC_ADD_TB_COLUMN

**Message**: cannot change HIDDEN property of column

**Action**: Please reselect the label column corresponding to the table.

### YAS-06849:ERR_PL_LBAC_UNAUTHORIZED_SQL

**Message**: unauthorized SQL statement for policy %s

**Action**: Please recreate the label information for the specified policy.

### YAS-06850:ERR_PL_LBAC_COMPONENT_USED

**Message**: %s in use by existing %s labels

**Action**: Before deleting a level or a range, ensure that all related labels of the corresponding policy have been deleted.

### YAS-06851:ERR_PL_LBAC_INVALID_ILABEL

**Message**: invalid internal label

**Action**: The given string needs to be a valid string composed of the values of policy, component type, level, range, and group; please adjust accordingly.

### YAS-06852:ERR_PL_INVALID_INPUT_VALUE

**Message**: invalid input value for %s parameter

**Action**: Please enter a valid parameter value for the prompted parameter.

### YAS-06853:ERR_PL_ACCESS_POLICY_CONFLICT

**Message**: fine grain access policy conflicts with materialized view

**Action**: Creating materialized views cannot include label columns created with LBAC; please adjust accordingly.

### YAS-06854:ERR_PL_UDT_SUPER_VALUE_ERROR

**Message**: cannot assign supertype instance to subtype

**Action**: Please use type or subtype assignments.

### YAS-06855:ERR_PL_UDT_TREAT_TYPE_ERROR

**Message**: type %s must be a supertype or subtype of the TREAT expression

**Action**: Please modify the expression or type.

### YAS-06856:ERR_PL_TOO_MANY_PART_OF_NAME

**Message**: input object name has too many parts

**Action**: Please enter a valid object name.

### YAS-06857:ERR_PL_HTTP_INIT_FAILED

**Message**: the UTL_HTTP pkg initialization failed, %s

**Action**: Check initialization parameters.

### YAS-06858:ERR_PL_HTTP_REQUEST_FAILED

**Message**: the HTTP request failed, %s

**Action**: Check the target server or network connectivity.

### YAS-06859:ERR_PL_HTTP_BAD_ARGUMENT

**Message**: bad argument, %s

**Action**: Enter valid API parameters.

### YAS-06860:ERR_PL_HTTP_BAD_URL

**Message**: the URL is bad

**Action**: Enter a valid URL.

### YAS-06861:ERR_PL_HTTP_PROTOCOL_ERROR

**Message**: HTTP protocol error

**Action**: Check the HTTP service address or port you are connecting to.

### YAS-06862:ERR_PL_HTTP_UNKNOWN_SCHEME

**Message**: unknown or unsupported URL scheme

**Action**: The protocol type in the URL is not recognized. 

### YAS-06863:ERR_PL_HTTP_HEADER_NOT_FOUND

**Message**: the HTTP header is not found

**Action**: The HTTP response header is missing. Verify the request content or server response.

### YAS-06864:ERR_PL_HTTP_END_OF_BODY

**Message**: the end of response body is reached

**Action**: The response message body has been fully read.

### YAS-06865:ERR_PL_HTTP_ILLEGAL_CALL

**Message**: the API call is illegal at this stage

**Action**: The API call does not follow the expected workflow.

### YAS-06866:ERR_PL_HTTP_REDIRECT_ERROR

**Message**: HTTP redirect error

**Action**: HTTP service redirection error (3xx status code returned). 

### YAS-06867:ERR_PL_HTTP_CLIENT_ERROR

**Message**: HTTP client error

**Action**: HTTP client error (4xx status code returned).

### YAS-06868:ERR_PL_HTTP_SERVER_ERROR

**Message**: HTTP server error

**Action**: HTTP server error (5xx status code returned).

### YAS-06869:ERR_PL_HTTP_TOO_MANY_REQUESTS

**Message**: too many open requests or responses

**Action**: Too many concurrent HTTP requests are open.

### YAS-06870:ERR_PL_HTTP_PARTIAL_MULTIBYTE_CHAR

**Message**: partial multibyte character

**Action**: An incomplete multibyte character was encountered.

### YAS-06871:ERR_PL_HTTP_TRANSFER_TIMEOUT

**Message**: transfer time-out occurred

**Action**: HTTP message communication timed out.

### YAS-06872:ERR_PL_HTTP_NETWORK_ACCESS_DENIED

**Message**: network access denied

**Action**: Network access is unauthorized.  

### YAS-06873:ERR_PL_HTTP_REQUEST_BODY_LIMITED

**Message**: for HTTP methods other than PUT and POST, the maximum size limit for the request body is 65534 bytes

**Action**: The HTTP request body is too large. It is recommended to use the `PUT` or `POST` method to send the data.

### YAS-06900:ERR_PL_INVALID_SECURITY_LEVEL

**Message**: DBMS_SQL.OPEN_CURSOR failed. security_level is outside the valid range of 0 to 2

**Action**: Please modify the security_level parameter of DBMS_SQL.OPEN_CURSOR.

### YAS-06901:ERR_PL_DBMS_SQL_ACCESS_DENIED

**Message**: DBMS_SQL access denied

**Action**: Please enter the correct cursor ID.

### YAS-06902:ERR_PL_DBMS_SQL_INVALID_STMT

**Message**: given statement is not supported by package DBMS_SQL

**Action**: Please enter a valid statement.

### YAS-06903:ERR_PL_INVALID_LANGUAGE_FLAG

**Message**: unsupported client compatibility mode used when talking to the server

**Action**: Please enter a valid client compatibility mode.

### YAS-06904:ERR_PL_BIND_VARIABLE_NOT_FOUND

**Message**: bind variable does not exist

**Action**: The bind parameter does not exist; please enter a valid bind parameter name.

### YAS-06905:ERR_PL_NUMERIC_OR_VALUE_ERROR

**Message**: numeric or value error

**Action**: Value error, please correct and retry.

### YAS-06906:ERR_PL_NO_STATEMENT_PARSED

**Message**: no statement parsed

**Action**: Please parse the statement first.

### YAS-06907:ERR_PL_VARIABLE_NOT_IN_SELECT

**Message**: variable not in select list

**Action**: Please enter a valid position.

### YAS-06908:ERR_PL_DBMS_SQL_TYPE_MISMATCH

**Message**: type of out argument must match type of column or bind variable

**Action**: Please enter a column or bind variable of matching type.

### YAS-06909:ERR_PL_DBMS_SQL_USERID_CHANGED

**Message**: effective userid are not the same as when cursor was parsed

**Action**: Please check if it is the same as the user when parsed.

### YAS-06910:ERR_PL_DEFINE_REGULAR_AND_ARRAY

**Message**: cursor contains both regular and array defines which is illegal

**Action**: Cannot simultaneously define column and define array for the same cursor.

### YAS-06911:ERR_PL_BIND_AND_DEFINE_ARRAY

**Message**: cursor contains both bind and define arrays which is not permissible

**Action**: Cannot simultaneously bind array and define array for the same cursor.

### YAS-06912:ERR_PL_BIND_ARRAY_NO_ELEMENTS

**Message**: collection bound by bind_array contains no elements

**Action**: Nested Table variables bound by bind_array cannot have no members.

### YAS-06913:ERR_PL_BIND_ARRAY_INVALID_INDEX

**Message**: invalid bind_array index argument, reason: %s

**Action**: The index parameter of bind_array is incorrect; please correct and retry.

### YAS-06914:ERR_PL_LBAC_APPLY_SYS_SCHEMA

**Message**: cannot apply policies for system tables or set authorizations for sys user

**Action**: Do not apply row access control policies to non-system tables or non-SYS users.

### YAS-06915:ERR_PL_PACK_INIT_DEPDENT_SELF

**Message**: variable initialization may not refer to functions declared in the same package

**Action**: Variable initialization cannot reference functions in the current package.

### YAS-06916:ERR_PL_USE_LOCAL_UDT_BEFORE_DEFINE

**Message**: local user define type cannot be used before definition

**Action**: Local user-defined types (UDT) need to be used after definition.

### YAS-06917:ERR_PL_RECURSIVE_USE_CURSOR

**Message**: cannot recursively reference cursor

**Action**: Please modify the use case; do not recursively use cursors in the cursor's query statement.

### YAS-06918:ERR_PL_ACCESS_SERIALLY_PACK

**Message**: cannot access serially reusable package %s

**Action**: Cannot use a PL package marked as serially reusable in SQL statement or triggers.

### YAS-06919:ERR_PL_BULK_INTO_INVALID_VARIABLE

**Message**: collection type variable expected in "bulk collect into" clause

**Action**: A collection type variable is required in the "bulk collect into" clause.

### YAS-06920:ERR_PL_HIST_CHECK_TABLE_TYPE_ERROR

**Message**: hist_check is not supported on %s

**Action**: The HIST_CHECK advanced package cannot be called on tables other than normal tables.

### YAS-06921:ERR_PL_PIPEFUNC_RETURN_UDT

**Message**: PL/SQL pipelined functions must have a supported collection return type

**Action**: The return type defined for the pipelined function is not supported.

### YAS-06922:ERR_PL_NO_DATA_NEEDED

**Message**: no more rows needed

**Action**: The pipelined function stops writing row data.

### YAS-06923:ERR_PL_CALL_WRONG_NUM_OR_TYPE_ARGS

**Message**: wrong number or types of arguments in call to '%s'

**Action**: The number or type of parameters for the subprocedure call is incorrect.

### YAS-06924:ERR_PL_CALL_TO_MANY_MATCHED

**Message**: too many declarations of '%s' match this call

**Action**: The subprocedure call matches multiple declarations.

### YAS-06925:ERR_PL_PIPEFUNC_STATE_EXCEPTION

**Message**: PL/SQL pipelined function occurs exceptional state: '%s'

**Action**: An exception occurred during the execution of the pipelined function; please retry.

### YAS-06926:ERR_PL_NEW_OLD_DUPLICATED

**Message**: OLD and NEW values cannot be identical

**Action**: Change the trigger OLD and NEW to different aliases.

### YAS-06930:ERR_PL_UDT_OVERRIDE_ERROR

**Message**: method does not override

**Action**: UDT function overriding was not found in the source function.

### YAS-06931:ERR_PL_UDT_MISS_OVERRIDING

**Message**: overriding method requires OVERRIDING keyword

**Action**: The UDT function overriding is missing the 'OVERRIDING' keyword.

### YAS-06932:ERR_PL_UDFAGGR_NO_PLSQL

**Message**: aggregate functions are not allowed %s

**Action**: Cancel the use of custom aggregate functions in the corresponding scenario.

### YAS-06933:ERR_PL_UDFAGGR_EXEC_ERROR

**Message**: error in executing %s callout

**Action**: Check the error scene in the method definition of the custom aggregate function.

### YAS-06934:ERR_PL_UDFAGGR_ARG_ERROR

**Message**: incorrect number of arguments for aggregate function

**Action**: Define at least one parameter for the aggregate function.

### YAS-06935:ERR_PL_CANNOT_EXECUTE

**Message**: cannot execute %s

**Action**: Check the corresponding error branch of the stored procedure.

### YAS-06936:ERR_PL_UDF_DECLARE_NO

**Message**: at most one declaration for '%s' is permitted

**Action**: Keep only one declaration.

### YAS-06937:ERR_PL_INVALID_PARAM_VALUE

**Message**: the value of parameter %s is invalid

**Action**: Please set a valid parameter.

### YAS-06938:ERR_PL_INVALID_ARGS_NUM

**Message**: argument %d is null, invalid, or out of range

**Action**: Please set a valid parameter.

### YAS-06939:ERR_PL_INVALID_GREATER_AMOUNT

**Message**: specified input amount is greater than actual source amount

**Action**: Please set a valid amount.

### YAS-06940:ERR_PL_OFFSET_BEYOND_SOURCE_LOB

**Message**: source offset is beyond the end of the source LOB

**Action**: Please set a valid source offset.

### YAS-06941:ERR_PL_INVALID_LOB_LOCATOR

**Message**: numeric or value error: invalid LOB locator specified, %s

**Action**: Please specify a bfile locator.

### YAS-06943:ERR_PL_INVALID_LINE

**Message**: invalid plsql line

**Action**: Please enter the correct PL statements.

### YAS-06944:ERR_PL_FORALL_EXEC_OPT

**Message**: can not do optimized forall insert, try non-optimized forall insert

**Action**: Internal error, please contact our technical support for resolution.

### YAS-06945:ERR_PL_COMPLEX_VAR_INIT

**Message**: variable initialization is too complex

**Action**: Variable initialization is too complex.

### YAS-06946:ERR_PL_UDF_INDEX_DEPEND

**Message**: cannot drop or replace a function index dependents

**Action**: Please delete the corresponding index first and then retry. You can execute `SELECT * FROM DBA_DEPENDENCIES WHERE TYPE = 'INDEX' AND REFERENCED_TYPE = 'UDF'` to get the related indexes.

- If the error occurs when dropping a USER, the WHERE clause also needs to add the `REFERENCED_OWNER = username` condition.

- If the error occurs when dropping a FUNCTION, the WHERE clause also needs to add the `REFERENCED_OWNER = username AND REFERENCED_NAME = functionname` condition.

### YAS-06947:ERR_PL_FUNCTION_WRONG_STATE

**Message**: function which index depends on is in wrong state

**Action**: Please verify whether the objects on which the function depends are functioning properly.

### YAS-06948:ERR_PL_CALL_THROUGH_PACK_ONLY

**Message**: call this pack subprogram through package %s only

**Action**: Please invoke the subroutine through the specified package.

<span id="errno7001" name="errno7001"></span>

### YAS-07201:ERR_YSPI_INIT_FAILED

**Message**: failed to initialize plug-in library %s, reason:%s

**Action**: Please check if the plug-in library is valid.

### YAS-07202:ERR_YSPI_ERROR

**Message**: plugin execution error, %s

**Action**: Please check if the plug-in library is valid.

### YAS-07203:ERR_YSPI_GEOS

**Message**: geos library error: %s

**Action**: Please check if the geos library is valid.

### YAS-07204:ERR_YSPI_VERIFY_ERROR

**Message**: no function matches the given name and argument types

**Action**: Please check whether the input function's parameter types and quantity are correct.

### YAS-07205:ERR_YSPI_INVALID_TYPE_ERROR

**Message**: unexpected column %s type. Expected %s

**Action**: Please check whether the data types of the table columns are correct.

### YAS-07301:ERR_YEX_TIMEOUT

**Message**: external module timeout, reason: %s

**Action**: Please check if the yex_server service is running properly.

### YAS-07302:ERR_YEX_PROTOCOL_ERROR

**Message**: external module protocol error, reason: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07303:ERR_YEX_LIBFILE_NOT_FOUND

**Message**: library file %s not found

**Action**: Please check if the library file exists.

### YAS-07304:ERR_YEX_INVALID_LIBFILE

**Message**: invalid library file %s

**Action**: Please check if the specified library file is valid.

### YAS-07305:ERR_YEX_TOO_MANY_PROCS

**Message**: too many external procedures

**Action**: Too many external UDFs have been created; please remove unnecessary external UDFs and retry.

### YAS-07306:ERR_YEX_TOO_MANY_LIBS

**Message**: too many external libraries

**Action**: Too many custom libraries have been created; please remove unnecessary custom libraries and retry.

### YAS-07307:ERR_YEX_TOO_MANY_DBLINKS

**Message**: too many dblinks

**Action**: Too many dblinks have been created; please remove unnecessary dblinks and retry.

### YAS-07308:ERR_YEX_OBJECT_INVALIDATED

**Message**: external object is invalidated

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07309:ERR_YEX_SIZE_TOO_LARGE

**Message**: the size of yex packet is too large

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07310:ERR_YEX_UNSUPPORTED_DATATYPE

**Message**: unsupported data type %s used by %s

**Action**: Please enter a supported input parameter type by YEX.

### YAS-07311:ERR_YEX_OBJECT_BUSY

**Message**: object %s.%s is busy

**Action**: The YEX object is in use, please try again later.

### YAS-07312:ERR_YEX_OBJECT_NOT_EXIST

**Message**: object %s.%s does not exist

**Action**: Please check if the YEX object exists.

### YAS-07313:ERR_YEX_DRIVER_ERROR

**Message**: database driver error: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07314:ERR_YEX_TOO_MANY_CONNS

**Message**: too many connections for dblink %s

**Action**: The number of connections for dblinks is too high; please disconnect some connections and retry.

### YAS-07315:ERR_YEX_TOO_MANY_RESULTSETS

**Message**: too many dblink resultsets

**Action**: The current number of concurrent DBLink connections is too high, and there are not enough cursors available. Please increase the value of the system parameter DBLINK_CURSOR_COUNT to allow more concurrent DBLink connections.

### YAS-07316:ERR_YEX_TOO_MANY_DBLINK_CURSORS

**Message**: too many dblink cursors

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07317:ERR_YEX_CONNECT_FAILED

**Message**: can't establish a connection to external module

**Action**: Please check if the yex_server is running properly.

### YAS-07318:ERR_YEX_CALL_FAILED

**Message**: failed to call external module

**Action**: The execution of the external UDF failed; please check if the execution is legitimate according to the error message.

### YAS-07319:ERR_YEX_SERVICE_TOO_OLD

**Message**: external module service is too old

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07320:ERR_YEX_SERVER_RUN_ERROR

**Message**: failed to run yex_server, error no: %d

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07321:ERR_YEX_INVALID_OUT_ARG_ID

**Message**: invalid out/in out argument id: %d

**Action**: Please use a valid out/in out parameter ID.

### YAS-07322:ERR_YEX_OUT_ARG_WRONG_TYPE

**Message**: set out/in out argument with wrong type, expect %s type, actual %s type

**Action**: Please use out/in out parameters of the correct type.

### YAS-07323:ERR_YEX_NULL_RETURN

**Message**: external function must set return value

**Action**: Please set a return value in the external UDF.

### YAS-07324:ERR_YEX_INVALID_RETURN

**Message**: external procedure invalid return

**Action**: Please do not set a return value in the external stored procedure.

### YAS-07325:ERR_YEX_INVALID_ARG_ID

**Message**: invalid argument id: %d

**Action**: Please use a valid argument ID.

### YAS-07326:ERR_YEX_BINDING_TYPE_MISMATCH

**Message**: data type of binding value mismatch

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07327:ERR_YEX_BINDING_SIZE_TOO_LONG

**Message**: the length of binding value is too long

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07328:ERR_YEX_PARAMSET_SIZE_LIMITED

**Message**: array's length is limited by param set size

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07329:ERR_YEX_NOT_ALL_PARAMS_BIND

**Message**: not all parameters bound

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07330:ERR_YEX_TOO_MANY_XACTS

**Message**: too many active dblink transactions

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07331:ERR_YEX_TOO_MANY_XACT_BRANCHS

**Message**: transaction branches in same session can not exceed %d

**Action**: The total number of different dblinks transactions under the same user session cannot exceed the specified limit.

### YAS-07332:ERR_YEX_SQLTEXT_TOO_LONG

**Message**: the length of dblink sql text must less than %lu

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07333:ERR_YEX_TOTAL_BINDING_SIZE

**Message**: the total binding size is too large

**Action**: Internal error, please contact our technical support for resolution.

### YAS-07334:ERR_YEX_TOO_MANY_SESSIONS

**Message**: too many sessions call external module

**Action**: The concurrent usage of the external module exceeds the limit; please reduce the concurrency.

### YAS-07335:ERR_YEX_CHECK_REMOTE_INFO

**Message**: remote data check error: %s

**Action**: Please retry in a stable environment.

### YAS-07336:ERR_YEX_DATABASE_LINK_IN_USE

**Message**: database link is in use

**Action**: Please clean up the transactions in the DBLink before closing the DBLink.

### YAS-07337:ERR_YEX_DATABASE_LINK_NOT_OPEN

**Message**: database link is not open

**Action**: The DBLink connection is currently not open.

<span id="errno8001" name="errno8001"></span>

### YAS-08001:ERR_YAC_UNKNOWN_HANDLE_TYPE

**Message**: failed to allocate handler, unknown type %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-08002:ERR_YAC_FUNCTION_SEQUENCE

**Message**: function sequence error

**Action**: Internal error, please contact our technical support for resolution.

### YAS-08003:ERR_YAC_INVALID_SERVICENAME

**Message**: invalid name or URL of service

**Action**: Please use the correct server name or link.

### YAS-08004:ERR_YAC_BUFFER_SIZE_SMALL

**Message**: the buffer size of %s is too small

**Action**: Please provide the required size of Cache for the data.

### YAS-08005:ERR_YAC_ATTR_READONLY

**Message**: attribute is readonly

**Action**: Please execute statements on the read-write node.

### YAS-08006:ERR_YAC_CONNECTION_RESET

**Message**: connection is reset

**Action**: Please quit and reconnect to the server.

### YAS-08007:ERR_YAC_FREE_UNKNOWN_HANDLE

**Message**: failed to free handler, unknown type %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-08008:ERR_YAC_NOT_ALL_BIND

**Message**: not all variables bounded

**Action**: Please check if the number of parameters matches.

### YAS-08009:ERR_YAC_UNKNOWN_TYPE

**Message**: failed to allocate descript, unknown type %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-08010:ERR_YAC_CONNECTION_INVALID

**Message**: invalid connection

**Action**: Please quit and reconnect to the server.

### YAS-08011:ERR_YAC_ALLOC_HANDLE_INVALID

**Message**: allocate invalid handler

**Action**: Internal error, please contact our technical support for resolution.

### YAS-08012:ERR_YAC_DISCONNECTION

**Message**: connection has been disconnected

**Action**: Please quit and reconnect to the server.

### YAS-08013:ERR_YAC_FETCH_INVALID

**Message**: invalid fetch

**Action**: Please re-execute the statement.

### YAS-08014:ERR_YAC_PACKET_SIZE_INVALID

**Message**: invalid packet size

**Action**: Please modify the packet range to be within a valid range (64KB ~ 32MB).

### YAS-08015:ERR_YAC_INPUT_HANDLE_INVALID

**Message**: invalid input handle, expected %s

**Action**: Please quit and reconnect to the server.

### YAS-08016:ERR_YAC_SQL_TYPE_INVALID

**Message**: invalid sql type

**Action**: Please check if the input statement is correct.

### YAS-08017:ERR_YAC_BIND_PARAM_INVALID

**Message**: binding param id cannot be less than 1

**Action**: Please check if the number of parameters matches.

### YAS-08018:ERR_YAC_PARTITION_TYPE_INVALID

**Message**: invalid partition type

**Action**: Please check if the partition type is correct.

### YAS-08019:ERR_YAC_PARTITION_ID_INVALID

**Message**: partition key does not map to any partition

**Action**: Please check if the partition identifier is correct.

### YAS-08020:ERR_YAC_BAT_INS_SQL_INVALID

**Message**: remove the CREATE TABLE clause

**Action**: This operation is not allowed; please remove the CREATE TABLE statement.

### YAS-08021:ERR_YAC_DATA_TYPE_INVALID

**Message**: invalid data type: %s

**Action**: Please check if the data type matches.

### YAS-08022:ERR_YAC_INVALID_PUMP_PARAM

**Message**: invalid pump parameter value

**Action**: Please modify the parameter value; string type must not exceed 8192 bytes.

### YAS-08023:ERR_YAC_INVALID_PUMP_FILE_FORMAT

**Message**: invalid file format, %s

**Action**: Please check the statement based on specific information.

### YAS-08024:ERR_YAC_TABLESPACE_NOT_FOUND

**Message**: the tablespace '%s' is not found

**Action**: Please use an existing tablespace or create a new one.

### YAS-08025:ERR_YAC_INVALID_PUMP_PARAM_VALUE

**Message**: invalid value of parameter %s

**Action**: Please check if the parameter value is valid.

### YAS-08026:ERR_YAC_IMPORTING_BROKEN

**Message**: importing broken

**Action**: Please fix according to the prompt and re-import the data.

### YAS-08027:ERR_YAC_ILLEGAL_ATTR_VALUE

**Message**: the value of %s is illegal

**Action**: Please use a valid value.

### YAS-08028:ERR_YAC_UNKNOWN_ATTR

**Message**: unknown attribute id

**Action**: Please check if the attribute name is correct.

### YAS-08029:ERR_YAC_INVALID_PUMP_PRIV

**Message**: %s may not %s other users

**Action**: Please operate with a DBA user.

### YAS-08030:ERR_YAC_TOO_MANY_PUMP_OBJ

**Message**: too many %s for %s

**Action**: Please reduce the number of objects.

### YAS-08031:ERR_YAC_INVALID_PUMP_TABLE

**Message**: cannot qualify table name by owner %s, %s

**Action**: Please use the FROMUSER parameter to specify the user of the table.

### YAS-08032:ERR_YAC_CONFLICT_PUMP_MODE

**Message**: conflicting modes specified

**Action**: Please reduce the import/export modes and retry.

### YAS-08033:ERR_YAC_USER_NOT_FOUND

**Message**: user %s does not exist

**Action**: Please use an existing user or create the user.

### YAS-08034:ERR_YAC_TABLE_NOT_FOUND

**Message**: table %s.%s does not exist

**Action**: Please use an existing table or create that table.

### YAS-08035:ERR_YAC_OBJECT_NOT_IN_FILE

**Message**: %s %s not found in export file

**Action**: Please recreate that object.

### YAS-08036:ERR_YAC_TEMP_LOB_INVALID

**Message**: invalid temp lob

**Action**: Please create a valid temporary LOB first.

### YAS-08037:ERR_YAC_PARAM_NOT_FOUND

**Message**: param name %s not found

**Action**: Please enter the correct parameter name.

### YAS-08039:ERR_YAC_TXN_ISOLATION_INVALID

**Message**: invalid transaction isolation level

**Action**: Please enter the correct transaction isolation level: READ COMMITTED/SERIALIZABLE.

### YAS-08040:ERR_YAC_PASSWORD_INVALID

**Message**: invalid password

**Action**: Please enter the correct password.

### YAS-08041:ERR_YAC_CHARSET_CODE_INVALID

**Message**: invalid charset code,the value must be one of ASCII GBK UTF8 ISO88591 GB18030.

**Action**: Please enter the correct charset, which must be one of ASCII, GBK, UTF8, ISO88591, GB18030.

### YAS-08042:ERR_YAC_CHARSET_INVALID

**Message**: invalid charset,the value must be one of ASCII GBK UTF8 ISO88591 GB18030.

**Action**: Please enter the correct encoding, which must be one of ASCII, GBK, UTF8, ISO88591, GB18030.

### YAS-08043:ERR_YAC_DATE_FORMAT_INVALID

**Message**: invalid value for date format

**Action**: YashanDB does not support the specified date format; please adjust and retry.

### YAS-08044:ERR_YAC_PASSWORD_NOT_MATCH

**Message**: new passwords do not match

**Action**: Please check that the two entered passwords match.

### YAS-08045:ERR_YAC_INVALID_NULL_PTR

**Message**: invalid null pointer

**Action**: Please check for illegal null pointer inputs.

### YAS-08046:ERR_YAC_BOUND_TOO_LARGE

**Message**: partition bound value '%s' is invalid, expected size not more than %u, actual size is %u

**Action**: Please ensure the data length does not exceed the length of the corresponding partition column.

### YAS-08047:ERR_YAC_DATA_INVALID

**Message**: invalid data path

**Action**: Please configure the correct YASDB_DATA path (the path must be absolute and not exceed 256 characters).

### YAS-08048:ERR_YAC_EXTPROC_STREAM_ERROR

**Message**: extproc stream error, reason: %s

**Action**: Internal error, please contact our technical support for resolution.

### YAS-08049:ERR_YAC_UNSUPPORTED_GET_DATA

**Message**: unsupported yacGetData invocation, please set YAC_ATTR_GET_DATA_SUPPORT

**Action**: The current invocation of yacGetData is not supported; please set the YAC_ATTR_GET_DATA_SUPPORT attribute to true.

### YAS-08050:ERR_YAC_NO_RESULTSET_FOR_GET_DATA

**Message**: no resultset cached for yacGetData

**Action**: There is currently no cached result set for the yacGetData invocation.

### YAS-08051:ERR_YAC_LOB_INVALID

**Message**: invalid lob

**Action**: Invalid LOB, please create or bind to get a valid LOB.

### YAS-08052:ERR_YAC_FEATURE_NOT_SUPPORT

**Message**: this feature is not supported in the current version

**Action**: This functionality is not supported in the current version.

### YAS-08053:ERR_YAC_PROVIDE_LITTLE_DATA

**Message**: provide less data for writing than specified

**Action**: The amount of data provided for writing is less than specified; please adjust and retry.

### YAS-08054:ERR_YAC_FREE_UNKNOWN_DESCRIPTOR_TYPE

**Message**: failed to free descriptor, unknown type %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-08055:ERR_YAC_UNKNOWN_DESCRIPTOR_TYPE

**Message**: failed to allocate descriptor, unknown type %u

**Action**: Internal error, please contact our technical support for resolution.

### YAS-08056:ERR_YAC_INVALID_CONN_PARAM_FORMAT

**Message**: invalid connect param format: %s

**Action**: Please check if the connection parameter input format is correct; it should be in the form of key=value.

### YAS-08057:ERR_YAC_INVALID_CONN_PARAM_NAME

**Message**: invalid connect param name: %s

**Action**: Please check if the connect parameter name is correct.

### YAS-08058:ERR_YAC_INVALID_CONN_PARAM_VALUE

**Message**: invalid connect param value: %s for %s

**Action**: Please check if the connect parameter value is correct.

### YAS-08059:ERR_YAC_TAF_CONNECT_FAILED

**Message**: TAF connect failed: connect failed

**Action**: TAF connection failed; please reconnect to the database.

### YAS-08060:ERR_YAC_TAF_CONNECT_SUCCEED

**Message**: TAF connect succeed: please reuse this interface

**Action**: TAF connection succeeded; please re-invoke this interface.

### YAS-08061:ERR_YAC_TAF_CONNECT_SUCCEED_WITH_CB_ERROR

**Message**: TAF connect succeed: callback func return error

**Action**: TAF connection succeeded, but the TAF callback function returned an error; please check the TAF callback function.

### YAS-08062:ERR_YAC_INVALID_MEM_CALLBACK_SET

**Message**: invalid mem callback set

**Action**: The current memory callback function setting is incorrect; please set the correct memory callback function.

### YAS-08063:ERR_YAC_INVALID_ATTR_SET_AFTER_CONNECTED

**Message**: invalid attr set after connected

**Action**: The current attribute does not support setting after successful connection.

### YAS-08064:ERR_YAC_INVALID_PARAMETER

**Message**: invalid parameter, reason: %s

**Action**: Please check the parameter based on the information.

### YAS-08065:ERR_YAC_DATA_TRUNCATED

**Message**: the data has been right truncated

**Action**: The current data has been right-truncated.

### YAS-08066:ERR_YAC_ALL_CONNECTION_WITH_STANDBY

**Message**: invalid connection, primary mode connect to standby

**Action**: Invalid connection; the PRIMARY connection mode only finds the Standby Database; please modify the connection URL.

### YAS-08067:ERR_YAC_PARSE_PARTITION_INFO_ERROR

**Message**: parse partition info error: %s

**Action**: Failed to parse partition information; please contact our technical support for resolution.

### YAS-08068:ERR_YAC_DEBUG_STATUS_ERROR

**Message**: debug status is %s

**Action**: The debug mode status is abnormal.

### YAS-08069:ERR_YAC_DEBUG_FRAMES_NOT_EXIST

**Message**: debug frames information does not exist

**Action**: The stack information for the debugger mode does not exist.

### YAS-08070:ERR_YAC_DEBUG_VARIANTS_NOT_EXIST

**Message**: debug variants information does not exist

**Action**: The variable information for the debugger mode does not exist.

### YAS-08071:ERR_YAC_DEBUG_BP_NOT_EXIST

**Message**: debug breakpoints information does not exist

**Action**: The breakpoint information for the debugger mode does not exist.

### YAS-08072:ERR_YAC_AUTH_NEED_UKEY_INFO_ERROR

**Message**: current user needs ukey to login, please input ukey information: %s

**Action**: The current user login requires UKEY; please enter the UKEY information.

### YAS-08073:ERR_YAC_INVALID_INDICATOR

**Message**: invalid indicator value %u

**Action**: Invalid indicator value.

### YAS-08074:ERR_YAC_VERSION_MISMATCH

**Message**: tool version mismatch

**Action**: Please check the tool version to ensure it is compatible with the server version.

### YAS-08075:ERR_YAC_STMT_TIMEOUT

**Message**: stmt execute time out

**Action**: The current statement execution timed out.

### YAS-08076:ERR_YAC_XA_PREPARE_READ_ONLY

**Message**: XA: transaction branch prepare returns read-only

**Action**: The preparation phase of the transaction process returned read-only.

### YAS-08077:ERR_YAC_XA_INVALID_FLAG

**Message**: XA: invalid flag

**Action**: Invalid XA transaction flag.

### YAS-08401:ERR_RES_CGROUP_ROOT_DIR_LENGTH_TOO_LONG

**Message**: resource control root dir %s is too long

**Action**: Please reduce the length of the cgroup directory path.

### YAS-08402:ERR_RES_CGROUP_ROOT_DIR_HANDLE

**Message**: resource control root dir %s is invalid

**Action**: Failed to process the cgroup file; please check if the filename or file privileges are correct.

### YAS-08403:ERR_RES_CGROUP_NO_MOUNT

**Message**: resource control is not mounted

**Action**: Please mount the cgroup component on Linux.

### YAS-08404:ERR_RES_CGROUP_CREATE_FAIL

**Message**: resource control cgroup name %s is failed to be created

**Action**: Failed to create cgroup; please check if the cgroup directory is correct.

### YAS-08405:ERR_RES_CGROUP_DEL_FAIL

**Message**: resource control fails to delete cgroup and the cgroup name is %s

**Action**: Failed to delete cgroup; please check if the cgroup directory is correct.

### YAS-08406:ERR_RES_CGROUP_ALL_CLOSE

**Message**: cgroup is all close

**Action**: The cgroup is closed. To use cgroup functionality, please turn on the corresponding switch.

### YAS-08407:ERR_RES_CGROUP_STATUS_INVALID

**Message**: operate resource control in invalid database status

**Action**: The current database status does not allow resource management configuration operations.

### YAS-08408:ERR_RES_CGROUP_TOO_MANY_CONSUMER_GROUP

**Message**: number of consumer groups exceeds limit %d

**Action**: The number of resource consumer groups has reached the upper limit; no more resource consumer groups can be configured.

### YAS-08409:ERR_RES_CGROUP_DROP_CONSUMER_GROUP

**Message**: consumer group %s is being used and cannot be deleted

**Action**: Please delete all related scheduled instructions before deleting the consumer group.

### YAS-08410:ERR_RES_CGROUP_CLUSTER_DIR_NOT_EXISTING

**Message**: cgroup directory %s is not existed, please create cgroup directory first

**Action**: The solution varies depending on the scenario:

- Scenario 1: If this error occurs when starting the database after a server restart, it may be because the CPU and IO configuration in cgroup-related files is lost after the server restart, causing the database to fail to start (at this point, the run.log will record the current error). In this scenario, execute the following commands to restore CPU and IO functionality before starting the database.

  ```shell
  $ sudo sh -c 'find /sys/fs/cgroup -name "cgroup.procs" -exec cat {} \; 2>/dev/null | sort -u | while read pid; do [ -n "$pid" ] && echo $pid 2>/dev/null; done > /sys/fs/cgroup/cgroup.procs'
  $ echo "+cpu" | sudo tee /sys/fs/cgroup/cgroup.subtree_control
  $ echo "+io" | sudo tee /sys/fs/cgroup/cgroup.subtree_control
  ```

- For other scenarios, use the `yasboot host cgroup create` command to create the cgroup directory.

### YAS-08411:ERR_RES_CGROUP_DROP_PLAN

**Message**: plan %s is being used and cannot be deleted

**Action**: Please delete all related scheduled instructions before deleting the resource plan.

### YAS-08412:ERR_RES_CGROUP_WRITE_FAIL

**Message**: unable to write cgroup file of consumer group %s

**Action**: Failed to write to the cgroup file; please check if the cgroup directory and privileges are correct.

### YAS-08413:ERR_RES_SESSION_MEM_LIMIT

**Message**: session has reached the maximum memory usage limit, alloc type %s, alloc size %lu, mem limit %lu, session cur size %lu

**Action**: Please increase the upper limit or reduce the resources required for execution.

### YAS-08414:ERR_RES_GROUP_MEM_LIMIT

**Message**: session has reached the maximum memory usage limit of resource group, alloc size %lu, remain size %lu, total size %lu

**Action**: Increase the upper limit or reduce the resources required for the resource usage group.

### YAS-08415:ERR_RES_USER_MEM_QUOTA_CONGESTION

**Message**: user mem quota is in congestion

**Action**: User memory quota contention, will retry.

### YAS-08416:ERR_RES_QUOTA_PX_CONGESTION

**Message**: px res quota is in congestion

**Action**: PX resource quota contention, will retry.

### YAS-08417:ERR_RES_QUOTA_EXCESSIVE

**Message**: res quota exceeding consumer group or session upper limit

**Action**: Please increase the resource quota upper limit or reduce the execution resources.

### YAS-08418:ERR_RES_WAIT_TIMEOUT

**Message**: timeout when waiting for resource

**Action**: Please adjust the system's resource utilization.

### YAS-08419:ERR_RES_GROUP_MEM_ERROR

**Message**: res group user mem quota is lack, alloc type %s

**Action**: Please increase the resource group quota or reduce the resources required for execution.

### YAS-08420:ERR_RES_GROUP_VERSION_CHANGED

**Message**: res group version changed

**Action**: Internal transient error.

<span id="errno9001" name="errno9001"></span>

### YAS-09401:ERR_MYSQL_INVALID_AUTO_COLUMN_DEFINED

**Message**: there can be only one auto column and it must be defined as a key

**Action**: There can currently be only one auto-increment column, which must be defined as an index; please check the settings of the auto-increment column.

### YAS-09402:ERR_MYSQL_INVALID_DEFAULT

**Message**: invalid default value for %s

**Action**: The default value set for a column is invalid; please remove the default value setting.

### YAS-09403:ERR_MYSQL_INVALID_AUTO_INCREMENT

**Message**: invalid autoIncrement

**Action**: The auto-increment property set for a column is invalid; please remove the auto-increment property and set it again.

### YAS-09404:ERR_MYSQL_ACCESS_TABLE_NOT_LOCK

**Message**: table "%s" was not locked with lock tables

**Action**: Please re-access after locking the target table.

### YAS-09405:ERR_MYSQL_INCORRECT_TABLE_NAME

**Message**: Incorrect table name %s

**Action**: Please reset to the correct table name.

### YAS-09406:ERR_MYSQL_GRANT_RECORD_NOT_FOUND

**Message**: There is no such grant defined

**Action**: There are no corresponding authorization records; revoking these privileges failed.

### YAS-09407:ERR_MYSQL_INVALID_COLLATION

**Message**: Unknown collation: %s

**Action**: Please reset the correct character collation.

### YAS-09408:ERR_MYSQL_INVALID_COLLATION_CHARSET

**Message**: COLLATION %s is not valid for CHARACTER SET %s

**Action**: The collation does not match the current character set; please reset the correct collation.

### YAS-09409:ERR_MYSQL_COLLATION_MISMATCH

**Message**: Illegal mix of collations %s and %s

**Action**: Illegal mixing of collations; please reset the correct collation.

### YAS-09410:ERR_MYSQL_FORBID_DEFAULT

**Message**: BLOB, TEXT, GEOMETRY or JSON column %s can't have a default value

**Action**: BLOB, TEXT, GEOMETRY, and JSON columns cannot be set with default values (DEFAULT 'XXX').

<span id="errno10001" name="errno10001"></span>

### YAS-10001:ERR_PARSER_INVALID_PARTSPEC_FORVALUES

**Message**: Invalid partition specification for FOR VALUES clause

**Action**: Please specify valid values in the FOR VALUES clause; the values must be constants and compatible with the partition column type.

### YAS-10002:ERR_ANS_VERIFY_INVALID_VISIBLE_COL_COUNT

**Message**: table must have at least one column that is not invisible

**Action**: Ensure the table has at least one non-hidden column and modify the table definition accordingly.

### YAS-10003:ERR_ANS_VERIFY_INVISIBLE_COL_ON_TABLE_TYPE

**Message**: Invisible column is not supported on this type of table

**Action**: Hidden columns cannot exist in external tables or temporary tables; please modify the table definition accordingly.

### YAS-10004:ERR_ANS_VERIFY_INVALID_JOIN_REFERENCE

**Message**: invalid join reference

**Action**: Invalid join reference. Please check the statement.

### YAS-10005:ERR_ANS_PARSE_INVALID_VALUE_COLUMN

**Message**: invalid user.table.column, table.column, or column specification

**Action**: The value of the function input parameter is incorrect; please check the statement.

### YAS-10006:ERR_ANS_PARSER_REF_VIRTUAL_ON_VIRTUAL

**Message**: virtual column cannot reference another virtual column

**Action**: Virtual columns are not allowed to reference other virtual columns.

### YAS-10007:ERR_ANS_PARSER_VIRTUAL_COLUMN

**Message**: cannot %s a virtual column

**Action**: This operation is not supported for virtual columns.

### YAS-10008:ERR_ANS_VERIFY_COLUMN_SIZE

**Message**: column %s size is too large(actual: %u, maximum: %u)

**Action**: The length of the inserted or updated data exceeds the maximum length limit of the target column.

### YAS-10009:ERR_ANS_VERIFY_INVALID_COLUMN_EXPR

**Message**: Invalid column expression was specified

**Action**: The specified virtual column expression does not meet the requirements. Please modify it and retry.

### YAS-10010:ERR_ANS_VIRTUAL_COLUMN_TYPE

**Message**: specified data type is not supported for a virtual column

**Action**: Please specify a supported data type for the virtual column.

### YAS-10011:ERR_ANS_DUPLICATE_VIRTUAL_COLUMN_EXPR

**Message**: Duplicate column expression was specified

**Action**: Please remove duplicate expressions from the virtual column expression.

### YAS-10012:ERR_ANS_VIRTUAL_COLUMN_EXISTS

**Message**: A virtual column exists for this expression

**Action**: Virtual columns are not allowed in expressions used for function indexes. Please modify.

### YAS-10013:ERR_ANS_MOD_VIRTUAL_COLUMN_DEPS

**Message**: column to be dropped or modified is used in a virtual column

**Action**: The target column is already used by a virtual column and cannot be deleted or modified. If you still wish to perform the current operation, please delete the corresponding virtual column first.

### YAS-10014:ERR_ANS_VIRTUAL_COLUMN_TABLE_TYPE

**Message**: expression column is not supported for %s

**Action**: Virtual columns cannot be created in tables with columnar indexes, columnar tables, or temporary tables.

### YAS-10015:ERR_ANS_VIRTUAL_COLUMN_ENCRYPT

**Message**: Virtual column cannot be encrypted

**Action**: Encryption cannot be applied to virtual columns.

### YAS-10016:ERR_ANS_VIRTUAL_COLUMN_DEFAULT

**Message**: DEFAULT expression not allowed here

**Action**: Adding a default value is not allowed for virtual columns.

### YAS-10017:ERR_ANS_VERIFY_INVALID_VIRTUAL_COL_COUNT

**Message**: table must have at least one column that is not virtual

**Action**: Each table must have at least one non-virtual column. Please modify the table definition.

### YAS-10018:ERR_ANS_ILLEGAL_FUNC_ARGUMENT

**Message**: illegal argument for function

**Action**: Illegal parameters were used in the function. Please modify them and try again.

### YAS-10019:ERR_ANS_VERIFY_INVALID_GROUP_CONST

**Message**：invalid group column

**Action**: Please enter a valid group column.

<span id="errno100001" name="errno100001"></span>

### YAS-100001

**Message**: invalid value of argument AUDIT_TRAIL_TYPE

**Action**: Please set the correct audit cleanup type.

### YAS-100002

**Message**: invalid value of argument AUDIT_TRAIL_STATUS_VALUE

**Action**: Please set the correct audit cleanup task status.

### YAS-100003

**Message**: invalid value of argument LAST_ARCHIVE_TIME

**Action**: Please set the correct audit cleanup timestamp.

### YAS-100004

**Message**: invalid value of argument AUDIT_TRAIL_PURGE_INTERVAL

**Action**: Please set the correct next execution time for the audit task.

### YAS-100005

**Message**: invalid value of argument AUDIT_TRAIL_INTERVAL_VALUE

**Action**: Please set the correct next startup time interval for the audit task.

### YAS-100006

**Message**: Cleanup job already existed for the given audit trail type

**Action**: An audit cleanup job of the specified type already exists; do not duplicate.

### YAS-100010

**Message**: line length overflow, limit of 65534 bytes per line

**Action**: Cache writing causes overflow in the current line; the maximum limit is 65534 bytes per line.

### YAS-100011

**Message**: buffer overflow, limit of %d bytes

**Action**: Cache writing causes overflow; the total length limit of the cache is %d bytes.

### YAS-100020

**Message**: cannot describe a non-existent procedure

**Action**: The specified stored procedure does not exist.

### YAS-100021

**Message**: cannot describe an invalid procedure

**Action**: The specified stored procedure is in an invalid state.

### YAS-100031

**Message**: unsupported function

**Action**: The member function of the specified xmltype is temporarily unsupported.

### YAS-100041

**Message**: invalid RETENTION %d, must be in the range (1440, 52560000)

**Action**: The specified retention period must be between 1 day to 100 years.

### YAS-100042

**Message**: invalid INTERVAL %d, must be in the range (10, 52560000)

**Action**: The specified snapshot creation interval must be between 10 minutes to 100 years.

### YAS-100043

**Message**: the end_snap_id must greater then begin_sap_id

**Action**: The specified end snapshot ID must be greater than the starting snapshot ID.

### YAS-100044

**Message**: the snap_id is not exist

**Action**: The specified snapshot ID does not exist in a snapshot-related system table that does not allow nulls.

### YAS-100045

**Message**: Begin Snapshot Id %d does not exist for this database/instance

**Action**: The specified starting snapshot ID does not exist in the snapshot information table.

### YAS-100046

**Message**: End Snapshot Id %d does not exist for this database/instance

**Action**: The specified end snapshot ID does not exist in the snapshot information table.

### YAS-100047

**Message**: Incorrect format of instance number input

**Action**: The input format for instance information is incorrect. The format is like 1 for standalone or YAC/Distributed Cluster Deployment, and '3-1' for ISC Distributed Cluster Deployment.

### YAS-100048

**Message**: invalid TOPNSQL string: %s

**Action**: The entered TOPNSQL parameter is invalid.

### YAS-100049

**Message**: invalid TOPNSQL %d, must be in the range (30, 50000)

**Action**: The specified TOPNSQL must be between 30 and 50000.

### YAS-100050

**Message**: The instance was shutdown between snapshots %d and %d

**Action**: The instance was restarted between the specified starting and ending snapshots; the report cannot be generated.

### YAS-100051

**Message**: The instance was shutdown or had primary/standby switchover between snapshots %d and %d

**Action**: The instance experienced a restart or primary/standby switch between the specified starting and ending snapshots; the report cannot be generated.

### YAS-100052

**Message**: The instance had primary/standby switchover between snapshots %d and %d

**Action**: The instance experienced a primary/standby switch between the specified starting and ending snapshots; the report cannot be generated.

### YAS-100053

**Message**: create snapshot failed: INTERVAL Setting is 0

**Action**: The snapshot creation interval has been set to 0, disabling manual or automatic snapshot collection.

### YAS-100054

**Message**: operation failed due to invalid snapshot range ( %d, %d )

**Action**: The specified end snapshot ID must be greater than or equal to the starting snapshot ID.

### YAS-100055

**Message**: numeric or value error

**Action**: Invalid value. Correct and retry.
