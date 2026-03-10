## 功能简介

yacSetConnAttr函数用于设置连接信息句柄中的某个属性值，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

conn级别的参数会对当前连接信息句柄下的所有句柄的行为产生影响，请通过yacSetConnAttr()和[yacGetConnAttr](yacGetConnAttr)函数进行配置和获取。

| 属性                          | value对应数据类型          | 属性读写权限（R/W） | 说明                                                   |
|-----------------------------|----------------------|-------------|------------------------------------------------------|
| YAC_ATTR_LOGIN_TIMEOUT      | YacUint32            | R/W         | 该值指定当前登录流程的超时时间，保留参数。                                |
| YAC_ATTR_AUTOCOMMIT         | YacBool              | R/W         | 该值指定是否在语句结束时自动提交。                                    |
| YAC_ATTR_PACKET_SIZE        | YacUint32            | R/W         | 该值指定会话交互过程中的数据包大小。                                   |
| YAC_ATTR_TXN_ISOLATION      | YacTxnIsolation      | R/W         | 该值指定会话所执行事务的隔离级别。<br />指定该属性要求当前会话未在事务内或必须是事务的第一条语句。 |
| YAC_ATTR_CREDT              | YacCredtType         | R/W         | 该值指定会话的鉴权类型。                                         |
| YAC_ATTR_TAF_CALLBACK       | YacTafCallbackStruct | R/W         | 该值用于设定TAF的回调函数。                                      |
| YAC_ATTR_HEARTBEAT_ENABLED  | YacBool              | R/W         | 该值用于设定或查询是否使用心跳检测功能。                                 |
| YAC_ATTR_TAF_ENABLED        | YacBool              | R           | 该值返回是否已开启TAF功能。                                      |
| YAC_ATTR_MAX_CHARSET_RATIO  | YacUint32            | R           | 该值返回最大CHARSET膨胀比率。                                   |
| YAC_ATTR_MAX_NCHARSET_RATIO | YacUint32            | R           | 该值返回最大NCHARSET膨胀比率。                                  |

## 函数声明

```c
YacResult yacSetConnAttr(yacHandle hConn,
                         yacConnAttr attr,
                         YacVoid* value,
                         YacInt32 bufLength);
```

## 参数说明

| 参数名         | 说明                         |
| -------------- | ---------------------------- |
| hConn (IN/OUT) | 连接信息句柄。               |
| attr (IN)      | 属性类型。                   |
| value (IN)     | 欲设置的属性值。             |
| bufLength (IN) | 欲设置的属性值的缓冲区长度。 |