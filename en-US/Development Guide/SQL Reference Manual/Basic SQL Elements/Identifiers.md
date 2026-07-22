
YashanDB includes two types of identifiers:

- As syntax keywords, they are used to delimit the positions of the SQL syntax tree; as system [reserved syntax keywords](../../../Reference Manual/Reserved Keywords), they cannot be used as names for database objects.
- As names for database objects, they are used for table names, column names, aliases, etc., but must comply with the following naming conventions.


## Naming Conventions for Database Object

When identifiers are used as names, the following rules and constraints exist: (when not using double quotes)

*   Supports all case-sensitive letters and digits, and is case insensitive.
*   Cannot contain special characters such as '\\0', ',', ' ', '+', '-', '\*', '/', '|', '(', '%', ':', '?', '.', '\\t', '\\r', '\\n', '=', '\\\\', '!', '>', '<', ';', '&', '^', '"', '~', ''', '{', '}', '[', ']', etc.
*   System reserved syntax keywords cannot be used as names (case insensitive).

When double quotes are used before and after the identifier:

*   Not restricted by the above constraints, i.e., special characters can appear in the name, can start with non-letters, and system reserved syntax keywords can be used as names.
*   Case handling must comply with the following rules: (and must also meet the [general rules for double quotes](Double Quotes))

|Identifier |Double Quotes |When Defining Name |When Referencing Name |
| -------- | ------------ | ---------- | ------------------------------------------------------------ |
| Complies with Constraints | Using double quotes | Optional           | All upper case definition: <br/>\*   When referenced with double quotes, case sensitivity is strictly observed <br/>\*   When referenced without double quotes, case sensitivity is not observed <br/>Non-all upper case definition: <br/>\*   Must be referenced with double quotes <br/>\*   Case sensitivity is strictly observed |
| Complies with Constraints | Not using double quotes | Optional           | \*   Case sensitivity is not observed                               |
| Violates Constraints | Using double quotes | Must               | \*   Must be referenced with double quotes <br>\*   Case sensitivity is strictly observed                             |
| Violates Constraints | Not using double quotes | Error              | Error                                                         |

## Usage Examples

```sql
-- Identifier that complies with constraints as name, defined with double quotes
CREATE TABLE "CREATE01" (c1 INT);             -- Defined with all upper case
Succeed.
INSERT INTO Create01 VALUES(1);               -- Referenced without double quotes, case insensitivity applies
1 row affected.
INSERT INTO "Create01" VALUES(1);             -- Referenced with double quotes, case sensitivity strictly applied
[1:12]YAS-02012 table or view does not exist
  
DROP TABLE Create01;
Succeed.
CREATE TABLE "CREATe01" (c1 INT);             -- When defined with non-all upper case, must reference with double quotes, and case sensitivity is strictly applied
Succeed.
INSERT INTO CREATE01 VALUES(1);
[1:12]YAS-02012 table or view does not exist
INSERT INTO "CREATE01" VALUES(1);
[1:12]YAS-02012 table or view does not exist
INSERT INTO "CREATe01" VALUES(1);
1 row affected.
  
-- Identifier that violates constraints as name, must use double quotes for both defining and referencing
CREATE TABLE "CREATE" (c1 INT);         -- When defined with all upper case, case sensitivity is strictly observed when referencing
Succeed.
INSERT INTO CREATE VALUES(1);
[1:12]YAS-04202 missing or invalid table name
INSERT INTO "CREATE" VALUES(1);
1 row affected.
INSERT INTO "Create" VALUES(1);
[1:12]YAS-02012 table or view does not exist
  
DROP TABLE "CREATE";
Succeed.
CREATE TABLE "Create" (c1 INT);         -- When defined with non-all upper case, case sensitivity is strictly observed when referencing
Succeed.
INSERT INTO Create VALUES(1);
[1:12]YAS-04202 missing or invalid table name
INSERT INTO "CREATE" VALUES(1);
[1:12]YAS-02012 table or view does not exist
INSERT INTO "Create" VALUES(1);
1 row affected.
```
