```ebnf
ai_complete = AI_COMPLETE "(" model_id "," prompt ["," parameters] ")".
```
```ebnf
ai_generate = AI_GENERATE "(" model_id "," prompt ["," parameters] ")".
```

The AI_COMPLETE function invokes the specified LLM model for dialogue completion and returns a VARCHAR type result.

The AI_GENERATE function has the same meaning as AI_COMPLETE.

**model_id**

The registered AI model ID, obtained from the MODEL_ID field in the DBA_AI_MODELS view, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL.

**prompt**

The user input prompt text, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL. Maximum length is 65534 bytes.

**parameters**

Optional parameter, specifies additional parameters for model invocation (such as temperature, max_tokens, etc.), is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type in JSON format.

- When parameters contains multiple parameters, they are separated by commas and together form members of a JSON object. For example: `"temperature":0.7,"max_tokens":100`.
- When parameters is NULL or omitted, the function uses the default temperature and max_tokens values configured when the model was registered.
- If no default values were configured during model registration, the API is called without additional parameters.

Examples

```sql
-- Basic invocation
SELECT AI_COMPLETE('my_llm_model', 'Translate to English: Hello') FROM DUAL;

-- Specify additional parameters
SELECT AI_COMPLETE('my_llm_model', 'Write a poem about database', '"temperature":0.8,"max_tokens":200') FROM DUAL;

-- Error when model_id or prompt is NULL
SELECT AI_COMPLETE(NULL, 'test') FROM DUAL;
-- ERROR: model_id must not be NULL
```