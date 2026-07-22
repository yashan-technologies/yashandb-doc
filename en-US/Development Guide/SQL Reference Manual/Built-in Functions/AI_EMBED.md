```ebnf
ai_embed = AI_EMBED "(" model_id "," text ["," dimensions] ")".
```

The AI_EMBED function invokes the specified embedding model to convert text into vector data and returns a VECTOR type result.

**model_id**

The registered AI model ID, obtained from the MODEL_ID field in the DBA_AI_MODELS view, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL.

**text**

The text to be embedded, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL. Maximum length is 65534 bytes.

**dimensions**

Optional parameter, specifies the desired vector dimensions, is a [general expression](../General SQL Syntax/expr). Its value must be of INT type. This parameter is currently only used for validation and has no practical effect.

When dimensions is NULL, the function uses the model's default vector dimensions.

Examples

```sql
-- Invoke embedding model to convert text to vector
SELECT AI_EMBED('my_embed_model', 'YashanDB is a database') FROM DUAL;

-- Specify vector dimensions
SELECT AI_EMBED('my_embed_model', 'Hello world', 768) FROM DUAL;

-- Error when model_id or text is NULL
SELECT AI_EMBED(NULL, 'test') FROM DUAL;
-- ERROR: model_id must not be NULL
```