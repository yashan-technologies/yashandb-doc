高级包内允许定义使用多个具有相同名称，但[形参](参数/形参和实参)的名称、数量、顺序或[数据类型](../SQL参考手册/数据类型（yashan模式）/00数据类型（yashan模式）)不同的子过程。PL可以通过重载子过程功能实现对此类同名子过程的筛选调用。

PL子过程重载的约束如下：

- PL子过程重载不适用于分布式部署。

- PL重载的目标对象必须为用户自定义包内的子过程，不能为全局/嵌套子过程。

- 以下子过程无法作为PL重载的目标对象：
  
  - 不同子过程间仅形参的出入参数方式不同，例如`IN`和`OUT`。

  - 不同子过程间仅形参有/无参数默认值的差异。
  
  - 不同子函数间仅返回值类型不同。

## 重载子过程

如果自定义包中定义了多个同名子过程，调用对应名称的子过程时，YashanDB将根据传入的[实参](参数/形参和实参)与各子过程的形参列表进行匹配，从而决定实际需执行哪个子过程。如果最终未匹配到具体的某一个子过程或匹配到多个子过程，调用将失败。

匹配维度包括参数的名称、数量、顺序以及数据类型，关于数据类型的匹配规则如下：

- 仅数据类型不一致且均为[数值型](../SQL参考手册/数据类型（yashan模式）/数值型)时，将按类型接近、精度优先的原则进行匹配，选取首个被匹配中的子过程进行调用。

- 仅数据类型不一致时，YashanDB会先尝试将实参的数据类型[隐式转换](../SQL参考手册/数据类型（yashan模式）/数据类型转换)为形参的数据类型再进行匹配。

子过程重载调用方式比较灵活，为避免隐式转换过程中出现异常或导致非预期的结果，建议：

- 明确定义子过程各个参数接受哪种数据类型，调用时使用与形参完全一致的数据类型。

- 使用更精确的数据类型写法，例如实参以3.0f方式传入FLOAT类型数据。

- 通过类型转换函数将实参转换成目标子过程中形参的数据类型，例如将实参通过[CAST](../SQL参考手册/内置函数（yashan模式）/CAST)函数或[显式转换](../SQL参考手册/数据类型（yashan模式）/数据类型转换)为与形参一致的数据类型。

示例（单机、共享集群部署）

```plsql
DROP PACKAGE IF EXISTS pack_calc;
CREATE OR REPLACE PACKAGE pack_calc AS
    FUNCTION calc_max(val1 NUMBER, val2 NUMBER) RETURN NUMBER;
    FUNCTION calc_max(val1 NUMBER, val2 NUMBER, val3 NUMBER) RETURN NUMBER;
END;
/

CREATE OR REPLACE PACKAGE BODY pack_calc AS
    FUNCTION calc_max(val1 NUMBER, val2 NUMBER) RETURN NUMBER IS 
    BEGIN
        IF val1 > val2 THEN
            RETURN val1;
        ELSE
            RETURN val2;
        END IF;
    END;
    FUNCTION calc_max(val1 NUMBER, val2 NUMBER, val3 NUMBER) RETURN NUMBER IS 
    BEGIN
        RETURN calc_max(calc_max(val1, val2), val3);
    END;
END;
/

SELECT pack_calc.calc_max(1, 2) AS a, pack_calc.calc_max(1, 2, 3) AS b FROM dual;

-- result
          A           B
----------- -----------
          2           3

```

## 常见问题

子过程重载调用时，如果子过程调用的参数个数或类型不正确，无法匹配到对应的子过程，将上报`YAS-06923`错误。如果子过程调用过程中，匹配到多个符合同等条件的子过程时，将上报`YAS-06924`错误。

### 声明时出现完全一致的子过程

示例（单机、共享集群部署）

```plsql
CREATE OR REPLACE PACKAGE pack1 AS
    FUNCTION func(val NUMBER) RETURN NUMBER;
    FUNCTION func(val NUMBER) RETURN NUMBER;
END;
/
YAS-05290 duplicate item "FUNC" in package

-- 使用不同的参数个数、参数名或数据类型可以通过验证
CREATE OR REPLACE PACKAGE pack2 AS
    FUNCTION func(val NUMBER) RETURN NUMBER;
    FUNCTION func(val DOUBLE) RETURN NUMBER;
END;
/
```

### 声明了不同的子过程，但调用过程中出现了二义性

示例（单机、共享集群部署）

```plsql
CREATE OR REPLACE PACKAGE pack1 AS
    FUNCTION func(val1 NUMBER) RETURN NUMBER;
    FUNCTION func(val1 NUMBER, val2 NUMBER default 3) RETURN NUMBER;
END;
/

CREATE OR REPLACE PACKAGE BODY pack1 AS
    FUNCTION func(val1 NUMBER) RETURN NUMBER IS BEGIN RETURN 1; END;
    FUNCTION func(val1 NUMBER, val2 NUMBER default 3) RETURN NUMBER IS BEGIN RETURN 2; END;
END;
/

SELECT pack1.func(val1 => 3) FROM dual;
-- 系统无法该匹配到唯一的一个函数导致出错
YAS-06924 too many declarations of 'FUNC' match this call

SELECT pack1.func(val1 => 3, val2 => 4) FROM dual;
-- 通过参数名能识别到调用的函数，可以执行成功
PACK1.FUNC(VAL1=>3,VAL2=>4)
---------------------------
                          2
```

### 重载使用时，由于数据类型隐式转换过程产生了二义性

示例（单机、共享集群部署）

```plsql
CREATE OR REPLACE PACKAGE pack1 AS
    FUNCTION func(val1 NUMBER, val2 VARCHAR) RETURN NUMBER;
    FUNCTION func(val1 NUMBER, val2 NUMBER) RETURN NUMBER;
END;
/

CREATE OR REPLACE PACKAGE BODY pack1 AS
    FUNCTION func(val1 NUMBER, val2 VARCHAR) RETURN NUMBER IS BEGIN RETURN 1; END;
    FUNCTION func(val1 NUMBER, val2 NUMBER) RETURN NUMBER IS BEGIN RETURN 2; END;
END;
/

SELECT pack1.func(1, '2') FROM dual;
-- 执行正常，值为1
PACK1.FUNC(1,'2')
-----------------
                1
                
SELECT pack1.func(1, 2) FROM dual;
-- 执行正常，值为2
PACK1.FUNC(1,2)
---------------
              2


SELECT pack1.func('1', '2') FROM dual;
-- 执行失败，系统无法识别要调用哪个函数
YAS-06924 too many declarations of 'FUNC' match this call


SELECT pack1.func('1', 2) FROM dual;    
-- 执行失败，系统无法识别要调用哪个函数
YAS-06924 too many declarations of 'FUNC' match this call
```
