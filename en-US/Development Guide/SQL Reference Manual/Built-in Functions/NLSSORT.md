```ebnf
nlssort = NLSSORT "(" expr ["," "'" NLS_SORT "=" collation "'"] ")".
```

The NLSSORT function sorts the values of [expr](../General SQL Syntax/expr) according to the specified collation, generating a VARCHAR type sorting key.

This function can only run in the UTF-8 and GB18030 character sets.

This function is generally applied in the ORDER BY statement, at which point, the function generates an overall sorting key containing the sorting keys for each row. When the sorting key for any row is NULL, it follows the principle of NULLS LAST when in ASC order and NULLS FIRST when in DESC order.

**expr**

A general expression whose value must be of character type or other types that can be implicitly converted to character type (LOB and BFILE types support implicit conversion).

- expr cannot be BFILE or LOB data that exceeds 65534 bytes.
- When the value of expr is NULL, the function returns NULL.

**NLS_SORT**

Specifies the sorting rules, which can be omitted; the default is CHINESE_PINYIN. YashanDB supports the following sorting rules:

- CHINESE_PINYIN: Case-sensitive pinyin sorting, equivalent to SCHINESE_PINYIN_M
- CHINESE_PINYIN_CI: Case-insensitive pinyin sorting, equivalent to SCHINESE_PINYIN_M_CI

The rules for constructing sorting keys under the CHINESE_PINYIN sorting rule are as follows:

- For each character (excluding spaces), generate a four-digit string sorting key based on its Unicode value (hexadecimal), for example, for 'a' generate '0061'.
- For a string without spaces, its sorting key is composed by concatenating the sorting keys of all its characters, followed by '000001..01', where the number of '01' corresponds to the number of characters, for example, for 'aA' generate '0061004100000101'.
- For a string consisting only of spaces, regardless of the number of spaces, generate the sorting key '00000020'.
- For a string partially containing spaces, ignore spaces at the end of the string; other spaces will increase '20' in '000001..01' according to the position of the spaces, for example, for 'a(space1)(space2)(space3)A(space4)(space5) ' generate '0061004100000120202001'.

The rules for constructing sorting keys under the CHINESE_PINYIN_CI sorting rule are as follows:

- For each character (excluding spaces), first convert lowercase to uppercase, and then generate a four-digit string sorting key based on its Unicode value (hexadecimal), for example, for 'a' generate '0041'.
- For a string without spaces, its sorting key is composed by concatenating the sorting keys of all its characters, for example, for 'aA' generate '00410041'.
- For a string consisting only of spaces, regardless of the number of spaces, generate the sorting key '0000'.
- For a string partially containing spaces, ignore all spaces, for example, for 'a(space1)(space2)(space3)A(space4)(space5) ' generate '00410041'.

***Example***

```sql
-- Update the name of employee John to join in the employees table
UPDATE employees SET employee_name='join' WHERE employee_name='John';
 
-- Specify different sorting rules to sort by name
SELECT employee_name n,NLSSORT(employee_name) s1,
NLSSORT(employee_name,'NLS_SORT=CHINESE_PINYIN_CI') s2
FROM employees;
N         S1                                 S2                 
--------- ---------------------------------- --------------------
Mask      004D00610073006B000001010101       004D00410053004B   
join      006A006F0069006E000001010101       004A004F0049004E   
Anna      0041006E006E0061000001010101       0041004E004E0041   
Jack      004A00610063006B000001010101       004A00410043004B   
Jim       004A0069006D0000010101             004A0049004D                       
 
SELECT employee_name n
FROM employees
ORDER BY NLSSORT(employee_name);
N            
-------------
Anna        
Jack        
Jim         
Mask        
join 
 
SELECT employee_name n
FROM employees
ORDER BY NLSSORT(employee_name,'NLS_SORT=CHINESE_PINYIN_CI');
N            
-------------
Anna        
Jack        
Jim         
join        
Mask
```
