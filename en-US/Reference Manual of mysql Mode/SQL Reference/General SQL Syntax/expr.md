
**expr::=**

```ebnf
= column|literal|function|NULL|ROWNUM|oper_expr.
```

**oper_expr::=**

```ebnf
= expr ("+"|"-"|"*"|"/"|"%"|"||") expr.
```

**expr\_list::=**

```ebnf
= ("(" expr_list {"," expr_list } ")").
```

```ebnf
= (expr {"," expr})|("(" (expr {"," expr}) ")").
```

expr represents a fundamental expression, which can be one of the following items or a combination of multiple items in an operation:

- Column field
- Variable, constant, literal
- Function
- NULL
- Pseudocolumn 
- Subquery

In YashanDB, the following are all valid expressions:

```sql
area.area_no
'34.44'
SYSDATE
NULL
TO_NUMBER('11')%2
```

Almost all SQL syntax descriptions use expr, such as in SQL query columns, function parameters, WHERE conditions, expression lists (expr_list), and other syntax scenarios.
