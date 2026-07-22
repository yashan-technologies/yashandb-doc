```ebnf
ai_summarize = AI_SUMMARIZE "(" model_id "," plain_text ")".
```

The AI_SUMMARIZE function invokes the specified LLM model to summarize the input text and returns a condensed summary of the text.

Common use cases:

- Extracting key information from long articles, reports, and documents
- Generating news summaries and meeting minutes
- Quickly understanding the core content of long texts
- Data noise reduction, filtering important information from large amounts of text

**model_id**

The registered AI model ID, obtained from the MODEL_ID field in the DBA_AI_MODELS view, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL.

**plain_text**

The text to be summarized, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL. Maximum length is 65534 bytes.

Examples

```sql
-- Invoke LLM model to summarize text
SELECT AI_SUMMARIZE('my_llm_model', 'This is a detailed article about AI development...') FROM DUAL;

-- Error when plain_text is NULL
SELECT AI_SUMMARIZE('my_llm_model', NULL) FROM DUAL;
-- ERROR: plain text must not be NULL

-- Error when model_id is NULL
SELECT AI_SUMMARIZE(NULL, 'some text') FROM DUAL;
-- ERROR: model_id must not be NULL
```