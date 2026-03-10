YashanDB provides different types of operators, such as arithmetic operators and comparison operators. This section will introduce the meanings and rules of these operators.

Operands
----

Operands define the quantity involved in an SQL operation instruction, for example:

*   Unary: An operation with only one operand, where the unary operator is usually placed before it, such as the negation operation (-).
*   Binary: One operand on each side of the operator, for example, 1 + 2.
*   Ternary: An operator that requires three operands to perform the operation, for example, A BETWEEN B AND C.

Operator Precedence
-----

When multiple types of operators appear in the same SQL expression, the operator precedence from highest to lowest is shown in the table below. Operators listed in the same row have the same precedence:

|Precedence |Operator |Operation |
|-----|------------|--------|
| 1   | +, -         | Positive, Negative  |
| 2   | ^            | XOR                |
| 3   | *, /, %      | Multiplication, Division, Modulus |
| 4   | +, -, &#124;&#124;   | Addition, Subtraction, Concatenation |
| 5   | &, &#124;        | Bitwise AND, Bitwise OR |

Parentheses () can be used to adjust operator precedence.