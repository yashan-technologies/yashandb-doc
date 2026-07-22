Outline ensures the stability of execution plans through fixed SQL statement execution plans. The specified storage outline can be created using [CREATE OUTLINE](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE OUTLINE). The SQL_TEXT syntax generates the corresponding Hint information based on the current execution plan of the SQL statement, while the SQL_ID syntax sets Hint information for a specific SQL statement. When a stored outline is available, the database will automatically generate the execution plan for the corresponding SQL statement based on these Hint information.

## Outline Switch

The system controls the Outline switch through USE_STORED_OUTLINES (default value `FALSE`). The explanation of the parameter values is as follows:

- true: Use the Outline with the Category as DEFAULT.
- false: Default value, do not use Outline.
- category_name: Use the Outline of a specified Category.

**Category**

When creating an OUTLINE, a category needs to be specified for the purpose of classifying and managing the Outline.

An OUTLINE created using SQL_ID syntax does not have a CATEGORY and can be used when USE_STORED_OUTLINES is not false.

## Outline Management

Outlines are defined and maintained by users, including creating, modifying, and deleting. For specific operations, please refer to [CREATE OUTLINE](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE OUTLINE), [ALTER OUTLINE](../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER OUTLINE), and [DROP OUTLINE](../../../Development Guide/SQL Reference Manual/SQL Statements/DROP OUTLINE).

Successfully created Outlines can be viewed through the dba_outlines/all_outlines/user_outlines views to check their definition information, and through dba_outline_hints/all_outline_hints/user_outline_hints views to check the contained Hints.

## Outline Limitations

Currently, outlines only support generating hints of SCAN and JOIN types, specifically FULL, INDEX, USE_NL, USE_MERGE, USE_HASH as included in [hint](../../../Development Guide/SQL Reference Manual/General SQL Syntax/hint).

## Outline Usage

1. Create an Outline:

   ```sql
   -- Create Outline for the given SQL.
   CREATE OUTLINE yashan_outline FOR CATEGORY ctgy_yashan ON 
   SELECT /*+ full(a) */ a.area_name, b.branch_name
   FROM area a, branches b
   WHERE a.area_no = b.area_no
   AND b.branch_no like '01%'
   AND a.area_no like '01';

   CREATE OUTLINE yashan_outline1 FOR CATEGORY ctgy_yashan ON 
   SELECT /*+ full(a) */ a.area_name
   FROM area a
   WHERE a.area_no like '01';
   ```

2. Enable the Outline of category ctgy_yashan, where all Outlines under this category will take effect and be recognized by the optimizer:

   ```sql
   ALTER SESSION SET USE_STORED_OUTLINES=ctgy_yashan;
   ```

3. Check if the Outline is effective. Note that the system will first perform a case-insensitive match for this SQL statement and the SQL statements under the ctgy_yashan category. If a match is successful, the optimizer will adopt the Outline and display it in the execution plan, as shown below:

   ```sql
   EXPLAIN 
   SELECT /*+ full(a) */ a.area_name, b.branch_name
   FROM area a, branches b
   WHERE a.area_no = b.area_no
   AND b.branch_no like '01%'
   AND a.area_no like '01';

   PLAN_DESCRIPTION                                                 
   ---------------------------------------------------------------- 
   SQL hash value: 1136128624                                      
   Optimizer: ADOPT_C                                              
                                                                  
   +----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
   | Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
   +----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
   |  0 | SELECT STATEMENT               |                      |            |          |             |                                |
   |* 1 |  HASH JOIN INNER               |                      |            |      8000|      722( 0)|                                |
   |* 2 |   TABLE ACCESS FULL            | AREA                 | SALES      |     10000|      445( 0)|                                |
   |* 3 |   TABLE ACCESS BY INDEX ROWID  | BRANCHES             | SALES      |          |             |                                |
   |* 4 |    INDEX RANGE SCAN            | SYS_C_36             | SALES      |     10000|      159( 0)|                                |
   +----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                  
   Operation Information (identified by operation id):             
   ---------------------------------------------------             
                                                                  
      1 - Predicate : access("A"."AREA_NO" = "B"."AREA_NO")        
      2 - Predicate : filter("A"."AREA_NO" LIKE '01')              
      3 - Predicate : filter("B"."BRANCH_NO" LIKE '01%')           
      4 - Predicate : access("B"."BRANCH_NO" LIKE '01%')           
                                                                  
   Hint Information :                                              
   ---------------------------------------------------             
                                                                  
   full(a) / rejected by IGNORE_OPTIM_EMBEDDED_HINTS               
                                                                  
   Outline Information :                                           
   ---------------------------------------------------             
                                                                  
      - outline OL_AB used for this statement 
   ```

4. View the view to confirm if the hints included in the Outline are consistent with the operators and order listed in the execution plan:

   ```sql
   SELECT join_pos, hint
   FROM user_outline_hints
   WHERE name = 'OL_AB';
   JOIN_POS HINT                                                             
   -------- -----------------------
         0 LEADING(A B)                                                    
         0 USE_HASH(A B)                                                   
         1 FULL(A)                                                         
         1 INDEX(B SYS_C_36) 
   ```
