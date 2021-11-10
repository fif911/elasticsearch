#

https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html

Difference between Query and Filter
If relevance should be calculated - Query
If just boolean match or do not match - Filter

Took - number in millis how much time query took to execute
_shard - total number of shards that were searched


hits
by default first 10 docs returned

Understanding relevance scoring
2 algorithms:
TF/IDF - old algorithm
BM25 has Nonlinear term frequency saturation so it's better at handling stop words

Relevance depends on:
Term frequency ( how many times word is repeated in field)
Inverse document frequency ( how many times the term appear within the index)
Field Length Norm



You can get explanation why doc matched or not with _explain API or body param
GET /products/_search
{
  "explain": true,
  "query": {
    "term": {
      "name": "lobster"
    }
  }
}

GET /products/_doc/1/_explain
{
  "query": {
    "term": {
      "name": "lobster"
    }
  }
}
```

