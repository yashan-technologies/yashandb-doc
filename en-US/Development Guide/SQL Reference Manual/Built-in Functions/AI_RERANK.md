```ebnf
ai_rerank = AI_RERANK "(" model_id "," query "," documents ")".
```

The AI_RERANK function invokes the specified rerank model to score and rank a set of documents by relevance, and returns a result in JSON format.

**model_id**

The registered AI model ID, obtained from the MODEL_ID field in the DBA_AI_MODELS view, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL.

**query**

The query text, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL. Maximum length is 65534 bytes.

**documents**

The list of documents to be ranked, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type, in JSON array string format. For example: `'["doc1", "doc2", "doc3"]'`. Cannot be NULL.

The return value is in JSON array format, with each element containing the following fields:

| Field Name | Type | Description |
|------------|------|-------------|
| index | INT | The original index of the document in the input array |
| score | FLOAT | Relevance score |
| document.text | VARCHAR | Document content |

Examples

```sql
-- Rank documents by relevance
SELECT AI_RERANK('my_rerank_model',
                  'Database system',
                  '["MySQL is a database","Python is a language","Oracle is a database"]') FROM DUAL;

-- Error when model_id, query, or documents is NULL
SELECT AI_RERANK(NULL, 'test', '["doc1"]') FROM DUAL;
-- ERROR: model_id must not be NULL
```