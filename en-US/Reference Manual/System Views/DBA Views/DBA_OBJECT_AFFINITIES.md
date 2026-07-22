This view displays the affinity information of all objects in the database.    

|Field |Type |Description |
| --- | --- |-----------------------------------------------------------------------------------------|
 OWNER      | VARCHAR(64) | Object owner username                 |
| OBJECT\_NAME       | VARCHAR(64) | Object name                            |
| SUBOBJECT\_NAME    | VARCHAR(64) | Sub-object name                       |
| OBJECT\_ID         | BIGINT      | Object ID                            |
| DATA\_OBJECT\_ID   | BIGINT      | Object Segment ID                      |
| OBJECT\_TYPE       | VARCHAR(18) | Object type<br/>\* 1: TABLE<br/>\* 2: VIEW<br/>\* 3: DYNAMIC\_VIEW<br/>\* 4: INDEX<br/>\* 5: SEQUENCE<br/>\* 6: AC<br/>\* 7: TABLE PARTITION<br/>\* 8: INDEX PARTITION<br/>\* 9: LOB<br/>\* 10: LOB PARTITION<br/>\* 11: SYNONYM<br/>\* 12: UDF<br/>\* 13: PROCEDURE<br/>\* 14: PACKAGE<br/>\* 15: TRIGGER<br/>\* 16: AC PARTITION<br/>\* 17: AUDIT POLICY<br/>\* 18: JOB<br/>\* 19: PACKAGE BODY<br/>\* 20: TYPE<br/>\* 21: TYPE BODY<br/>\* 22: LIBRARY<br/>\* 23: TABLE SUBPARTITION<br/>\* 24: INDEX SUBPARTITION<br/>\* 25: LOB SUBPARTITION<br/>\* 26: MATERIALIZED VIEW<br/>\* 27: DATABASE LINK<br/>\* 28: OUTLINE<br/>\* 29: FIXED TABLE<br/>\* 30: FIXED VIEW<br/>\* 31: DIRECTORY<br/>\* 32: LBAC POLICY<br/>\* 33: CONTEXT<br/>\* 34: CONTAINER |
| CREATED             | DATE        | Object creation time              
| AFFINITY_INSTANCE  | TINYINT     | Affinity instance of the object |
