**expr::=**

```ebnf+diagram
syntax::= column|literal|function|NULL|ROWNUM|oper_expr
```

**oper_expr::=**

```ebnf+diagram
syntax::= expr ("+"|"-"|"*"|"/"|"%"|"||") expr
```

**expr\_list::=**

```ebnf+diagram
syntax::= ("(" (expr_list) {"," (expr_list)} ")")
```

```ebnf+diagram
syntax::= ((expr) {"," (expr)})|("(" ((expr) {"," (expr)}) ")")
```

expr代表了一个最基本的表达式，它可以如下列示项中的某一项，或者为多项组成的运算式：

- 列字段
- 变量、常量、字面量
- 函数
- NULL
- 伪列（其中ROWID和ROWSCN只适用于HEAP表）

在YashanDB中，以下一些均为合法有效的表达式：

```sql
area.area_no
'34.44'
SYSDATE
NULL
TO_NUMBER('11')%2
```

几乎所有的SQL语法描述中都会使用到expr，例如，其可以参与到SQL的查询列、函数参数、WHERE条件、表达式列表（expr_list）等各种语法场景中。
