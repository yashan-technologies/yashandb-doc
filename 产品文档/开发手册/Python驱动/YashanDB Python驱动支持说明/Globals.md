Python DB API v2.0规范中要求数据库模块都应该定义如下3个变量：

| 名称         | 含义                                   | 取值  |
| ------------ | -------------------------------------- | ----- |
| apilevel     | 表示模块支持的DB API版本               | 2.0   |
| threadsafety | 模块接口支持的线程安全级别             | 2     |
| paramstyle   | 参数标记(parameter marker)的格式化风格 | named |

如上3个变量已在yasdb模块中定义，Python开发人员可通过如下代码查看变量的值：

```python
>>> import yasdb
>>> yasdb.threadsafety
2
>>> yasdb.apilevel
'2.0'
>>> yasdb.paramstyle
'named'
```

