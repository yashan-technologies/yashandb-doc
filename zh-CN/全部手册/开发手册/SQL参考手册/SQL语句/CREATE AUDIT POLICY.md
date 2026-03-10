通用描述
----

CREATE AUDIT POLICY用于创建一个审计策略，通过指定不同的审计项对数据库操作进行审计。

只有拥有AUDIT_ADMIN审计管理员角色，或者拥有AUDIT SYSTEM系统权限的用户，才能创建一个审计策略。

一个成功创建的审计策略，还必须满足如下两个前提条件才会触发系统开始审计操作：

- 配置参数UNIFIED_AUDITING值为true，即审计开关为打开状态。
- 通过[AUDIT POLICY](./AUDIT POLICY)语句将审计策略使能。

审计策略被成功创建后，可通过AUDIT_UNIFIED_POLICIES视图查看其定义信息。

YashanDB对用户提供如下方面的审计管理：

- [系统权限审计](#privilegeauditclause)
- [系统行为和对象行为的操作审计](#actionauditclause)
- [角色审计](#roleauditclause)

对于审计管理更具体的描述请查阅[安全审计](../../../产品安全/安全审计/00安全审计)。

语句定义
----

**create audit policy::=**

```ebnf+diagram
syntax::= CREATE AUDIT POLICY policy_name [privilege_audit_clause] [action_audit_clause] [role_audit_clause] [when_clause] [toplevel_clause]
```

**以下clause参数至少选择一个。**

**[privilege\_audit\_clause](#privilegeauditclause)::=**

```ebnf+diagram
syntax::= PRIVILEGES (system_privileges_clause) {"," (system_privileges_clause)}
```

**[action\_audit\_clause](#actionauditclause)::=**

```ebnf+diagram
syntax::= ACTIONS (system_action_clause|object_action_clause) {"," (system_action_clause|object_action_clause)}
```

**[system_action_clause](#systemactionclause)::=**

```ebnf+diagram
syntax::= system_action|ALL
```

**[object_action_clause](#objectactionclause)::=**

```ebnf+diagram
syntax::= (object_action|ALL) ON [schema "."] object_name
```

**[role\_audit\_clause](#roleauditclause)::=**

```ebnf+diagram
syntax::= ROLES (role_name) {"," (role_name)}
```

**[when\_clause](#whenclause)::=**

```ebnf+diagram
syntax::= WHEN "'" audit_condition "'" EVALUATE PER (STATEMENT|SESSION|INSTANCE)
```

**[toplevel\_clause](#toplevelclause)::=**

```ebnf+diagram
syntax::= ONLY TOPLEVEL
```

### 1. policy\_name

审计策略的名称，审计策略名称不可重复，不可省略，不可带模式名，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

<span id="privilegeauditclause" name="privilegeauditclause" class="yaslink"></span>

### 2. privilege\_audit\_clause

该语句用于定义审计策略包含的系统权限的审计项，可同时定义多个系统权限审计项，用`,`分隔。

**system_privileges_clause**

指定具体的系统权限名称，具体权限项请查阅[系统特权](../../../产品安全/数据访问控制/特权与角色管理/系统特权)中查看。

对指定的系统权限启用审计策略后，所有使用到该权限的操作将被审计。

示例

```sql
-- 创建审计策略，定义对涉及select any table, delete any table权限的语句进行审计
CREATE AUDIT POLICY up1
PRIVILEGES SELECT ANY TABLE, DELETE ANY TABLE;
```

<span id="actionauditclause" name="actionauditclause" class="yaslink"></span>

### 3. action\_audit\_clause

该语句用于定义审计策略包含的系统行为或对象行为审计项，可同时定义多个行为审计项，用`,`分隔，其中审计项可分为如下两种类型：

- 全局的审计项。
- 针对具体对象的审计项。

<span id="systemactionclause" name="systemactionclause" class="yaslink"></span>

#### 3.1. system\_action\_clause

指定全局审计项。

##### 3.1.1. system\_action

全局审计项目，具体项目可以通过AUDITABLE_SYSTEM_ACTIONS视图查看。

##### 3.1.2. ALL

当全局审计项指定为ALL时，表示在AUDITABLE_SYSTEM_ACTIONS中列出的所有项目都将纳入审计策略。

<span id="objectactionclause" name="objectactionclause" class="yaslink"></span>

#### 3.2. object\_action\_clause

指定针对具体对象的审计项。

##### 3.2.1. object\_action

针对对象的审计项目，具体项目可以通过AUDITABLE_OBJECT_ACTIONS视图查看。

##### 3.2.2. schema

模式名称，可省略，则使用当前登录用户的模式。

##### 3.2.3. object\_name

对象名称，YashanDB支持对表、视图等所有对象进行操作审计。

示例

```sql
-- 创建审计策略，定义针对drop table类型语句、对表area的delete、insert、update操作、在branches所有操作进行审计
CREATE AUDIT POLICY up2
  ACTIONS DROP TABLE,
    DELETE ON sales.area,
    INSERT ON sales.area,
    UPDATE ON sales.area,
    ALL ON sales.branches;
```

<span id="roleauditclause" name="roleauditclause" class="yaslink"></span>

### 4. role\_audit\_clause

该语句用于定义审计策略包含的角色的审计项，可同时定义多个角色审计项，用`,`分隔。

**role_name**

角色名称，只能为NORMAL ROLE，具体项目可在DBA_ROLES视图中查看。

对指定的角色启用审计策略后，被直接赋予到该角色的所有系统权限都将会被审计，即所有使用到这些权限的操作都将被审计。

示例

```sql
-- 创建角色
CREATE ROLE role_a;
-- 给角色赋予系统权限
GRANT CREATE SESSION TO role_a;
--创建角色审计策略，使能后该策略生效，使能范围内的会话连接将被审计
CREATE AUDIT POLICY audit_role_a ROLES role_a;
```

<span id="whenclause" name="whenclause" class="yaslink"></span>

### 5. when\_clause

该语句用于定义是否执行审计策略的判断条件，若条件结果为true则执行审计策略，否则不执行。

#### 5.1. audit\_condition

条件表达式，为简单的条件判断，条件结果值是true或者false。具体可为如下内容：

* 数学函数：BITAND, CEIL, FLOOR, POWER
* 字符函数：CONCAT, LOWER, UPPER
* 长度计算函数：INSTR, LENGTH
* 环境信息相关函数：SYS_CONTEXT
* 比较运算符：=, !=, <>, \<, >, \<=, >=
* 逻辑运算符：AND, OR
* [NOT] BETWEEN
* [NOT] IN

在审计条件表达式中使用SYS_CONTEXT不适用于存算一体分布式集群部署。

#### 5.2. EVALUATE PER (STATEMENT|SESSION|INSTANCE)

表示条件判断执行频率，分别对应语句级、SESSION级、实例级（一个实例运行周期里只进行一次条件判断）。

示例（单机、共享集群部署）

```sql
-- 创建审计策略，针对表area的select查询，在满足给定的条件的情况下进行审计
CREATE AUDIT POLICY up_SYS_CONTEXT
  ACTIONS SELECT ON sales.area
  WHEN 'SYS_CONTEXT(''USERENV'', ''OS_USER'') = ''SALES'''
  EVALUATE PER SESSION;
```

示例

```sql
-- 创建审计策略，针对表area的select查询，在满足给定的条件的情况下进行审计
CREATE AUDIT POLICY up3
  ACTIONS SELECT ON sales.area
  WHEN '2>1'
  EVALUATE PER SESSION;
```

<span id="toplevelclause" name="toplevelclause" class="yaslink"></span>

### 6. toplevel\_clause

该语句用于定义是否对匿名块、存储过程、函数等过程体内部的SQL语句进行审计。指定ONLY TOPLEVEL则不对内部语句进行审计。

示例

```sql
-- 创建审计策略，针对执行时涉及SELECT ANY TABLE权限的语句进行审计但不针对匿名快、存储过程、函数的内部语句进行审计。
CREATE AUDIT POLICY up4
  PRIVILEGES SELECT ANY TABLE
  ONLY TOPLEVEL;
```
