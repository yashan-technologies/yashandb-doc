在yasql的容错机制中，对于需要用户及时感知到的错误，提供一套统一的错误消息代码进行抛出，格式如下：

错误码编号（Error Number）：由'YASQL-'+五位数字组成。

错误码消息（Error Message）：错误信息描述，在不同的错误场景下，系统通过变量进行针对该项错误具体信息的消息传递。

## COMMON

### YASQL-00001: ASQLE_ALLOC_MEMORY

**Message**: can't allocate %s bytes for %s

**Action**：分配空间失败，请排查原因或减小分配空间大小。

### YASQL-00002: ASQLE_OPEN_FILE

**Message**: 

can't create the spool file \"%s.%s\"

can't open the file %s error: %d

**Action**：打开或创建文件失败，请排查原因并重新打开文件。

### YASQL-00003: ASQLE_SQLTEXT_TOO_LONG

**Message**: the length of sql text/param name exceed limit

**Action**：长度超过最大限制，请缩短长度并重新执行。

### YASQL-00005: ASQLE_NOT_CONNECTED

**Message**: connection is not established

**Action**：未建立连接，请先连接服务端并重新执行。

### YASQL-00007: ASQLE_CONNECTION_ERROR

**Message**: 以实际输出错误信息为准。

**Action**：连接过程中出现错误，请排查原因并重新连接。

### YASQL-00008: ASQLE_SET_OPTION

**Message**: 以实际输出错误信息为准。

**Action**：设置选项中出现错误，请排查原因并重新设置。

### YASQL-00009: ASQLE_EXEC_SHELL

**Message**: failed to execute shell command

**Action**：执行shell命令失败，请排查原因并重新执行。

### YASQL-00010: ASQLE_SHOW_OPTION

**Message**: unknown SHOW option

**Action**：未知的SHOW选项，请查看SHOW命令支持的参数选项并重新执行。

### YASQL-00011: ASQLE_NOTHING

**Message**: Nothing in SQL buffer to run.

**Action**：SQL缓冲池中没有要运行的内容，请排查原因并重新输入内容。

### YASQL-00012: ASQLE_EXIT

**Message**: EXIT variable \"%s\" was non-numeric

**Action**：EXIT变量不是数字，请重新输入。

### YASQL-00013: ASQLE_PARSE_ERROR

**Message**: 以实际输出错误信息为准。

**Action**：解析过程中出现错误，请排查原因并重新执行命令。

### YASQL-00014: ASQLE_HELP

**Message**: this topic was not found.

**Action**：找不到此主题，请查看HELP命令支持的参数选项并重新执行。

### YASQL-00015: ASQLE_NULL_PASSWD

**Message**: null password given; logon denied

**Action**：给定的密码为空；登录被拒绝，请输入正确的密码并重新登录。

### YASQL-00016: ASQLE_INVALID_LIB

**Message**: the linking library INFRA/CLI is invalid

**Action**：链接库INFRA/CLI无效，请查看环境配置排查原因并重试。

### YASQL-00017: ASQLE_SET_ARG

**Message**: SET command requires an argument.

**Action**：SET命令需要一个参数，请输入参数重新执行。

### YASQL-00018: ASQLE_SPOOL_ARG

**Message**: Illegal SPOOL command

**Action**：非法SPOOL命令，请输入正确的命令重新执行。

### YASQL-00019: ASQLE_SUBVAR_LEN

**Message**: line overflow during variable substitution (>%d byte at line %d)

**Action**：变量替换期间的行溢出，请排查原因或尝试输入更短的行。

### YASQL-00020: ASQLE_VAR_NOT_DECLARED

**Message**: Bind variable \"%s\" not declared.

**Action**：绑定变量“%s”未声明，请先声明绑定变量并重试。

### YASQL-00021: ASQLE_INPUT_LEN

**Message**: 以实际输出错误信息为准。

**Action**：输入行溢出，请排查原因或尝试输入更短的行。

### YASQL-00022: ASQLE_INVALID_PARAM

**Message**: 以实际输出错误信息为准。

**Action**：输入的参数无效，请修改。

### YASQL-00023: ASQLE_WHENEVER_SQLEEROR

**Message**: unknown command

**Action**：输入的命令无法识别，请输入正确的命令。

### YASQL-00024: ASQLE_COL_UNDEFINED

**Message**: * COLUMN not defined

**Action**：COLUMN未定义，请定义COLUMN后重试。

### YASQL-00025: ASQLE_COL_SINGLE_UNDEFINED

**Message**: COLUMN '%s' not defined

**Action**：COLUMN未定义，请定义COLUMN后重试。

### YASQL-00026: ASQLE_COL_OPTION

**Message**: unknown COLUMN option

**Action**：未知的COLUMN选项，请查看COLUMN命令支持的参数选项并重新执行。

### YASQL-00027: ASQLE_COL_FORMAT

**Message**: illegal FORMAT string '%s'

**Action**：无效的FORMAT字符串，请输入符合规范的字符串。

### YASQL-00028: ASQLE_CLEAR_OPTION

**Message**: unknown CLEAR option

**Action**：未知的CLEAR选项，请查看CLEAR命令支持的参数选项并重新执行。

### YASQL-00029: ASQLE_INVALID_INPUT_STRING

**Message**: invalid input string, charset may not be %s

**Action**：无效的输入字符串，字符集可能不匹配，请排查原因并输入正确的字符串。
