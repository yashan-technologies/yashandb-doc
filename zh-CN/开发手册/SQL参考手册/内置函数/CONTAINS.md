```ebnf
contains = CONTAINS "(" expr "," string_literal ")".
```

CONTAINS函数用于对文本列进行全文检索，判断文本内容是否包含指定的关键词，并返回0或1表示匹配结果。

全文检索能力依赖于全文索引（[Search Index](../SQL语句/CREATE INDEX.md#search)），通过分词器对文本进行中英文混合分词处理，实现高效的关键词匹配。 当列上未创建全文索引时，函数将执行全表扫描，性能较差。

当[LIKE条件](../通用SQL语法/condition.md#like条件)中char1为文本列且该列上已创建全文索引、char2中的模式匹配字符不与任何文本字符直接相连时，可额外扩展出一个CONTAINS函数谓词供全文索引使用。

本函数只能在谓词中使用，不支持向量化计算。

**expr**

指定要检索的文本列，expr须为CHAR、VARCHAR或CLOB类型的列，且该列上已[创建全文索引](../SQL语句/CREATE INDEX.md#search)。

expr的值不能为NULL。

**string_literal**

指定检索关键词以及匹配模式，字符串须使用单引号包围，不能为NULL。

CONTAINS函数的关键词支持以下匹配模式，且匹配模式支持组合使用，组合时的优先级从高到低为：圆括号 > EQUIV > 词组 > NOT > AND > OR。

| 模式 | 语法                             | 说明                    |
|------|--------------------------------|-----------------------|
| AND | term1 AND term2 或 term1 & term2 | 检索同时包含term1和term2的结果  |
| OR | term1 OR term2 或 term1 &#124; term2  | 检索包含term1或term2任一关键词的结果 |
| NOT | term1 NOT term2 或 term1 ~ term2 | 检索包含term1但不包含term2的结果 |
| 词组 | term1 term2 term3（中间用空格连接）     | 检索包含连续词组的结果，词序和间隔须一致  |
| 等价 | term1 EQUIV term2 或 term1 = term2 | term1和term2等价，出现任意一个均可 |

示例（HEAP表）

```sql
--创建测试表并插入数据
CREATE TABLE articles (
    id INT,
    title VARCHAR(200),
    content VARCHAR(2000)
);

INSERT INTO articles VALUES (1, 'Database Basics', 'This book covers database fundamentals including SQL, performance optimization, and database design principles.');
INSERT INTO articles VALUES (2, 'MySQL Guide', 'A comprehensive guide to MySQL database management and administration.');
INSERT INTO articles VALUES (3, 'PostgreSQL vs Oracle', 'Comparison between PostgreSQL and Oracle databases, focusing on performance and features.');
INSERT INTO articles VALUES (4, 'Full Text Search', 'Learn how to implement full text search functionality in modern databases.');
INSERT INTO articles VALUES (5, 'China Tech Report', 'Analysis of China technology industry and database market trends.');
INSERT INTO articles VALUES (6, 'DB Comparison', 'Comprehensive comparison of database systems: MySQL, PostgreSQL, and Oracle performance benchmarks.');
COMMIT;

--创建全文索引
CREATE SEARCH INDEX idx_content ON articles (content);

--单关键词查询
SELECT * FROM articles WHERE CONTAINS(content, 'database') > 0;

--AND模式查询，同时包含多个关键词
SELECT * FROM articles WHERE CONTAINS(content, 'database AND performance') > 0;

--OR模式查询，包含任意关键词
SELECT * FROM articles WHERE CONTAINS(content, 'mysql OR oracle') > 0;

--NOT模式查询，包含前者但排除后者
SELECT * FROM articles WHERE CONTAINS(content, 'database NOT mysql') > 0;

--词组查询，连续词组必须完整匹配
SELECT * FROM articles WHERE CONTAINS(content, 'full text search') > 0;

--等价模式查询，关键词互换不影响结果
SELECT * FROM articles WHERE CONTAINS(content, 'db = database') > 0;

--组合模式查询
SELECT * FROM articles WHERE CONTAINS(content, '(mysql OR postgresql) AND performance') > 0;

--LIKE条件使用全文索引
SELECT * FROM articles WHERE content like '% mysql % postgresql';
```