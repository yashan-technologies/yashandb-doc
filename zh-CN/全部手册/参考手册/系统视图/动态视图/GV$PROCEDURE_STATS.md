本视图显示所有PL Pool中自定义对象占用的内存汇总信息。

|  字段| 类型| 说明|
|-------|-----------|------|
| GROUP_ID | NUMBER    | 组ID   |
| GROUP_NODE_ID | NUMBER    | 组内节点ID |
| INST_ID | NUMBER    | 实例ID |
| OID      | BIGINT    | 对象标识       |
| VERSION  | INTEGER   | 对象在内存中的版本号 |
| BLOCKS   | SMALLINT  | 占用内存BLOCK数|
| REF_COUNT| INTEGER   | 内部引用次数   |
| TOUCHS   | INTEGER   | 访问使用次数   |
| STATUS   | TINYINT   | 对象状态 <br/>* 0：NO_READY状态, <br/>* 1：READY状态|
| TYPE     | TINYINT   | 对象类型 <br/>* 0：Function,包含作为Object type body方法 <br/>* 1：Procedure <br/>* 2：Package head <br/>* 3：Trigger <br/>* 5：Package body |
