```ebnf+diagram
label_string::= LABEL_TO_CHAR  "(" label ")" 
```

LABEL_TO_CHAR函数返回LBAC标签值为label的标签内容。

本函数仅适用于单机部署中的HEAP表。

使用本函数可能需要调用[YLS_ENFORCEMENT](../../PL参考手册/内置高级包/YLS_ENFORCEMENT)、[SA_SYSDBA](../../PL参考手册/内置高级包/SA_SYSDBA)、[SA_COMPONENTS](../../PL参考手册/内置高级包/SA_COMPONENTS)、[SA_LABEL_ADMIN](../../PL参考手册/内置高级包/SA_LABEL_ADMIN)等内置高级包，相关权限要求和使用约束还会受限于相应的内置高级包。

**label**

LBAC标签值，可查询[DBA_SA_LABELS](../../../参考手册/系统视图/DBA视图/DBA_SA_LABELS)视图获取。参数值为BIGINT类型，参数为BINARY_FLOAT，BINARY_DOUBLE类型时按四舍五入取整，为其它类型时向下取整。

示例（单机HEAP表）

```sql
-- 打开LBAC
CALL YLS_ENFORCEMENT.ENABLE_YLS;
-- 创建LBAC安全策略
CALL SA_SYSDBA.CREATE_POLICY('lbac_policy', 'policy_column');
-- 创建LBAC级别
CALL SA_COMPONENTS.CREATE_LEVEL( 'lbac_policy', 10, 'GENERAL', 'GENERAL');
CALL SA_COMPONENTS.CREATE_LEVEL ( 'lbac_policy', 20, 'SENS', 'SENS');
CALL SA_COMPONENTS.CREATE_LEVEL ( 'lbac_policy', 30, 'SECRET', 'SECRET');
-- 创建LBAC区间
CALL SA_COMPONENTS.CREATE_COMPARTMENT ( 'lbac_policy', 10, 'MNG', 'MNG');
CALL SA_COMPONENTS.CREATE_COMPARTMENT ( 'lbac_policy', 20, 'QA', 'QA');
CALL SA_COMPONENTS.CREATE_COMPARTMENT ( 'lbac_policy', 30, 'RD', 'RD');
CALL SA_COMPONENTS.CREATE_COMPARTMENT ( 'lbac_policy', 40, 'TEST', 'TEST');
-- 创建标签
CALL SA_LABEL_ADMIN.CREATE_LABEL ('lbac_policy', 1001, 'GENERAL:MNG');

SELECT LABEL_TO_CHAR(1001) label_string FROM DUAL;
LABEL_STRING                                                     
---------------------------------------------------------------- 
GENERAL:MNG

-- 删除策略
CALL SA_SYSDBA.DROP_POLICY('lbac_policy', TRUE);
-- 关闭开关
CALL YLS_ENFORCEMENT.DISABLE_YLS;
```