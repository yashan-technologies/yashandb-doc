通用描述
----

CREATE PROFILE用于创建一个profile，profile为系统对用户的一组资源限制的集合。在YashanDB中，profile可用于定义[密码使用策略](../../../产品安全/身份标识与鉴别/密码认证/密码策略.html#password_policy)相关、[会话资源](../../../产品安全/连接管理/管控会话资源)相关以及[连接请求IP](../../../产品安全/连接管理/管理IP黑白名单)相关限制。

执行本语句需注意如下事项：

- 用户必须拥有CREATE PROFILE权限。
- profile本身并不包含用户信息，需使用[CREATE USER](./CREATE USER)或[ALTER USER](./ALTER USER)语句关联user与profile。

通过DBA_PROFILES视图可查询所有profile及其内容。

YashanDB已预置一个默认的profile（名称为DEFAULT），在使用缺省语法创建新用户时，该用户都将被关联到默认的profile，受其指定的配置限制。

存算一体分布式集群部署中，只支持[定义密码策略相关的资源限制](#pswd)。

> **Note**: 
>
> - 参数值支持数据类型隐式转换和表达式运算，结果满足规则即可。
> - 所有参数的值为空字符串或表达式结果为NULL时，视为0值处理。

语句定义
----

**create profile::=**

```ebnf+diagram
syntax::= CREATE PROFILE profile_name LIMIT (password_parameters | resource_parameters | tcp_ip_parameters) {" " (password_parameters | resource_parameters | tcp_ip_parameters)}
```

**[password_parameters](#pswd)::=**

```ebnf+diagram
syntax::= (password_parameter_name password_parameter_value)
  {" " (password_parameter_name password_parameter_value)}
```

**[resource_parameters](#res)::=**

```ebnf+diagram
syntax::= (resource_parameter_name resource_parameter_value)
  {" " (resource_parameter_name resource_parameter_value)}
```

**[tcp_ip_parameters](#ip)::=**

```ebnf+diagram
syntax::= (tcp_ip_parameter_name tcp_ip_parameter_value)
  {" " (tcp_ip_parameter_name tcp_ip_parameter_value)}
```

### 1. profile\_name

profile名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

<span id="pswd" name="pswd" class="yaslink"></span>

### 2. password\_parameters

定义[密码使用策略](../../../产品安全/身份标识与鉴别/密码认证/密码策略.html#password_policy)相关的配置，包括密码策略参数和参数值，同时定义多个参数以空格分隔。

当某个密码策略参数未在本语句中指定时，系统按DEFAULT值（见下文DEFAULT描述）将其生成到profile中。

#### 2.1. password\_parameter\_name

密码使用策略相关参数名，同时定义多个参数以空格分隔。

#### 2.2. password\_parameter\_value

指定密码策略参数所对应的参数值值。

所有的参数均可设置为DEFAULT、UNLIMITED或具体参数值。

**DEFAULT**

指定为DEFAULT表示直接采用预置的默认profile中相关配置。

**UNLIMITED**

指定为UNLIMITED表示不对该参数进行限制。

**password_parameters参数值**

参数值支持数学运算，结果满足参数取值规则即可。

相关参数如下表所示：

::: tabs
== 未开启三权分立

|  参数| 对应策略| 取值范围| 默认值 |
| --------------------- | ----------------------------|-------------------------|------- |
| FAILED_LOGIN_ATTEMPTS | 最大连续错误登录次数                             | (0,2147483647) 区间的整数 |  10  |
| FAILED_LOGIN_INTERVAL | 密码重试间隔，即密码错误时再次尝试需要等待的时间间隔（单位：秒） | UNLIMITED或[1,2147483646]区间的整数 | UNLIMITED<br>即不限制等待间隔   |
| PASSWORD_LOCK_TIME    | 超过错误尝试次数后用户被锁定的天数         | (0,24855] | 1    |
| PASSWORD_LIFE_TIME    | 密码生命周期，即需要更新密码的天数         | UNLIMITED或(0,24855]  | UNLIMITED，即不限制天数                      |
| PASSWORD_GRACE_TIME   | 密码过期宽限期，即密码过期后还可使用原密码的天数       | UNLIMITED或[0,24855]   | UNLIMITED，即无限宽限 |
| PASSWORD_REUSE_TIME   | 密码不可复用的间隔天数<br>假设其为X，表示新密码不能设置为最近X天内使用过的密码     | UNLIMITED或(0,24855]       | UNLIMITED，即无限复用   |
| PASSWORD_REUSE_MAX    | 密码满足可复用的必要变更次数<br>假设其为N，表示新密码不能设置为最近N次使用过的密码 |  UNLIMITED或[1,2147483646]区间的整数 | UNLIMITED，即无限复用   |

== 已开启三权分立

|  参数| 对应策略| 取值范围| 默认值 |
| --------------------- | ----------------------------|-------------------------|------- |
| FAILED_LOGIN_ATTEMPTS | 最大连续错误登录次数                             | (0,5]区间的整数 |  5  |
| FAILED_LOGIN_INTERVAL | 密码重试间隔，即密码错误时再次尝试需要等待的时间间隔（单位：秒） | UNLIMITED或[1,2147483646]区间的整数 | UNLIMITED<br>即不限制等待间隔   |
| PASSWORD_LOCK_TIME    | 超过错误尝试次数后用户被锁定的天数         | (0,24855] | 1    |
| PASSWORD_LIFE_TIME    | 密码生命周期，即需要更新密码的天数         | (0,7]  | 7 |
| PASSWORD_GRACE_TIME   | 密码过期宽限期，即密码过期后还可使用原密码的天数       | 0   | 0 |
| PASSWORD_REUSE_TIME   | 密码不可复用的间隔天数<br>假设其为X，表示新密码不能设置为最近X天内使用过的密码     | UNLIMITED或(0,24855]       | UNLIMITED，即无限复用   |
| PASSWORD_REUSE_MAX    | 密码满足可复用的必要变更次数<br>假设其为N，表示新密码不能设置为最近N次使用过的密码 |  UNLIMITED或[1,2147483646]区间的整数 | UNLIMITED，即无限复用   |

> **Note**:
>
> 开启三权分立后，此前已创建的自定义PROFILE不受影响，但默认PROFILE的部分值会自动修改为符合三权分立规范的值，后续再关闭三权分立时默认PROFILE的值不会再重置。

:::

- FAILED_LOGIN_ATTEMPTS、FAILED_LOGIN_INTERVAL与PASSWORD_LOCK_TIME配合使用，可达到“密码错误时等待X秒后可重试，连续重试Y次后用户被锁定Z天无法登录（除非手动[解锁用户](../../../产品安全/身份标识与鉴别/管理用户.html#alteruser)）”的效果。

- PASSWORD_REUSE_TIME与PASSWORD_REUSE_MAX配合使用，可达到“新密码不能为最近N天内且最近M次使用过的相同密码”的效果。若其中一个参数设置为具体数值而另一个参数设置为UNLIMITED，则用户永远无法使用重复密码。

示例

```sql
CREATE PROFILE prof_pswd1 LIMIT FAILED_LOGIN_ATTEMPTS 5 PASSWORD_LIFE_TIME 180;

CREATE PROFILE prof_pswd2 LIMIT 
FAILED_LOGIN_ATTEMPTS 15 
FAILED_LOGIN_INTERVAL 10
PASSWORD_LOCK_TIME 1 
PASSWORD_LIFE_TIME 30*2.5 
PASSWORD_GRACE_TIME 0
PASSWORD_REUSE_TIME UNLIMITED 
PASSWORD_REUSE_MAX UNLIMITED;
```

<span id="res" name="res" class="yaslink"></span>

### 3. resource\_parameters

定义[会话资源](../../../产品安全/连接管理/管控会话资源)相关的配置，同时定义多个参数以空格分隔。

当某个资源策略参数未在本语句中指定时，系统按DEFAULT值（见下文DEFAULT描述）将其生成到profile中。

#### 3.1. resource\_parameter\_name

目前包括SESSIONS_PER_USER、IDLE_TIME。

**SESSIONS_PER_USER**

同一用户最多并行会话连接数。

**IDLE_TIME**

用户最长空闲无操作时间，单位为分钟。用户登录后若连续长时间无操作达到阈值时长，系统将断开该会话，会话被断开后再执行操作将报错。

后台线程每隔10s进行一次检测并断开长时间空闲会话，因此用户最长连续空闲的时间至多可比该用户的对应阈值多10s。

#### 3.2. resource\_parameter\_value

指定会话资源参数所对应的配置值。

所有的参数均可设置为DEFAULT、UNLIMITED或具体参数值。

**DEFAULT**

指定为DEFAULT表示直接采用预置的默认profile中相关配置。

**UNLIMITED**

指定为UNLIMITED表示不对该参数对应资源进行限制。

**resource_parameters参数值**

参数值支持表达式运算，结果满足参数值规则即可。

|  策略参数| 参数说明| 参数值规则|
| --------------------- | ------------------------- | ----------------------------------- |
| SESSIONS_PER_USER     | 同一用户最多并行会话连接数 | [1,2147483646]区间的整数                 |
| IDLE_TIME             | 用户最长空闲无操作时间（单位：分钟）| [1,2147483646]区间的整数             |

示例（单机/共享集群/分布式集群部署）

```sql
CREATE PROFILE prof_res LIMIT SESSIONS_PER_USER 10 IDLE_TIME 10;
```

<span id="ip" name="ip" class="yaslink"></span>

### 4. tcp\_ip\_parameters

定义[连接请求IP](../../../产品安全/连接管理/管理IP黑白名单)相关的配置，同时定义多个参数以空格分隔。

当某个连接请求IP配置参数未在本语句中指定时，系统按DEFAULT值（见下文DEFAULT描述）将其生成到profile中。

#### 4.1. tcp\_ip\_parameters\_name

目前包括登录IP白名单INVITED_NODES、登录IP黑名单EXCLUDED_NODES。

黑名单优先级高于白名单，若黑白名单中存在相同IP按黑名单生效。

**INVITED_NODES**

登录IP白名单，配置IP列表后将只允许当前profile关联的用户使用白名单中的IP登录。

非重复的IP个数不得超过64个。

**EXCLUDED_NODES**

登录IP黑名单，配置IP列表后将不允许当前profile关联的用户使用黑名单中的IP登录。

非重复的IP个数不得超过64个。

#### 4.2. tcp\_ip\_parameters\_value

指定连接请求IP参数所对应的参数值。

所有的参数均可设置为DEFAULT、UNLIMITED或具体的IP列表。

**DEFAULT**

指定为DEFAULT表示不受限制，效果等同于UNLIMITED。

**UNLIMITED**

指定为UNLIMITED表示不对该参数进行限制。

**iplist**

IP列表字符串，格式为`'IP/mask,IP'`。

- IP：实际IP地址，例如192.168.4.11。
- MASK：表示从左向右匹配二进制IP的比特数，取值范围为[0,32]。如果不加MASK，则默认表示全匹配。

示例（单机/共享集群/分布式集群部署）

```sql
CREATE PROFILE prof_ip LIMIT EXCLUDED_NODES '192.168.4.11';
```
