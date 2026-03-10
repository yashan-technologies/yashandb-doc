The DatabaseMetaData interface provides comprehensive information related to databases. Users can obtain and process information about the underlying DBMS through this interface.

Some methods of DatabaseMetaData return a list of information in the form of ResultSet objects, from which data can be retrieved using common ResultSet methods (such as getString and getInt).

If the requested form of metadata is not available, the interface returns an empty ResultSet.

The returned columns can be defined as other columns outside of the ResultSet object, but they must be defined through the JDBC driver program and must be accessed via their column labels.

The YashanDB JDBC driver has supported the functionality of the DatabaseMetaData interface:

> **Note**: 
>
> The following methods in the table are not applicable for ISC Distributed Cluster Deployment:
>- getProcedures​(String catalog, String schemaPattern, String procedureNamePattern)
>- getProcedureColumns​(String catalog, String schemaPattern, String procedureNamePattern, String columnNamePattern)

|Return Type |Method |
| --- | --- |
| boolean | allProceduresAreCallable() |
| boolean | allTablesAreSelectable() |
| boolean | autoCommitFailureClosesAllResultSets() |
| boolean | dataDefinitionCausesTransactionCommit() |
| boolean | dataDefinitionIgnoredInTransactions() |
| boolean | deletesAreDetected​(int type) |
| boolean | doesMaxRowSizeIncludeBlobs() |
| boolean | generatedKeyAlwaysReturned() |
| ResultSet | getColumns​(String catalog, String schemaPattern, String tableNamePattern, String columnNamePattern) |
| Connection | getConnection() |
| int | getDatabaseMajorVersion() |
| int | getDatabaseMinorVersion() |
| String | getDatabaseProductName() |
| String | getDatabaseProductVersion() |
| int | getDefaultTransactionIsolation() |
| int | getDriverMajorVersion() |
| int | getDriverMinorVersion() |
| String | getDriverName() |
| String | getDriverVersion() |
| String | getExtraNameCharacters() |
| ResultSet | getFunctions​(String catalog, String schemaPattern, String functionNamePattern) |
| String | getIdentifierQuoteString() |
| ResultSet | getIndexInfo​(String catalog, String schema, String table, boolean unique, boolean approximate) |
| int | getJDBCMajorVersion() |
| int | getJDBCMinorVersion() |
| int | getMaxBinaryLiteralLength() |
| int | getMaxCatalogNameLength() |
| int | getMaxCharLiteralLength() |
| int | getMaxColumnNameLength() |
| int | getMaxColumnsInGroupBy() |
| int | getMaxColumnsInIndex() |
| int | getMaxColumnsInOrderBy() |
| int | getMaxColumnsInSelect() |
| int | getMaxColumnsInTable() |
| int | getMaxConnections() |
| int | getMaxCursorNameLength() |
| int | getMaxIndexLength() |
| long | getMaxLogicalLobSize() |
| int | getMaxProcedureNameLength() |
| int | getMaxRowSize() |
| int | getMaxSchemaNameLength() |
| int | getMaxStatementLength() |
| int | getMaxStatements() |
| int | getMaxTableNameLength() |
| int | getMaxTablesInSelect() |
| int | getMaxUserNameLength() |
| String | getNumericFunctions() |
| ResultSet | getPrimaryKeys​(String catalog, String schema, String table) |
| ResultSet | getProcedures​(String catalog, String schemaPattern, String procedureNamePattern) |
| String | getProcedureTerm() |
| int | getResultSetHoldability() |
| RowIdLifetime | getRowIdLifetime() |
| ResultSet | getSchemas() |
| ResultSet | getSchemas​(String catalog, String schemaPattern) |
| String | getSchemaTerm() |
| String | getSearchStringEscape() |
| String | getSQLKeywords() |
| int | getSQLStateType() |
| String | getStringFunctions() |
| String | getSystemFunctions() |
| ResultSet | getTables​(String catalog, String schemaPattern, String tableNamePattern, String\[\] types) |
| ResultSet | getTableTypes() |
| String | getTimeDateFunctions() |
| ResultSet | getTypeInfo() |
| String | getURL() |
| String | getUserName() |
| boolean | insertsAreDetected​(int type) |
| boolean | isReadOnly() |
| boolean | locatorsUpdateCopy() |
| boolean | nullPlusNonNullIsNull() |
| boolean | nullsAreSortedAtEnd() |
| boolean | nullsAreSortedAtStart() |
| boolean | nullsAreSortedHigh() |
| boolean | nullsAreSortedLow() |
| boolean | othersDeletesAreVisible​(int type) |
| boolean | othersInsertsAreVisible​(int type) |
| boolean | othersUpdatesAreVisible​(int type) |
| boolean | ownDeletesAreVisible​(int type) |
| boolean | ownInsertsAreVisible​(int type) |
| boolean | ownUpdatesAreVisible​(int type) |
| boolean | storesLowerCaseIdentifiers() |
| boolean | storesLowerCaseQuotedIdentifiers() |
| boolean | storesMixedCaseIdentifiers() |
| boolean | storesMixedCaseQuotedIdentifiers() |
| boolean | storesUpperCaseIdentifiers() |
| boolean | storesUpperCaseQuotedIdentifiers() |
| boolean | supportsAlterTableWithAddColumn() |
| boolean | supportsAlterTableWithDropColumn() |
| boolean | supportsANSI92EntryLevelSQL() |
| boolean | supportsANSI92FullSQL() |
| boolean | supportsANSI92IntermediateSQL() |
| boolean | supportsBatchUpdates() |
| boolean | supportsColumnAliasing() |
| boolean | supportsConvert() |
| boolean | supportsConvert​(int fromType, int toType) |
| boolean | supportsCoreSQLGrammar() |
| boolean | supportsCorrelatedSubqueries() |
| boolean | supportsDataDefinitionAndDataManipulationTransactions() |
| boolean | supportsDataManipulationTransactionsOnly() |
| boolean | supportsDifferentTableCorrelationNames() |
| boolean | supportsExpressionsInOrderBy() |
| boolean | supportsExtendedSQLGrammar() |
| boolean | supportsFullOuterJoins() |
| boolean | supportsGetGeneratedKeys() |
| boolean | supportsGroupBy() |
| boolean | supportsGroupByBeyondSelect() |
| boolean | supportsGroupByUnrelated() |
| boolean | supportsIntegrityEnhancementFacility() |
| boolean | supportsLikeEscapeClause() |
| boolean | supportsLimitedOuterJoins() |
| boolean | supportsMinimumSQLGrammar() |
| boolean | supportsMixedCaseIdentifiers() |
| boolean | supportsMixedCaseQuotedIdentifiers() |
| boolean | supportsMultipleOpenResults() |
| boolean | supportsMultipleResultSets() |
| boolean | supportsMultipleTransactions() |
| boolean | supportsNamedParameters() |
| boolean | supportsNonNullableColumns() |
| boolean | supportsOpenCursorsAcrossCommit() |
| boolean | supportsOpenCursorsAcrossRollback() |
| boolean | supportsOpenStatementsAcrossCommit() |
| boolean | supportsOpenStatementsAcrossRollback() |
| boolean | supportsOrderByUnrelated() |
| boolean | supportsOuterJoins() |
| boolean | supportsPositionedDelete() |
| boolean | supportsPositionedUpdate() |
| boolean | supportsRefCursors() |
| boolean | supportsResultSetConcurrency​(int type, int concurrency) |
| boolean | supportsResultSetHoldability​(int holdability) |
| boolean | supportsResultSetType​(int type) |
| boolean | supportsSavepoints() |
| boolean | supportsSchemasInDataManipulation() |
| boolean | supportsSchemasInIndexDefinitions() |
| boolean | supportsSchemasInPrivilegeDefinitions() |
| boolean | supportsSchemasInProcedureCalls() |
| boolean | supportsSchemasInTableDefinitions() |
| boolean | supportsSelectForUpdate() |
| boolean | supportsStatementPooling() |
| boolean | supportsStoredFunctionsUsingCallSyntax() |
| boolean | supportsStoredProcedures() |
| boolean | supportsSubqueriesInComparisons() |
| boolean | supportsSubqueriesInExists() |
| boolean | supportsSubqueriesInIns() |
| boolean | supportsSubqueriesInQuantifieds() |
| boolean | supportsTableCorrelationNames() |
| boolean | supportsTransactionIsolationLevel​(int level) |
| boolean | supportsTransactions() |
| boolean | supportsUnion() |
| boolean | supportsUnionAll() |
| boolean | updatesAreDetected​(int type) |
| boolean | usesLocalFilePerTable() |
| boolean | usesLocalFiles() |
| ResultSet | getBestRowIdentifier​(String catalog, String schema, String table, int scope, boolean nullable) |
| ResultSet | getCrossReference​(String parentCatalog, String parentSchema, String parentTable, String foreignCatalog, String foreignSchema, String foreignTable) |
| ResultSet | getExportedKeys​(String catalog, String schema, String table) |
| ResultSet | getFunctionColumns​(String catalog, String schemaPattern, String functionNamePattern, String columnNamePattern) |
| ResultSet | getImportedKeys​(String catalog, String schema, String table) |
| ResultSet | getProcedureColumns​(String catalog, String schemaPattern, String procedureNamePattern, String columnNamePattern) |
| ResultSet | getPseudoColumns​(String catalog, String schemaPattern, String tableNamePattern, String columnNamePattern) |
| ResultSet | getTablePrivileges​(String catalog, String schemaPattern, String tableNamePattern) |
| ResultSet | getVersionColumns​(String catalog, String schema, String table) |
| ResultSet | getUDTs​(String catalog, String schemaPattern, String typeNamePattern, int\[\] types) |
| ResultSet | getSuperTypes​(String catalog, String schemaPattern, String typeNamePattern) |

Unsupported functionality:

|Return Type |Method |
| --- | --- |
| ResultSet | getAttributes​(String catalog, String schemaPattern, String typeNamePattern, String attributeNamePattern) |
| ResultSet | getClientInfoProperties() |
| ResultSet | getColumnPrivileges​(String catalog, String schema, String table, String columnNamePattern) |
| String | getCatalogTerm() |
| boolean | isCatalogAtStart() |
| String | getCatalogSeparator() |
| boolean | supportsCatalogsInDataManipulation() |
| boolean | supportsCatalogsInIndexDefinitions() |
| boolean | supportsCatalogsInPrivilegeDefinitions() |
| boolean | supportsCatalogsInProcedureCalls() |
| boolean | supportsCatalogsInTableDefinitions() |
| ResultSet | getCatalogs() |
| ResultSet | getSuperTables​(String catalog, String schemaPattern, String tableNamePattern) |
