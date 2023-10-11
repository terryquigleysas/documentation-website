---
layout: default
title: Neural sparse
parent: Specialized queries
grand_parent: Query DSL
nav_order: 55
---

# Neural sparse query
Introduced 2.11
{: .label .label-purple }

Use the `neural_sparse` query for vector field search in [sparse neural search]({{site.url}}{{site.baseurl}}/search-plugins/neural-sparse-search/). 

## Request fields

Include the following request fields in the `neural_sparse` query:

```json
"neural_sparse": {
  "<vector_field>": {
    "query_text": "<query_text>",
    "model_id": "<model_id>",
    "max_token_score": "<max_token_score>"
  }
}
```

The top-level `vector_field` specifies the vector field against which to run a search query. The following table lists the other `neural_sparse` query fields.

Field | Data type | Required/Optional | Description
:--- | :--- | :--- 
`query_text` | String | Optional | The query text from which to generate vector embeddings. You must specify at least one `query_text` or `query_image`.
`model_id` | String | Required | The ID of the sparse encoding model that will be used to generate vector embeddings from the query text. The model must be indexed in OpenSearch before it can be used in sparse neural search. For more information, see [Using custom models within OpenSearch]({{site.url}}{{site.baseurl}}/ml-commons-plugin/ml-framework/) and [Semantic search]({{site.url}}{{site.baseurl}}/ml-commons-plugin/semantic-search/).
`max_token_score` | Float | Optional | The theoretical upper bound of the score for all tokens in the vocabulary (required for performance optimization).

#### Example request

```json
GET my-nlp-index/_search
{
  "neural_sparse": {
    "passage_embedding": {
      "query_text": "Hi world",
      "model_id": "aP2Q8ooBpBj3wT4HVS8a",
      "max_token_score": 2
    }
  }
}
```
{% include copy-curl.html %}