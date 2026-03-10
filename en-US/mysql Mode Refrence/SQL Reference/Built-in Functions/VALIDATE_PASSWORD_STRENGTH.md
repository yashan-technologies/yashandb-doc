```ebnf+diagram
validate_password_strength::= VALIDATE_PASSWORD_STRENGTH "(" str ")"
```

The VALIDATE_PASSWORD_STRENGTH function is used to check the password strength of str, parsing the given parameter in byte form.

This function relies on password-related [system variables](../../../All Manuals/Reference Manual of mysql Mode/系统变量/系统变量参考), and its return value may vary based on the values of such variables. Based on the byte length of the password Length, the [password policy](../../../All Manuals/Reference Manual of mysql Mode/系统变量/系统变量参考.html#validate_password_policy) configuration, and whether it matches the password dictionary, the return rules of this function are as follows:

* If Length < 4, return 0.
* If 4 <= Length < [VALIDATE_PASSWORD_LENGTH](../../../All Manuals/Reference Manual of mysql Mode/系统变量/系统变量参考.html#validate_password_length), return 25.
* If it meets the Low policy, return 50.
* If it meets the Medium policy and matches the password dictionary, return 75; if it meets the Medium policy but does not match the password dictionary, return 100.

If the validate_password_check_user_name system variable is enabled, and the password matches the username in either a forward or reverse manner, this function will return 0 regardless of the settings of other validate_password system variables.

**str**

The password whose strength is to be checked, and its value must be of string type or other types that can be converted to string type.

When the value of str is a non-string type, the function will first convert it to string type before strength checking; if the conversion fails, an error will be raised.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT VALIDATE_PASSWORD_STRENGTH('Aa#12345') res FROM DUAL;
res                   
--------------------- 
100
```
