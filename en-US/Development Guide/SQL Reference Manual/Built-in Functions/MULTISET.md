```ebnf
multiset = CAST "(" MULTISET "(" subquery ")" AS type_name ")".
```

The MULTISET function returns the result set of the subquery subquery in the form of a collection (nested table), where each member of the collection corresponds to a row returned by the subquery. The return value of the MULTISET function needs to be converted to the target collection type type_name using the [CAST](CAST) function.

**subquery**

The specified subquery.

**type\_name**

The specified target collection type for conversion, which must be either a varray or a nested table, where:

The member type of the collection is an object, and the number of attributes in the object must be equal to the number of projection columns in the subquery. The type of the nth projection column must be convertible to the type of the nth attribute in the object.

When there is only one projection column, the member type of the collection can also be a type that is convertible to that projection column type.

***Example***

```sql
create or replace type obj_multiset is object(col1 int, col2 int, col3 int);
/
create or replace type nt_multiset is table of obj_multiset;
/

select * from table(cast(multiset(select 1, 2, 3 from dual) as nt_multiset));

COL1         COL2         COL3 
------------ ------------ ------------ 
           1            2            3
```
