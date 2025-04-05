# Elasticsearch help notes
## Elasticsearch status
```
GET _cat/indices

GET _stats

GET log-18.04.2018/_mapping

GET _cat/health

GET _cluster/health

GET _aliases
```

## Working with log 
```
GET log-18.04.2018/_search
{
  "query": {
    "match_all": {}
  }
}
```

```
GET log-18.04.2018/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "@timestamp": {
              "gte": "2018-04-18",
              "lte": "2018-04-18"
            }
          }
        },
        {
          "query_string": {
            "default_field": "message",
            "query": "*expir*"
          }
        }
      ]
    }
  },
  "sort": [
    {
      "@timestamp": {
        "order": "desc"
      }
    }
  ], 
  "size": 1,
  "aggs": {
    "group_by_tenantId": {
      "terms": {
        "field": "fields.LogEntry.TenantId.keyword"
      }
    }
  }
}
```

```
GET log-18.04.2018/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "@timestamp": {
              "gte": "2017-04-19",
              "lte": "2017-04-19"
            }
          }
        },
        {
          "match_phrase": {
              "message": "User account updated"
          }          
        }
      ]
    }
  }
}
```

