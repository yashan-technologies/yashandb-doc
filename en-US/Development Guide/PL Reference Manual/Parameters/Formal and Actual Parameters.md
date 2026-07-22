In PL, parameters can be differentiated into two concepts: formal parameters and actual parameters.

Formal parameters: Parameters specified when defining a PL object, located in the parentheses following the PL object name in the header structure.

Actual parameters: Parameters specified when calling a PL object, located in the parentheses following the specified PL object name at the time of invocation.

The PL executor will correspond actual parameters to formal parameters in one of the following three ways:

* The actual parameter directly inputs a value or variable, corresponding to the formal parameter by position.
* The actual parameter uses "=>" to specify the name of the formal parameter for correspondence.
* The first two ways can be mixed, but if there are multiple formal parameters, if any actual parameter specified by name corresponds to a formal parameter, then all subsequent actual parameters corresponding to formal parameters must also be specified by name.

If the data type of the actual parameter is inconsistent with that of the formal parameter, the system will convert it to the data type of the formal parameter according to the conversion rules referenced in the [CAST](../../SQL Reference Manual/Built-in Functions/CAST) function description. If conversion fails, an error will be raised.

> **Note**: 
>
> Unlike in CAST where `CHAR` defaults to `CHAR(1)`, there is no such rule for parameter conversion in PL.

***Example***

```plsql
-- Here, argu is called a formal parameter
CREATE OR REPLACE PROCEDURE ya_proc(argu1 FLOAT, argu2 VARCHAR DEFAULT 'shenzhen') AS
BEGIN
DBMS_OUTPUT.PUT_LINE(TO_CHAR(argu1)||'------'||argu2);
END;
/
  
-- Here, argu is called an actual parameter, and this value will be assigned to the formal parameter
SET NUMWIDTH 20;
exec ya_proc('4');             
  
--result
4.0E+000------shenzhen
  
-- An error will be thrown if the actual parameter cannot be converted to the corresponding formal parameter data type
exec ya_proc('shenzhen');             
  
--result
  
YAS-00008 type convert error : not a valid number
```

Format of Formal Parameters
-----

The format of formal parameters is: _parameter name [parameter type] [NOCOPY] data type [default value]_.

The data type should not include attributes such as length or precision.

Types of Formal Parameters
-----

There are three types: IN, OUT, and IN OUT. When defining a PL object, a type can be explicitly specified for each formal parameter or can be left unspecified to use the default IN type.

### IN Parameter

An IN parameter is used to pass a value to the procedure body, and the procedure body cannot modify this parameter's value. The corresponding actual parameter can be either a constant or a variable.

### OUT Parameter

An OUT parameter is used to return a value to the caller. The corresponding actual parameter must be a variable, and the value of the actual parameter will not be assigned to the formal parameter but will have the formal parameter value assigned to the actual parameter after the procedure body execution.

### IN OUT Parameter

An IN OUT parameter passes an initial value to the procedure body and returns an updated value to the caller. The corresponding actual parameter must be a variable, and the value of the actual parameter is assigned to the formal parameter, which can be read and written inside the procedure body. After the execution of the procedure body, the formal parameter value is assigned to the actual parameter for return.

### NOCOPY

This keyword is used to reduce the memory copy overhead of aggregate type parameters (VARRAY, NESTED TABLE, or INDEX-BY TABLE) during subroutine calls. When specified, parameters are passed by reference; if not specified, they are passed by value by default.

The usage rules for the NOCOPY keyword are as follows:

- It only takes effect for OUT and IN OUT parameters of subprograms, subfunctions, or nested subprograms within stored procedures, user-defined functions, packages, or user-defined types. In other scenarios, it is retained only for backward compatibility.

- Regardless of whether the NOCOPY keyword is specified, bound parameters are always passed by value.

Default Values of Formal Parameters
------

Only IN parameters can have default values, and the default value must meet the type requirements of the formal parameter.

For formal parameters with default values, the corresponding actual parameters can be omitted, provided that all formal parameters with default values are defined at the end of the parentheses, and they must either all be specified or all omitted; otherwise, it cannot be guaranteed that the actual parameters correspond to the correct formal parameters. However, when an actual parameter is passed using "=>", there is no requirement for these prerequisites.

Restrictions on using default values:

* Currently, default values only support constants, sysdate, sequence, functions, functions in package, and functions in UDT.
* The default values for nested subprocedures and explicit cursors can be bind parameters.

