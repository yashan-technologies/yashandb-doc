
**condition::=**

```ebnf
= ["("] (([NOT] expr)|
expr ("<"|"<="|">"|">="|"="|"<>"|"!=") (((ANY|SOME|ALL) (subquery|expr_list))|expr|subquery)|
((expr [NOT] IN (expr_list|subquery))|(expr_list [NOT] IN (expr_list|subquery)))|
expr [NOT] LIKE expr [ESCAPE esc_char]|
expr [NOT] RLIKE expr|
([NOT] EXISTS) subquery|
expr IS ([NOT] NULL)|
expr IS ([NOT] EMPTY)|
expr [NOT] BETWEEN expr AND expr|
rownum_clause|
start_date1 end_date1 OVERLAPS start_date2 end_date2) [")"] 
[(AND|OR) condition].
```

**[Definition of expr and expr_list](expr)**

The condition is a clause that is applied in grammatical scenarios such as WHERE, CHECK, etc., which require conditional evaluation, and the condition will return a TRUE or FALSE value.

YashanDB supports various conditional specifications that comply with SQL standards:

*   NOT

*   =|>|>=|<|<=|!=|<> [ANY|SOME|ALL]
*   IN|NOT IN
*   LIKE|NOT LIKE \[ESCAPE\]
*   RLIKE|NOT RLIKE
*   EXISTS|NOT EXISTS
*   IS NULL|IS NOT NULL

*   IS EMPTY|IS NOT EMPTY

*   BETWEEN...AND...
*   ROWNUM
*   Any expression that can produce BOOLEAN values

## Comparison Conditions

The comparison operator (=|>|>=|<|<=|!=|<>) is used to compare the expressions on its left and right sides, yielding a boolean result.

### ANY|SOME

The ANY|SOME syntax selects a subset that meets the comparison criteria from the set on the right side of the comparison operator, then compares them one by one. As long as one TRUE value is obtained, it returns TRUE; if all are FALSE, it returns FALSE; if the set on the right is a subquery and is empty, it returns FALSE.

The right-side set can be a list of expressions or a subquery statement; for comparisons without specifying ANY|SOME, the right side of the comparison operator can only be an expression that produces a single row result.

When either side of the comparison operator contains NULL, it returns FALSE.

***Example***

```sql
-- ANY and SOME have the same effect
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE BRANCH_NO < ANY('0303','0401');
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0001      Shenzhen                                                         
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen                                                         
0201      Chengdu         
 
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE area_no <> SOME(SELECT area_no FROM area WHERE area_no IN ('01','02'));
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen                                                         
0401      Beijing                                                         
0402      Tianjin                                                         
0403      Dalian                                                         
0404      Shenyang                                                         
0201      Chengdu                                                         
0502      Changsha
```

### ALL

The ALL syntax selects a subset that meets the comparison criteria from the set on the right side of the comparison operator, then compares them one by one. If one FALSE value is found, it returns FALSE; if all are TRUE, it returns TRUE; if the right side set is a subquery and is empty, it returns TRUE.

The right-side set can be a list of expressions or a subquery statement; for comparisons without specifying ALL, the right side of the comparison operator can only be an expression that produces a single row result.

When either side of the comparison operator contains NULL, it returns FALSE; however, for NULL > ALL (an empty result set), it returns TRUE.

***Example***

```sql
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE branch_no != ALL('0001','0303','0401','0403');
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen                                                         
0201      Chengdu
0402      Tianjin
0404      Shenyang                                                         
0502      Changsha
```

## IN Condition

The IN syntax compares the values in the left set with the values in the right set for equality; if all values on the left match, it returns TRUE; otherwise, it returns FALSE.

The right-side set can be a list of expressions or a subquery statement; when the left side is a list of expressions, the right side must be a collection from that list, and the subquery returns the result set of corresponding list items.

***Example*** for Heap tables

```sql
-- heap table 
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES b
WHERE (branch_no,area_no) IN
(SELECT b.branch_no,area_no FROM area a
WHERE a.area_no IN ('01','02')
AND a.area_no=b.area_no);
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen                                                         
0201      Chengdu
```



## LIKE Condition

