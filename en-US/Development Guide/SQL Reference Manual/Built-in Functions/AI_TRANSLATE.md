```ebnf
ai_translate = AI_TRANSLATE "(" model_id "," plain_text "," target_language ")".
```

The AI_TRANSLATE function invokes the specified LLM model to translate the input text into the target language and returns the translated text.

Common use cases:

- Cross-language content translation
- Internationalization text processing
- Multi-language document conversion
- Cross-border e-commerce product description translation

**model_id**

The registered AI model ID, obtained from the MODEL_ID field in the DBA_AI_MODELS view, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL.

**plain_text**

The text to be translated, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL. Maximum length is 65534 bytes.

**target_language**

The target language, is a [general expression](../General SQL Syntax/expr). Its value must be of VARCHAR type and cannot be NULL. For example: "English", "Japanese", "French", etc.

Examples

```sql
-- Invoke LLM model to translate text to English
SELECT AI_TRANSLATE('my_llm_model', 'Hello World', 'English') FROM DUAL;

-- Invoke LLM model to translate text to Japanese
SELECT AI_TRANSLATE('my_llm_model', 'Database is a system for storing data', 'Japanese') FROM DUAL;

-- Error when target_language is NULL
SELECT AI_TRANSLATE('my_llm_model', 'some text', NULL) FROM DUAL;
-- ERROR: target language must not be NULL

-- Error when plain_text is NULL
SELECT AI_TRANSLATE('my_llm_model', NULL, 'English') FROM DUAL;
-- ERROR: plain text must not be NULL
```