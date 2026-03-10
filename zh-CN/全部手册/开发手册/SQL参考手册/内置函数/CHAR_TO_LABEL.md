```ebnf+diagram
char_to_label::= CHAR_TO_LABEL  "(" policy_name "," label_string ")" 
```

CHAR_TO_LABEL函数返回LBAC安全策略policy_name下标签内容为label_string的标签值。label_string内容必须满足标签内容要求。

若标签内容已存在则直接返回对应的标签值；若标签内容不存在会先自动创建标签，再返回对应的标签值。

本函数仅适用于单机部署中的HEAP表。

使用本函数可能需要调用[YLS_ENFORCEMENT](../../PL参考手册/内置高级包/YLS_ENFORCEMENT)、[SA_SYSDBA](../../PL参考手册/内置高级包/SA_SYSDBA)、[SA_COMPONENTS](../../PL参考手册/内置高级包/SA_COMPONENTS)、[SA_LABEL_ADMIN](../../PL参考手册/内置高级包/SA_LABEL_ADMIN)等内置高级包，相关权限要求和使用约束还会受限于相应的内置高级包。

**policy_name**

LBAC安全策略名称，策略名称大小写不敏感，可查询[DBA_SA_POLICIES](../../../参考手册/系统视图/DBA视图/DBA_SA_POLICIES)视图获取。

**label_string**

标签内容，必须符合标签格式要求且对应的组件均存在，标签内容大小写不敏感。
- 可参考[SA_LABEL_ADMIN](../../PL参考手册/内置高级包/SA_LABEL_ADMIN)查看标签格式要求。
- 可参考[DBA_SA_LEVELS](../../../参考手册/系统视图/DBA视图/DBA_SA_LEVELS)查看该策略下的级别名。
- 可参考[DBA_SA_COMPARTMENTS](../../../参考手册/系统视图/DBA视图/DBA_SA_COMPARTMENTS)查看该策略下的范围名。
- 可参考[DBA_SA_LABELS](../../../参考手册/系统视图/DBA视图/DBA_SA_LABELS)查看该策略下的已存在的标签。

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

SELECT CHAR_TO_LABEL('lbac_policy', 'general:mng') label_tag FROM DUAL;
            LABEL_TAG 
--------------------- 
                 1001

-- 删除策略
CALL SA_SYSDBA.DROP_POLICY('lbac_policy', TRUE);
-- 关闭LBAC
CALL YLS_ENFORCEMENT.DISABLE_YLS;
```
