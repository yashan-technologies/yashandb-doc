PL stands for Procedural Language/Structured Query Language, which is a programming language built on standard SQL. 

In YashanDB, PL-related functionality is parsed using Yashan syntax, regardless of the grammatical mode.

Audience:

The PL reference manual is applicable to all roles involved in development using PL language, including:

* Developers based on database PL language

* Analysts using database PL language

* Database administrators

To effectively use this document, the following knowledge base is required:

* Basic programming skills, including concepts of variables, control, and function calls

* Structured Query Language

* Basic knowledge of YashanDB database, such as system views

Manual conventions:

* Symbol   Description

* []      Indicates one or more optional items.

* {}      Indicates two or more candidates, one of which must be selected.

* |       Separates two or more options within brackets or braces.

* ...     Indicates that the preceding element can be repeated.

* Uppercase   Indicates system keywords.

* Lowercase    Indicates user identifiers or abbreviations for clauses needing user input.

Regarding the example explanations used in this manual:

* SQL and PL statements in the examples run in the *yasql* tool, using the output results of the *yasql* tool as samples for display.

* The examples use DBMS_OUTPUT.PUT_LINE statements to print output to the *yasql* tool. To achieve the output effect, ensure that "set serveroutput on" has been executed in the *yasql* tool to enable control data switching. For details, please refer to [yasql user guide](../../Tools Guide/yasql/User Guide for yasql).

* The system default display width for float and NUMBER type data output is set to 10, which can be adjusted using SET NUMWIDTH.

* Regarding date display format: To closely simulate actual business scenarios, the date format in the example database of this manual is set as 'YYYY-MM-DD HH24:MI:SS'. If the DATE_FORMAT configuration parameter is not set according to this format, the displayed date format may differ from that in this manual.

* The examples use sample tables consistent with the [SQL reference manual](../SQL Reference Manual/00SQL Reference Manual).