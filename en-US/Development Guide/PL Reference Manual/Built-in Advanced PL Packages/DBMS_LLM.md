The DBMS_LLM package provides a set of built-in functions and procedures for managing and using AI models. Through the DBMS_LLM package, users can create, delete, and modify AI models, and invoke models through built-in functions such as AI_EMBED, AI_COMPLETE, and AI_RERANK.

## CREATE_MODEL

```plsql
DBMS_LLM.CREATE_MODEL (
   model_id    IN VARCHAR,
   base_url    IN VARCHAR,
   model_name  IN VARCHAR,
   model_type  IN VARCHAR,
   protocol    IN VARCHAR,
   api_key     IN VARCHAR DEFAULT 'NULL',
   attributes  IN JSON DEFAULT 'NULL');
```

The CREATE_MODEL procedure is used to register a new AI model into the data dictionary.

> **Note**:
>
> All parameters must not contain non-ASCII characters.

| Parameter | Description |
| :---- | :---- |
| model_id | Model ID, used to uniquely identify an AI model. Cannot be NULL. |
| base_url | Base URL of the model service. Cannot be NULL. |
| model_name | Model name, used in API requests to identify the actual model being called. Cannot be NULL. |
| model_type | Model type, must be one of 'embedding', 'rerank', or 'llm'. Cannot be NULL. |
| protocol | Protocol type, currently only supports 'openai'. Cannot be NULL. |
| api_key | API key for Bearer Token authentication. Defaults to 'NULL', meaning no authentication. |
| attributes | Model attributes in JSON format. Defaults to 'NULL'. |

Examples

```plsql
-- Register an embedding model
EXEC DBMS_LLM.CREATE_MODEL('my_embed_model', 'http://localhost:8080', 'text-embedding-ada-002', 'embedding', 'openai');

-- Register an LLM model (with API key)
EXEC DBMS_LLM.CREATE_MODEL('my_llm_model', 'http://localhost:8080', 'gpt-4', 'llm', 'openai', 'sk-xxx', JSON('{"temperature":0.7,"max_tokens":1000}'));

-- Register a rerank model
EXEC DBMS_LLM.CREATE_MODEL('my_rerank_model', 'http://localhost:8080', 'rerank-model', 'rerank', 'openai', 'NULL');
```

## ALTER_MODEL

```plsql
DBMS_LLM.ALTER_MODEL (
   model_id    IN VARCHAR,
   attributes  IN JSON DEFAULT 'NULL');
```

The ALTER_MODEL procedure is used to modify the attributes of a registered AI model. After successful modification, the model cache will be automatically invalidated and reloaded on the next invocation.

| Parameter | Description |
| :---- | :---- |
| model_id | ID of the model to be modified. Cannot be NULL. |
| attributes | New model attributes in JSON format. Defaults to 'NULL', meaning no modification. |

Examples (for Standalone/YAC/Distributed Cluster Deployment)

```plsql
-- Modify model attributes
EXEC DBMS_LLM.ALTER_MODEL('my_llm_model', '{"temperature":0.7,"max_tokens":200}');
```

## DROP_MODEL

```plsql
DBMS_LLM.DROP_MODEL (
   model_id    IN VARCHAR);
```

The DROP_MODEL procedure is used to delete a registered AI model from the data dictionary. After successful deletion, the model cache will be automatically invalidated.

| Parameter | Description |
| :---- | :---- |
| model_id | ID of the model to be deleted. Cannot be NULL. |

Examples

```plsql
-- Delete a model
EXEC DBMS_LLM.DROP_MODEL('my_embed_model');
```