```ebnf+diagram
validate_password_strength::= VALIDATE_PASSWORD_STRENGTH "(" str ")"
```

VALIDATE_PASSWORD_STRENGTH函数用于检测str的密码强度，以字节形式对给定参数进行解析。

本函数依赖密码相关的[系统变量](../../系统变量/系统变量参考)，函数返回值会因此类变量的取值变化而有所差异。基于密码的字节长度Length、[密码策略](../../系统变量/系统变量参考.html#validate_password_policy)配置以及是否匹配密码字典等因素，本函数的返回规则如下：

* Length < 4，返回0。
* 4 <= Length < [VALIDATE_PASSWORD_LENGTH](../../系统变量/系统变量参考.html#validate_password_length)，返回25。
* 满足Low策略，返回50。
* 满足Medium策略且匹配到密码字典时返回75，满足Medium策略但未匹配到密码字典时返回100。

如果启用了validate_password_check_user_name系统变量，且密码与用户名正向或反向匹配，则无论其他validate_password系统变量如何设置，本函数都会返回0。

**str**

待检测强度的密码，其值需为字符串类型或可转为字符串类型的其他类型。

当str的值为非字符串类型时，函数会先将其转为字符串类型再进行强度判断，若转换失败则报错。

示例（HEAP表）

```sql
SELECT VALIDATE_PASSWORD_STRENGTH('Aa#12345') res FROM DUAL;
res                   
--------------------- 
100
```