The LIKE syntax requires specifying the character expression char1 to match against the pattern string char2. Through the matching algorithm, char1 is compared to char2, and if the match is successful, it returns TRUE; if the match fails, it returns FALSE. Its complete syntax form is:

char1 \[NOT\] LIKE char2 \[ESCAPE esc\_char\].

Where char1 represents the character expression to match, char2 represents the pattern to match, and esc_char represents the escape character.

The LIKE statement follows these rules:

*   char1, char2, and esc_char can all be expressions, whose result can be any data type. When their data types are inconsistent, they will be converted to VARCHAR type for comparison.
*   When either char1 or char2 is NULL, it returns NULL.
*   char2 can contain special pattern matching characters: underscore (__) represents an exact match with one character in the value; the percentage sign (%) represents a match with zero or more characters in the value (excluding empty).

*   Escape is used to escape special pattern matching characters, turning % or _ into their literal meanings. If escape is specified, any character following esc_char in char2 must be % or _ or esc_char itself; otherwise, it returns error YAS-04428 or YAS-04429.

*   If the character after esc_char is esc_char itself, it means treating it as its literal meaning; for example, when esc_char is `/`, the pattern `//` matches the character `/`, but the third `/` in `///` will act as the escape character.

*   esc_char must be a character of length 1, or a result that becomes a character of length 1 after operations.

***Example***

```sql
-- Exact match
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE BRANCH_NO||'11/1' LIKE '_1__11_1';
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen       
 
-- No data meets the condition after escaping
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE BRANCH_NO||'11/1' LIKE '_1__11/_1' ESCAPE '/';
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------

-- Changing to literal character search and removing the wildcard can find data
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE BRANCH_NO||'11/1' LIKE '_1__11//1' ESCAPE '/';
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen
```

## RLIKE Condition



The RLIKE syntax matches the character expression expr with the regular expression regexp using a regular expression matching algorithm. It achieves the same effect as the [REGEXP_LIKE](../Built-in Functions/REGEXP REGEXP_LIKE RLIKE) function when its third parameter is omitted. It returns 1 on a successful match and 0 on a failed match. 

The RLIKE conditional expression is a synonym for the REGEXP conditional expression, with a return type of TINYINT.

The complete syntax form of RLIKE is:



expr \[NOT\] RLIKE regexp.

The RLIKE statement follows these rules:

*   expr is the character expression to match, which must be of character type or another type that can be converted to character type.
*   regexp is the regular expression to match, which must also be of character type or another type that can be converted to character type, with a length no more than 512 bytes.
*   If either expr or regexp is NULL, it returns NULL.

***Example***

```sql
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE BRANCH_NO RLIKE '01[0-9]+';
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen
```

## BETWEEN AND Condition

The BETWEEN AND syntax determines whether the value of an expression is within the range defined by two other expressions. All three expressions in the statement must be numeric, character, or date-time expressions. When the data types of the three expressions are inconsistent, type conversion is performed first, and if conversion fails, an error is returned.

***Example***

```sql
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE BRANCH_NO NOT BETWEEN '01' AND '05';
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0001      Shenzhen                                                         
0501      Wuhan                                                         
0502      Changsha
```




## OVERLAPS

OVERLAPS is a function specifically used to compare whether date ranges overlap. It returns true if there is intersection in the time range; otherwise, it returns false. The syntax format is as follows:

(start_date1, end_date1) OVERLAPS (start_date2, end_date2) 

The OVERLAPS function follows these rules:

*   The expressions start_date1, end_date1, start_date2, and end_date2 cannot be omitted and can accept null or empty string inputs.
*   The types of the four expressions must be consistent, and the parameter types must be DATE, TIMESTAMP, or TIME; if other types are provided, an error will be raised.
*   The return type is BOOL, and the output result is true or false.
*   The interval is open-ended.
*   An error is raised if one side of the interval is entirely defined as null.

***Example***

```sql
-- Determine if the time interval (2022-01-01, 2024-03-05) overlaps with the interval (null, 2024-01-05)
select 1 from dual where (to_date('2022-01-01', 'YYYY-MM-DD'), to_date('2024-03-05', 'YYYY-MM-DD')) overlaps (null, to_date('2024-01-05', 'YYYY-MM-DD'));

           1 
------------ 
           1

1 row fetched.
```
