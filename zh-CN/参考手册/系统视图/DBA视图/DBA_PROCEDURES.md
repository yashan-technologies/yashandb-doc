PL视图，显示过程体相关信息。

|  字段| 类型| 说明|
| --- |-------------| --- |
| OWNER | VARCHAR(64) | 对象所属的用户 |
| OBJECT\_NAME | VARCHAR(64) | 对象名称 |
| PROCEDURE\_NAME | VARCHAR(68) | 对象下属过程体名称 |
| OBJECT\_ID | BIGINT      | 对象ID |
| SUBPROGRAM\_ID | INTEGER     | 对象在所属高级包中的ID |
| OBJECT\_TYPE | VARCHAR(17) | 对象类型 |
| AGGREGATE | VARCHAR(1)     | 对象是否为聚合函数 |
| PIPELINED | VARCHAR(1)  | 对象是否为管道函数 |
| IMPLTYPEOWNER | VARCHAR(1)  | 实现类型所属用户<sup>*</sup> |
| IMPLTYPENAME | VARCHAR(1)  | 实现类型名称<sup>*</sup> |
| PARALLEL | CHAR(1)     | 是否可并行执行<sup>*</sup> |
| INTERFACE | CHAR(1)     | 是否为使用接口实现的函数<sup>*</sup> |
| DETERMINISTIC | VARCHAR(1)  | 是否为确定性函数 |
| AUTHID | VARCHAR(12) | 函数权限检查模式 |
| RESULT\_CACHE | CHAR(1)     | 是否为结果缓存函数<sup>*</sup> |
| ORIGIN\_CON\_ID | VARCHAR(1)  | 容器原始行号<sup>*</sup> |
| POLYMORPHIC | VARCHAR(1)  | 是否为多态表函数<sup>*</sup> |

描述中带\*号的列为预留列，内部值恒为空或N。
