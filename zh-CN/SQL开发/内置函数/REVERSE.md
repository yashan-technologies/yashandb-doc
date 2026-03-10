```ebnf+diagram
reverse::= REVERSE "(" expr ")"
```

REVERSE函数按字节从左至右反转[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)字符串，例如`abc`将被反转为`cba`。

本函数仅适用于HEAP表。

**expr**

通用表达式，其值须为字符型。

当expr的值为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT REVERSE('abc') FROM DUAL;

REVERSE('ABC')
--------------
cba

-- REVERSE函数按字节进行反转，因此多字节字符的反转结果因数据库所配字符集的编码方式不同而不同

-- 示例1：数据库服务端和客户端字符集为UTF8
SELECT USERENV('language') CHARACTER_SET , REVERSE('中文字符') REVERSE FROM DUAL;

CHARACTER_SET                                                    REVERSE
---------------------------------------------------------------- -------------------
UTF8                                                             ��痭凖歸�          

-- 示例2：数据库服务端和客户端字符集为GB18030
SELECT USERENV('language') CHARACTER_SET , REVERSE('中文字符') REVERSE FROM DUAL;

CHARACTER_SET                                                    REVERSE
---------------------------------------------------------------- -------------------
GB18030                                                          肿奈兄
```