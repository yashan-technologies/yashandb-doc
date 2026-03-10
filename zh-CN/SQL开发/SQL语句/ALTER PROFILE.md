通用描述
----

ALTER PROFILE用于修改一个profile的内容。

执行本语句需注意如下事项：

- 用户必须拥有ALTER PROFILE权限才能修改一个profile。
- 对于已与用户关联的profile，修改profile后，其对用户的资源限制立即生效。

对于YashanDB默认的profile（名称为DEFAULT），也可使用本语句对其内容进行修改。

语句定义
----

**alter profile::=**

```ebnf+diagram
syntax::= ALTER PROFILE profile_name LIMIT  (password_parameters | resource_parameters | tcp_ip_parameters) {" " (password_parameters | resource_parameters | tcp_ip_parameters)}
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

已存在的一个profile的名称。

<span id="pswd" name="pswd" class="yaslink"></span>

### 2. password\_parameters

定义密码使用策略相关的配置，同时修改多个参数以空格分隔。

语法及规则同CREATE PROFILE中的[password_parameters](CREATE PROFILE.html#pswd)。

<span id="res" name="res" class="yaslink"></span>

### 3. resource\_parameters

定义会话资源相关的配置，同时修改多个参数以空格分隔。

语法及规则同CREATE PROFILE中的[resource_parameters](CREATE PROFILE.html#res)。

<span id="ip" name="ip" class="yaslink"></span>

### 4. tcp\_ip\_parameters

定义连接请求IP相关的配置，同时修改多个参数以空格分隔。

语法及规则同CREATE PROFILE中的[tcp_ip_parameters](CREATE PROFILE.html#ip)。

示例

```sql
ALTER PROFILE prof_pswd1 LIMIT FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LIFE_TIME 200;
```

示例（单机/共享集群/分布式集群部署）

```sql
ALTER PROFILE prof_res LIMIT SESSIONS_PER_USER 10 IDLE_TIME UNLIMITED;

ALTER PROFILE prof_ip LIMIT EXCLUDED_NODES '192.168.4.11,192.168.4.12';
```