***Example***

```plsql
-- Create a stored procedure with three types of formal parameters
CREATE OR REPLACE PROCEDURE ya_proc(
argu_a int,
argu_b IN int,
argu_c OUT int,
argu_d IN OUT BINARY_FLOAT
) IS
BEGIN
DBMS_OUTPUT.PUT_LINE('Inside procedure ya_proc:');
DBMS_OUTPUT.PUT('IN argu_a = ');
DBMS_OUTPUT.PUT_LINE(NVL(TO_CHAR(argu_a), 'NULL'));
DBMS_OUTPUT.PUT('IN argu_b = ');
DBMS_OUTPUT.PUT_LINE(NVL(TO_CHAR(argu_b), 'NULL'));
DBMS_OUTPUT.PUT('OUT argu_c = ');
DBMS_OUTPUT.PUT_LINE(NVL(TO_CHAR(argu_c), 'NULL'));
DBMS_OUTPUT.PUT_LINE('IN OUT argu_d = ' || TO_CHAR(argu_d));
argu_c := argu_a+10;
argu_d := 10/argu_b;
END;
/
  
-- Create an anonymous block to call ya_proc, demonstrating the values of three types of formal parameters before, during, and after executing the stored procedure
DECLARE
aa int := 1;
bb int := 2;
cc int := 3;
dd BINARY_FLOAT := 4;
ee int;
ff BINARY_FLOAT := 5;
BEGIN
DBMS_OUTPUT.PUT_LINE('Before invoking procedure ya_proc:');
DBMS_OUTPUT.PUT('aa = ');
DBMS_OUTPUT.PUT_LINE(NVL(TO_CHAR(aa), 'NULL'));
DBMS_OUTPUT.PUT('bb = ');
DBMS_OUTPUT.PUT_LINE(NVL(TO_CHAR(bb), 'NULL'));
DBMS_OUTPUT.PUT('cc = ');
DBMS_OUTPUT.PUT_LINE(NVL(TO_CHAR(cc), 'NULL'));
DBMS_OUTPUT.PUT_LINE('dd = ' || TO_CHAR(dd));
ya_proc (aa,
bb,
cc,
dd
);
DBMS_OUTPUT.PUT_LINE('After invoking procedure ya_proc:');
DBMS_OUTPUT.PUT('aa = ');
DBMS_OUTPUT.PUT_LINE(NVL(TO_CHAR(aa), 'NULL'));
DBMS_OUTPUT.PUT('bb = ');
DBMS_OUTPUT.PUT_LINE(NVL(TO_CHAR(bb), 'NULL'));
DBMS_OUTPUT.PUT('cc = ');
DBMS_OUTPUT.PUT_LINE(NVL(TO_CHAR(cc), 'NULL'));
DBMS_OUTPUT.PUT_LINE('dd = ' || TO_CHAR(dd));
  
DBMS_OUTPUT.PUT_LINE('Before invoking procedure ya_proc:');
DBMS_OUTPUT.PUT('ee = ');
DBMS_OUTPUT.PUT_LINE(NVL(TO_CHAR(ee), 'NULL'));
DBMS_OUTPUT.PUT_LINE('ff = ' || TO_CHAR(ff));
ya_proc (1,
(bb+3)*4,
ee,
ff
);
DBMS_OUTPUT.PUT_LINE('After invoking procedure ya_proc:');
DBMS_OUTPUT.PUT('ee = ');
DBMS_OUTPUT.PUT_LINE(NVL(TO_CHAR(ee), 'NULL'));
DBMS_OUTPUT.PUT_LINE('ff = ' || TO_CHAR(ff));
END;
/
  
--result
Before invoking procedure ya_proc:
aa = 1
bb = 2
cc = 3
dd = 4.0E+000
Inside procedure ya_proc:
IN argu_a = 1
IN argu_b = 2
OUT argu_c = NULL
IN OUT argu_d = 4.0E+000
After invoking procedure ya_proc:
aa = 1
bb = 2
cc = 11
dd = 5.0E+000
Before invoking procedure ya_proc:
ee = NULL
ff = 5.0E+000
Inside procedure ya_proc:
IN argu_a = 1
IN argu_b = 20
OUT argu_c = NULL
IN OUT argu_d = 5.0E+000
After invoking procedure ya_proc:
ee = 11
ff = 5.0E-001
```
