
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
CREATE TABLE "CREATE01" (c1 INT);             -- Defined WITH ALL upper CASE
INSERT INTO Create01 VALUES(1);               -- Referenced WITHOUT DOUBLE quotes, CASE insensitivity applies
INSERT INTO "Create01" VALUES(1);             -- Referenced WITH DOUBLE quotes, CASE sensitivity strictly applied
[1:12]YAS-02012 TABLE OR VIEW does NOT exist
  
DROP TABLE Create01;
CREATE TABLE "CREATe01" (c1 INT);             -- WHEN defined WITH non-ALL upper CASE, must reference WITH DOUBLE quotes, AND CASE sensitivity IS strictly applied
INSERT INTO CREATE01 VALUES(1);
[1:12]YAS-02012 table or view does not exist
INSERT INTO "CREATE01" VALUES(1);
[1:12]YAS-02012 table or view does not exist
INSERT INTO "CREATe01" VALUES(1);
  
-- Identifier that violates constraints as name, must use double quotes for both defining and referencing
CREATE TABLE "CREATE" (c1 INT);         -- WHEN defined WITH ALL upper CASE, CASE sensitivity IS strictly observed WHEN referencing
INSERT INTO CREATE VALUES(1);
[1:12]YAS-04202 missing or invalid table name
INSERT INTO "CREATE" VALUES(1);
INSERT INTO "Create" VALUES(1);
[1:12]YAS-02012 table or view does not exist
  
DROP TABLE "CREATE";
CREATE TABLE "Create" (c1 INT);         -- WHEN defined WITH non-ALL upper CASE, CASE sensitivity IS strictly observed WHEN referencing
INSERT INTO CREATE VALUES(1);
[1:12]YAS-04202 missing or invalid table name
INSERT INTO "CREATE" VALUES(1);
[1:12]YAS-02012 table or view does not exist
INSERT INTO "Create" VALUES(1);
```
