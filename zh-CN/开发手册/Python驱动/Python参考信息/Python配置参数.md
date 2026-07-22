本文档介绍YashanDB Python驱动的配置参数。

**首字母索引**

[A](#apara)	[D](#dpara)	[G](#gpara)	[I](#ipara)	[M](#mpara)	[MIN](#minpara)	[P](#ppara)	[T](#tpara)	[U](#upara)

<span id="apara" name="apara"></span>

### apiLevel

*   参数类型：字符串

*   取值范围/格式：字符串

*   参数说明：DB-API版本。当前固定为"2.0"，表示支持Python DB API v2.0规范。

### arraySize

*   参数类型：数值

*   默认值：100

*   取值范围/格式：正整数

*   参数说明：每次fetchmany()默认返回的行数。用于批量获取结果集时指定每次获取的数量。

<span id="dpara" name="dpara"></span>

### DSN

*   参数类型：字符串

*   默认值：无

*   取值范围/格式：数据源名称，可包含数据库用户名、密码、数据库URL等信息。

*   参数说明：数据源名称。可通过该参数指定host、port、用户名、密码、PDB名称等信息。URL格式如下：
    * 单地址连接：`host:port[/pdb_name]`
    * 多地址连接（primary类型）：`host:port,host:port,host:port[/pdb_name]`或`PRIMARY:host:port,host:port,host:port[/pdb_name]`
    * 多地址连接（loadBalance类型）：`LOADBALANCE:host:port,host:port,host:port[/pdb_name]`

> **Note**：
>
> - `connect()`的`dsn`参数支持两种格式：完整格式`user/password@url`（用户名和密码合并在dsn中）或简化格式`url`（用户名和密码通过独立的`user`和`password`参数指定）。
> - `SessionPool`的`dsn`参数**仅支持**`url`格式（不含`user/password@`前缀），用户名和密码必须通过独立的`user`和`password`参数指定。若将`user/password@url`格式用于SessionPool，会将整串当作主机名解析，导致连接失败。

<span id="gpara" name="gpara"></span>

### getMode

*   参数类型：数值

*   默认值：1

*   取值范围/格式：目前固定为1

*   参数说明：连接获取模式。具体表现为：如果池内存在空闲连接，直接获得连接；如果池内连接耗尽但总连接数不超过max值时，新请求会根据increment值创建新连接；如果池内连接耗尽且总连接数已达到max值时，直接报错。

> **Note**：
>
> `getMode`属性**不可读**，直接访问该属性会返回`AttributeError`（如`pool.getMode`或`pool.getmode`均不存在）。仅`SessionPool`构造时可设置此参数，`connect()`函数不支持此参数。

<span id="ipara" name="ipara"></span>

### increment

*   参数类型：数值

*   默认值：1

*   取值范围/格式：正整数

*   参数说明：单次可创建的连接数。当池内连接耗尽但总连接数不超过max值时，新请求会根据increment值创建新连接。

<span id="mpara" name="mpara"></span>

### MAX

*   参数类型：数值

*   默认值：4

*   取值范围/格式：正整数，取值范围为[min,4294967295]

*   参数说明：连接池的最大连接数。

<span id="minpara" name="minpara"></span>

### MIN

*   参数类型：数值

*   默认值：1

*   取值范围/格式：正整数，取值范围为[0,4294967295]

*   参数说明：连接池的最小连接数。

<span id="ppara" name="ppara"></span>

### PASSWORD

*   参数类型：字符串

*   默认值：无

*   取值范围/格式：字符串

*   参数说明：数据库用户密码。可选参数，若dsn中未指定则必选。

### paramStyle

*   参数类型：字符串

*   取值范围/格式：字符串

*   参数说明：参数风格。当前固定为`named`，表示使用命名参数风格，SQL中用`:name`或`:name=:value`形式绑定参数。**同时支持`?`位置绑定语法**，使用`?`作为占位符按顺序绑定参数。

<span id="tpara" name="tpara"></span>

### threadSafety

*   参数类型：数值

*   取值范围/格式：整数

*   参数说明：模块支持的线程安全级别。当前值为2，表示模块和连接可以跨线程共享，但游标不能跨线程共享。

<span id="upara" name="upara"></span>

### USER

*   参数类型：字符串

*   默认值：无

*   取值范围/格式：字符串

*   参数说明：数据库用户名。可选参数，若dsn中未指定则必选。
