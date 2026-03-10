密码策略用于在密码认证的过程中，由系统根据策略制定的规则，对用户的密码行为和属性进行判断并作出相应的动作，包括：

- 在设置/修改密码时，应校验密码复杂度（密码强度），使之满足一定的要求。
- 为防止恶意攻击者反复登录尝试猜出密码，当用户因为密码错误连续登录失败达到阈值时，将被锁定一定时间。 
- 用户长时间使用同一密码会影响数据库安全，因此系统对于密码的生命长度进行限制。
- 系统在用户登录过程中发现密码已使用超过限制天数时，将进行额外宽限期处理并打印告警日志提示。宽限期过后，该密码完全过期，客户端提示用户输入新密码，用户可以立即更新密码。
- 为防止用户长时间使用相同密码，用户在设置新密码时还需要满足密码复用限制，不允许复用一定时间内使用过的密码以及最近变更次数内使用过的密码。

<span id="password_complexity" name="password_complexity" class="yaslink"></span>

## 密码强度控制

通过yaspwd或yasboot工具设置或修改密码时，YashanDB默认启用密码强度控制，密码需满足如下要求：

- 密码长度为8 - 64位。
- 密码必须同时包含数字、字母和特殊字符。
- 密码中不能包含对应的用户名称。

通过SQL语句设置或修改密码时，需先开启密码强度控制功能（由隐藏参数_CHECK_PASSWORD_COMPLEXITY控制，请联系我们的技术支持处理）才会在密码设置时进行强度校验，密码需满足如下要求：

- 密码长度默认要求为8 - 64位（可通过隐藏参数_PASSWORD_MIN_LENGTH调整密码长度最低值）。
- 密码必须同时包含数字、大写字母、小写字母和特殊字符。

<span id="password_policy" name="password_policy" class="yaslink"></span>

## 密码使用策略

### 策略简介

用户配置文件PROFILE是密码使用策略的载体，通过配置一系列参数定义密码使用策略的具体要求，将PROFILE与目标用户进行关联即可应用相应的密码策略。如需调整密码使用策略，只需更新PROFILE即可立即对所有关联用户生效。

相关参数如下表所示：

|  参数| 对应策略| 默认值|
| --------------------- | ------------------------------------------------------------ | ------------------- |
| FAILED_LOGIN_ATTEMPTS | 帐户被锁定之前可以错误尝试的次数                             | 10                |
| PASSWORD_LOCK_TIME    | 超过错误尝试次数后用户被锁定的天数                           | 1                 |
| PASSWORD_LIFE_TIME    | 密码可以被使用的天数（生命长度）                             | UNLIMITED<br>即不限制天数 |
| PASSWORD_GRACE_TIME   | 密码过期之后还可宽限多少天使用原密码（额外宽限期）           | UNLIMITED<br>即无限宽限   |
| PASSWORD_REUSE_TIME   | 密码不可复用的天数<br>假设其为X，表示新密码不能设置为最近X天内使用过的密码                       | UNLIMITED无限复用   |
| PASSWORD_REUSE_MAX    | 密码满足可复用的必要变更次数<br>假设其为N，表示新密码不能设置为最近N次使用过的密码 | UNLIMITED<br>即无限复用   |
| FAILED_LOGIN_INTERVAL | 密码错误，下一次尝试登录需要等待的间隔 | UNLIMITED<br>即不限制等待间隔   |

PASSWORD_REUSE_TIME与PASSWORD_REUSE_MAX参数需关联设置，具体表现如下：

- 如果为两个参数都指定了具体数值，则需同时满足PASSWORD_REUSE_TIME指定天数以及密码更改了PASSWORD_REUSE_MAX指定的次数后，用户才能使用重复密码。
- 如果只为其中一个参数指定了具体数值、另一个参数保持默认的UNLIMITED，则用户永远无法使用重复密码。  

YashanDB预置了一个默认的PROFILE（名称为DEFAULT），密码使用策略相关参数均为默认值。所有新创建的用户在未指定PROFILE时默认关联到默认的PROFILE，受默认的密码策略管控。

DBA_PROFILES视图将呈现当前数据库中的所有PROFILE的信息。

### 创建密码使用策略

#### 前提条件

执行PROFILE创建操作的用户，需具备CREATE PROFILE权限，授权操作请查阅[权限授予与收回](../../数据访问控制/特权与角色管理（yashan模式）/权限授予与收回)。

#### 操作步骤

1. 使用具备相应权限的用户登录YashanDB。

2. 执行[CREATE PROFILE](../../../开发手册/SQL参考手册/SQL语句（yashan模式）/CREATE PROFILE)语句创建PROFILE。

    ```sql
    CREATE PROFILE prof_pswd LIMIT FAILED_LOGIN_ATTEMPTS 5 PASSWORD_LIFE_TIME 180;
    ```

### 应用密码使用策略

在[创建用户](../用户/管理用户（yashan模式）)或[管理用户的属性信息](../用户/管理用户（yashan模式）)时，可以为用户关联已有PROFILE。
```sql
-- 创建用户并为其关联指定profile
CREATE USER sales2 IDENTIFIED BY PASSWORD PROFILE prof_pswd;

-- 修改用户的profile，需指定为已存在的profile 
ALTER USER sales1 PROFILE prof_pswd;
```

### 修改密码使用策略

#### 前提条件

执行PROFILE修改操作的用户，需具备ALTER PROFILE权限，授权操作请查阅[权限授予与收回](../../数据访问控制/特权与角色管理（yashan模式）/权限授予与收回)。

#### 操作步骤

1. 使用具备相应权限的用户登录YashanDB。

2. 执行[ALTER PROFILE](../../../开发手册/SQL参考手册/SQL语句（yashan模式）/ALTER PROFILE)语句修改PROFILE。

    ```sql
    ALTER PROFILE prof_pswd LIMIT FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LIFE_TIME 200;
    ```

    修改后，新的密码策略配置值立即对所有关联用户生效。

### 删除密码使用策略

若无需再使用某个自定义创建的PROFILE，可将其删除，YashanDB内置的默认PROFILE（名称为DEFAULT）则无法删除。

对于已与用户关联的PROFILE，必须使用CASCADE关键字才能删除，对应用户将会自动关联默认PROFILE。

#### 前提条件

执行PROFILE删除操作的用户，需具备DROP PROFILE权限，授权操作请查阅[权限授予与收回](../../数据访问控制/特权与角色管理（yashan模式）/权限授予与收回)。

#### 操作步骤

1. 使用具备相应权限的用户登录YashanDB。

2. 执行[DROP PROFILE](../../../开发手册/SQL参考手册/SQL语句（yashan模式）/DROP PROFILE)语句删除不必要的PROFILE。

    ```sql
    DROP PROFILE prof_pswd CASCADE;
    ```
