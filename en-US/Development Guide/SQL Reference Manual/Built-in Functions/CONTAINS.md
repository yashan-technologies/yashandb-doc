```ebnf
contains = CONTAINS "(" expr "," string_literal ")".
```

The CONTAINS function performs full-text search on a text column, determining whether the text content contains the specified keywords, and returns 0 or 1 to indicate the matching result.

Full-text search capability depends on a [Search Index](../SQL Statements/CREATE INDEX.md#search), which uses a tokenizer to perform mixed Chinese and English word segmentation for efficient keyword matching. When no Search Index exists on the column, the function will perform a full table scan with poor performance.

When a [LIKE condition](../General SQL Syntax/condition.md#like) has char1 as a text column with a Search Index created on it, and the pattern matching characters in char2 are not directly adjacent to any text characters, an additional CONTAINS function predicate can be generated for the Search Index to use.

This function can only be used in predicates and does not support vectorized computation.

**expr**

Specifies the text column to search. expr must be of type CHAR, VARCHAR or CLOB, and a [Search Index](../SQL Statements/CREATE INDEX.md#search) must have been created on this column.

expr cannot be NULL.

**string_literal**

Specifies the search keywords and matching modes. The string must be enclosed in single quotes, and cannot be NULL.

The CONTAINS function supports the following matching modes for keywords. Matching modes can be combined, and the priority from highest to lowest when combined is: parentheses > EQUIV > Phrase > NOT > AND > OR.

| Mode | Syntax | Description |
|------|--------|-------------|
| AND | term1 AND term2 or term1 & term2 | Retrieves results containing both term1 and term2 |
| OR | term1 OR term2 or term1 &#124; term2 | Retrieves results containing either term1 or term2 |
| NOT | term1 NOT term2 or term1 ~ term2 | Retrieves results containing term1 but not term2 |
| Phrase | term1 term2 term3 (connected by spaces) | Retrieves results containing the consecutive phrase, with consistent word order and spacing |
| Equiv | term1 EQUIV term2 or term1 = term2 | term1 and term2 are equivalent; either one matches |

Examples (HEAP tables)

```sql
-- Create test table and insert data
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

-- Create a Search Index
CREATE SEARCH INDEX idx_content ON articles (content);

-- Single keyword query
SELECT * FROM articles WHERE CONTAINS(content, 'database') > 0;

-- AND mode query, containing multiple keywords
SELECT * FROM articles WHERE CONTAINS(content, 'database AND performance') > 0;

-- OR mode query, containing any keyword
SELECT * FROM articles WHERE CONTAINS(content, 'mysql OR oracle') > 0;

-- NOT mode query, containing the first but excluding the second
SELECT * FROM articles WHERE CONTAINS(content, 'database NOT mysql') > 0;

-- Phrase query, consecutive words must match exactly
SELECT * FROM articles WHERE CONTAINS(content, 'full text search') > 0;

-- Equiv mode query, keywords are interchangeable
SELECT * FROM articles WHERE CONTAINS(content, 'db = database') > 0;

-- Combined mode query
SELECT * FROM articles WHERE CONTAINS(content, '(mysql OR postgresql) AND performance') > 0;

-- LIKE condition using Search Index
SELECT * FROM articles WHERE content like '% mysql % postgresql';
```