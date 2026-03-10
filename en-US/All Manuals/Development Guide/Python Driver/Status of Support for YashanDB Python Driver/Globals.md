The Python DB API v2.0 specification requires that database modules define the following three variables:

|Name |Meaning |Value |
| ------------ | -------------------------------------- | ----- |
| apilevel    | Indicates the DB API version supported by the module | 2.0    |
| threadsafety | The level of thread safety supported by the module interface | 2      |
| paramstyle  | The formatting style of parameter markers | named  |

The above three variables have been defined in the yasdb module. Python developers can check the values of the variables using the following code:

```python
>>> import yasdb
>>> yasdb.threadsafety
2
>>> yasdb.apilevel
'2.0'
>>> yasdb.paramstyle
'named'
```
