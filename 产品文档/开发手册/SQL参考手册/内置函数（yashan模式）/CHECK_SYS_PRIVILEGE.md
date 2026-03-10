```ebnf+diagram
check_sys_privilege::= CHECK_SYS_PRIVILEGE "(" user_id "," object_type_id ")"
```

CHECK_SYS_PRIVILEGE函数检测登陆用户是否拥有对某用户下某种类型对象的可访问类系统级权限，返回值为BOOLEAN类型。

本函数不支持向量化计算。

当检测表类型对象的权限时，如用户拥有SELECT ANY TABLE、INSERT ANY TABLE、UPDATE ANY TABLE、DELETE ANY TABLE中任一项权限时，函数返回TURE，否则返回FALSE。

**user_id**

对象所属OWNER用户ID，必须是整数类型的数据，使用[USERENV](./USERENV)函数或查询[DBA_USERS](../../../参考手册/系统视图/DBA视图/DBA_USERS)视图获取。

**object_type_id**

对象的类型ID，必须是整数类型的数据，可参考[DBA_OBJECTS](../../../参考手册/系统视图/DBA视图/DBA_OBJECTS)视图资料中OBJECT_TYPE字段描述，例如表的类型ID为1。



示例

```sql
SELECT CHECK_SYS_PRIVILEGE(0,1) pri FROM DUAL;
PRI
--------------------
true
```

