# Mapping

We can set mapping and get it for object or field Mapping is like serializer or schema

All fields in mapping are optional by default
(searches automatically handle missing fields)

## Add field mappings for `reviews` index

```
PUT /reviews
{
  "mappings": {
    "properties": {
      "rating": { "type": "float" },
      "content": { "type": "text" },
      "product_id": { "type": "integer" },
      "author": {
        "properties": {
          "first_name": { "type": "text" },
          "last_name": { "type": "text" },
          "email": { "type": "keyword" }
        }
      }
    }
  }
}
```

# Overview of mapping params

https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-params.html
https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-date-format.html
Some list of important params

FORMAT - used to specify format for date fields
"strict_date_optional_time||epoch_millis"
can be customized for format which are supported by Java's DateFormatter syntax /dd/MM/yyyy using built-in formats "
epoch_seconds"

```
"date": {
"type":   "date",
"format": "yyyy-MM-dd"
}
```

PROPERTIES - defines nested fields for object and nested fields

```json
{
  "sold_by": {
    "properties": {
      "name": {
        "type": "text"
      }
    }
  }
}

{
  "products": {
    "type": "nested",
    "properties": {
      "name": {
        "type": "text"
      }
    }
  }
}
```

COERCE - disables or enables coercion of values (enabled by default)

https://www.elastic.co/guide/en/elasticsearch/reference/current/coerce.html

(e.g. if enabled for integer field it will convert "10" value to 10 and index it (for search index)
BUT for _source obj the value still will be )

```

PUT my-index-000001
{
  "mappings": {
    "properties": {
      "number_one": {
        "type": "integer"
      },
      "number_two": {
        "type": "integer",
        "coerce": false
      }
    }
  }
}

PUT my-index-000001/_doc/1 The number_one field will contain the integer 10.
{
  "number_one": "10" 
}

PUT my-index-000001/_doc/2 This document will be rejected because coercion is disabled.
{
  "number_two": "10" 
}

---

PUT my-index-000001
{
  "settings": {
    "index.mapping.coerce": false
  },
  "mappings": {
    "properties": {
      "number_one": {
        "type": "integer",
        "coerce": true
      },
      "number_two": {
        "type": "integer"
      }
    }
  }
}

PUT my-index-000001/_doc/1 The number_one field overrides the index level setting to enable coercion.
{ "number_one": "10" } 

PUT my-index-000001/_doc/2 This document will be rejected because the number_two field inherits the index-level coercion setting.
{ "number_two": "10" } 
```

DOC_VALUES - additional data strcuture. Univerted index used for sorting aggregations and scripting. Elastic depending
on the query will query the needed data structure

- Set false to optimize disk space
- increase indexing speed
- Only disable doc values if you won't use aggregations sorting or scripting
- cannot be changed without reindexing every doc into new index

NORMS

Normalization actors used for relevance scoring. Often we dont just want to filter result but also rank them by
relevance

- Norms can be disabled to save disk space
- useful for fields that wont be used for relevance scoring

INDEX

- Disables indexing for a fields BUt still stores it into _source
- Usefull if you wont use a field for search queries
- often used for time series data
- fields with indexing disabled can be still used for aggregations

NULL_VALUES

NULL values are ignored in EL and cannot be indexed or searched
(Same applies for empty array)

Use this param to replace NULL values with another value

COPY_TO - Used to copy multiple field values into a "group field"

Some important points:

- It is the field value which is copied, not the terms (which result from the analysis process).
- The original _source field will not be modified to show the copied values.
- The same value can be copied to multiple fields, with "copy_to": [ "field_1", "field_2" ]
- You cannot copy recursively via intermediary fields such as a copy_to on field_1 to field_2 and copy_to on field_2 to
  field_3 expecting indexing into field_1 will eventuate in field_3, instead use copy_to directly to multiple fields
  from the originating field.

For example, the first_name and last_name fields can be copied to the full_name field as follows:

- The values of the first_name and last_name fields are copied to the full_name field.
- The first_name and last_name fields can still be queried for the first name and last name respectively, but the
  full_name field can be queried for both first and last names.

```json
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "first_name": {
        "type": "text",
        "copy_to": "full_name"
      },
      "last_name": {
        "type": "text",
        "copy_to": "full_name"
      },
      "full_name": {
        "type": "text"
      }
    }
  }
}

PUT my-index-000001/_doc/1
{
  "first_name": "John",
  "last_name": "Smith"
}

GET my-index-000001/_search
{
  "query": {
    "match": {
      "full_name": {
        "query": "John Smith",
        "operator": "and"
      }
    }
  }
}
```

# Dynamic mapping

The first time elastic eacounters the field - it automatically create a mapping for it

Dynamic Mapping can be set to strict and then we will not allow other than already mapped fields 

Numeric detection boolean - whether elastic need to try convert string to int when creating mapping

numeric_detection true by default

date_detection true by default