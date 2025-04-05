# Elasticsearch help notes
## Deleting indices
```
    DELETE ga

    DELETE dka

    DELETE test-dka

    DELETE test-dka-v4

    DELETE test-dka-v5

    DELETE test-dka-v6

    DELETE test-dka-v7

    DELETE test-dka-v8
```

## Elasticsearch status
```
    GET _cat/indices

    GET _stats

    GET _mapping

    GET _cat/health

    GET _cluster/health

    GET _aliases
```

## Working with test-dka alias 
```
GET test-dka/_mapping

GET test-dka/_settings

GET test-dka/_search
{
  "size": 1000, 
  "query": {
    "match_all": {}
  }
}
```

### Overview query aggregation by parent
```
GET test-dka/projects/_search
{
  "query": 
  {
    "bool": {
      "filter": [
        {
          "terms": {
            "customerId": [
              1122
            ]
          }
        },
        {
          "terms": {
            "departmentId": [
              18957,
              18967,
              18985              
            ]
          }
        },
        {
          "has_child": {
            "type": "projectsDetails",
            "filter": [
              {
                "range": {
                  "date": {
                    "gte": "2010-01-01",
                    "lte": "2016-12-01"
                  }
                }
              }
            ]
          }
        }
      ] 
    }
  },
  "size": 0,
  "aggs": {
    "summary": {
      "terms": {
        "field": "customerId",
        "size": 100000
      },
      "aggs": {
        "number of projects": {
          "value_count": {
            "field": "projectId"
          }
        },        
        "summary per customer": {
          "children": {
            "type": "projectsDetails"
          },
          "aggs": {
            "filtered summary per customer": {
              "filter": {
                "range": {
                  "date": {
                    "gte": "2010-01-01",
                    "lte": "2016-12-01"
                  }
                }
              },
              "aggs": {
                "Total number of job views": {
                  "sum": {
                    "field": "numberOfUsers"
                  }
                },
                "Total number of applications": {
                  "sum": {
                    "field": "numberOfApplicants"
                  }
                },
                "Total number of interviews": {
                  "sum": {
                    "field": "numberOfInterviews"
                  }
                },
                "Total number of hires": {
                  "sum": {
                    "field": "numberOfHires"
                  }
                },
                "Job views turning into an application": {
                  "scripted_metric": {
                    "init_script": "_agg['jobViews'] = []; _agg['applicants'] = [];",
                    "map_script": "_agg['jobViews'].add(doc['numberOfUsers'].value); _agg['applicants'].add(doc['numberOfApplicants'].value);",
                    "combine_script": "result = [0, 0]; for (t in _agg['jobViews']) { result[0] += t }; for (t in _agg['applicants']) { result[1] += t }; return result;",
                    "reduce_script": "totalViews = 0; totalApplicants = 0; for (a in _aggs) { totalViews += a[0]; totalApplicants += a[1]; }; if (totalViews > 0) { return totalApplicants / totalViews * 100.0; } else { return 0; }"
                  }
                },
                "Applications turning into an employment": {
                  "scripted_metric": {
                    "init_script": "_agg['applicants'] = []; _agg['hired'] = [];",
                    "map_script": "_agg['applicants'].add(doc['numberOfApplicants'].value); _agg['hired'].add(doc['numberOfHires'].value);",
                    "combine_script": "result = [0, 0]; for (t in _agg['applicants']) { result[0] += t }; for (t in _agg['hired']) { result[1] += t }; return result;",
                    "reduce_script": "totalApplicants = 0; totalHired = 0; for (a in _aggs) { totalApplicants += a[0]; totalHired += a[1]; }; if (totalApplicants > 0) { return totalHired / totalApplicants * 100.0; } else { return 0; }"
                  }              
                },
                "Interviews turning into an employment": {
                  "scripted_metric": {
                    "init_script": "_agg['interviewed'] = []; _agg['hired'] = [];",
                    "map_script": "_agg['interviewed'].add(doc['numberOfInterviews'].value); _agg['hired'].add(doc['numberOfHires'].value);",
                    "combine_script": "result = [0, 0]; for (t in _agg['interviewed']) { result[0] += t }; for (t in _agg['hired']) { result[1] += t }; return result;",
                    "reduce_script": "totalInterviewed = 0; totalHired = 0; for (a in _aggs) { totalInterviewed += a[0]; totalHired += a[1]; }; if (totalInterviewed > 0) { return totalHired / totalInterviewed * 100.0; } else { return 0; }"
                  }              
                }
              }              
            }
          } 
        }
      }
    }
  }
}
```

### Overview query aggregation by child
```
GET test-dka/projects/_search
{
  "query": 
  {
    "bool": {
      "filter": [
        {
          "terms": {
            "customerId": [
              1122
            ]
          }
        },
        {
          "terms": {
            "departmentId": [
              18957,
              18967,
              18985              
            ]
          }
        },
        {
          "has_child": {
            "type": "projectsDetails",
            "filter": [
              {
                "range": {
                  "date": {
                    "gte": "2010-01-01",
                    "lte": "2016-12-01"
                  }
                }
              },
              {
                
              },
              {
                
              },
              {
                
              }
            ]
          }
        }
      ] 
    }
  },
  "from": 0, 
  "size": 0,
  "aggs": {
    "summary per customer": {
      "children": {
        "type": "projectsDetails"
      },
      "aggs": {
        "filtered summary per customer": {
          "filter": {
            "range": {
              "date": {
                "gte": "2010-01-01",
                "lte": "2016-12-01"
              }
            }
          },
          "aggs": {
            "terms aggs": {
              "terms": {
                "field": "country",
                "size": 100000
              },
              "aggs": {
                "Total number of job views": {
                  "sum": {
                    "field": "numberOfUsers"
                  }
                },
                "Total number of applications": {
                  "sum": {
                    "field": "numberOfApplicants"
                  }
                },
                "Total number of interviews": {
                  "sum": {
                    "field": "numberOfInterviews"
                  }
                },
                "Total number of hires": {
                  "sum": {
                    "field": "numberOfHires"
                  }
                },
                "Job views turning into an application": {
                  "scripted_metric": {
                    "init_script": "_agg['jobViews'] = []; _agg['applicants'] = [];",
                    "map_script": "_agg['jobViews'].add(doc['numberOfUsers'].value); _agg['applicants'].add(doc['numberOfApplicants'].value);",
                    "combine_script": "result = [0, 0]; for (t in _agg['jobViews']) { result[0] += t }; for (t in _agg['applicants']) { result[1] += t }; return result;",
                    "reduce_script": "totalViews = 0; totalApplicants = 0; for (a in _aggs) { totalViews += a[0]; totalApplicants += a[1]; }; if (totalViews > 0) { return totalApplicants / totalViews * 100.0; } else { return 0; }"
                  }
                },
                "Applications turning into an employment": {
                  "scripted_metric": {
                    "init_script": "_agg['applicants'] = []; _agg['hired'] = [];",
                    "map_script": "_agg['applicants'].add(doc['numberOfApplicants'].value); _agg['hired'].add(doc['numberOfHires'].value);",
                    "combine_script": "result = [0, 0]; for (t in _agg['applicants']) { result[0] += t }; for (t in _agg['hired']) { result[1] += t }; return result;",
                    "reduce_script": "totalApplicants = 0; totalHired = 0; for (a in _aggs) { totalApplicants += a[0]; totalHired += a[1]; }; if (totalApplicants > 0) { return totalHired / totalApplicants * 100.0; } else { return 0; }"
                  }              
                },
                "Interviews turning into an employment": {
                  "scripted_metric": {
                    "init_script": "_agg['interviewed'] = []; _agg['hired'] = [];",
                    "map_script": "_agg['interviewed'].add(doc['numberOfInterviews'].value); _agg['hired'].add(doc['numberOfHires'].value);",
                    "combine_script": "result = [0, 0]; for (t in _agg['interviewed']) { result[0] += t }; for (t in _agg['hired']) { result[1] += t }; return result;",
                    "reduce_script": "totalInterviewed = 0; totalHired = 0; for (a in _aggs) { totalInterviewed += a[0]; totalHired += a[1]; }; if (totalInterviewed > 0) { return totalHired / totalInterviewed * 100.0; } else { return 0; }"
                  }              
                }
              }              
            }
          } 
        }
      } 
    }
  }
}
```

### Overview details query
```
GET test-dka/projects/_search
{
  "query": 
  {
    "bool": {
      "filter": [
        {
          "terms": {
            "customerId": [
              1122
            ]
          }
        },
        {
          "terms": {
            "departmentId": [
              18957,
              18967,
              18985              
            ]
          }
        },
        {
          "terms": {
            "projectCategory": [
              "seller"
            ]
          }          
        },
        {
          "has_child": {
            "type": "projectsDetails",
            "filter": [
              {
                "range": {
                  "date": {
                    "gte": "2010-01-01",
                    "lte": "2016-12-01"
                  }
                }
              }
            ]
          }
        }
      ] 
    }
  },
  "from": 0, 
  "size": 0,
  "aggs": {
    "agg1": {
      "children": {
        "type": "projectsDetails"
      },
      "aggs": {
        "agg2": {
          "filter": {
            "range": {
              "date": {
                "gte": "2010-01-01",
                "lte": "2016-12-01"
              }
            }
          },
          "aggs": {
            "men (visitors)": {
              "scripted_metric": {
                "init_script": "_agg['men'] = [];",
                "map_script": "if (doc['gender'].value.startsWith('m')) { _agg['men'].add(doc['numberOfUsers'].value); };",
                "combine_script": "result = 0; for (t in _agg['men']) { result += t; }; return result;",
                "reduce_script": "totalMen = 0; for (a in _aggs) { totalMen += a; }; return totalMen;"
              }
            },
            "women (visitors)": {
              "scripted_metric": {
                "init_script": "_agg['women'] = [];",
                "map_script": "if (doc['gender'].value.startsWith('f')) { _agg['women'].add(doc['numberOfUsers'].value); };",
                "combine_script": "result = 0; for (t in _agg['women']) { result += t; }; return result;",
                "reduce_script": "totalWomen = 0; for (a in _aggs) { totalWomen += a; }; return totalWomen;"
              }
            },            
            "average age (visitors)": {
              "scripted_metric": {
                "init_script": "keys = ['[18-24]', '[25-34]', '[35-44]', '[45-54]', '[55-64]', '[65+]']; for (key in keys) { _agg[key] = []; };",
                "map_script": "_agg[doc['ageRange'].value].add(doc['numberOfUsers'].value);",
                "combine_script": "keys = ['[18-24]', '[25-34]', '[35-44]', '[45-54]', '[55-64]', '[65+]']; result = [:]; for (key in keys) { result[key] = 0; }; for (key in keys) { for (value in _agg[key]) { result[key] += value; }; }; return result;",
                "reduce_script": "keys = ['[18-24]', '[25-34]', '[35-44]', '[45-54]', '[55-64]', '[65+]']; result = [:]; result2 = [:]; total = 0; index = 0; meanAgeRange = ''; ageUnit = 10; prevValue = 0; for (key in keys) { result[key] = 0; result2[key] = 0; }; for (key in keys) { for (value in _aggs[key]) { result[key] += value; }; }; for (key in keys) { if (index == 0) { result2[key] = result[keys.getAt(index)]; } else { result2[key] = result[keys.getAt(index)] + result2[keys.getAt(index - 1)]; }; index++; total += result[key]; }; mean = (total + 1)/2.0 as int; index = -1; for (key in keys) { if (result2[key] >= mean) { meanAgeRange = key; break; }; index++; }; if (index > 0) { prevValue = result2[keys.getAt(index)]; }; if (meanAgeRange != '') { delta = ((mean - prevValue) / result[meanAgeRange]) as double; return (meanAgeRange.substring(1,3).toInteger() + (delta * ageUnit)) as int; } else { return 0 };"
              }
            },
            "top popular city (visitors)": {
              "terms": {
                "field": "city",
                "size": 1,
                "order": {
                  "number of visitors": "desc"
                }
              },
              "aggs": {
                "number of visitors": {
                  "sum": {
                    "field": "numberOfUsers"
                  }
                }
              }
            },
            "men (applicants)": {
              "scripted_metric": {
                "init_script": "_agg['men'] = [];",
                "map_script": "if (doc['gender'].value.startsWith('m')) { _agg['men'].add(doc['numberOfApplicants'].value); };",
                "combine_script": "result = 0; for (t in _agg['men']) { result += t; }; return result;",
                "reduce_script": "totalMen = 0; for (a in _aggs) { totalMen += a; }; return totalMen;"
              }
            },
            "women (applicants)": {
              "scripted_metric": {
                "init_script": "_agg['women'] = [];",
                "map_script": "if (doc['gender'].value.startsWith('f')) { _agg['women'].add(doc['numberOfApplicants'].value); };",
                "combine_script": "result = 0; for (t in _agg['women']) { result += t; }; return result;",
                "reduce_script": "totalWomen = 0; for (a in _aggs) { totalWomen += a; }; return totalWomen;"
              }
            },            
            "average age (applicants)": {
              "scripted_metric": {
                "init_script": "keys = ['[18-24]', '[25-34]', '[35-44]', '[45-54]', '[55-64]', '[65+]']; for (key in keys) { _agg[key] = []; };",
                "map_script": "_agg[doc['ageRange'].value].add(doc['numberOfApplicants'].value);",
                "combine_script": "keys = ['[18-24]', '[25-34]', '[35-44]', '[45-54]', '[55-64]', '[65+]']; result = [:]; for (key in keys) { result[key] = 0; }; for (key in keys) { for (value in _agg[key]) { result[key] += value; }; }; return result;",
                "reduce_script": "keys = ['[18-24]', '[25-34]', '[35-44]', '[45-54]', '[55-64]', '[65+]']; result = [:]; result2 = [:]; total = 0; index = 0; meanAgeRange = ''; ageUnit = 10; prevValue = 0; for (key in keys) { result[key] = 0; result2[key] = 0; }; for (key in keys) { for (value in _aggs[key]) { result[key] += value; }; }; for (key in keys) { if (index == 0) { result2[key] = result[keys.getAt(index)]; } else { result2[key] = result[keys.getAt(index)] + result2[keys.getAt(index - 1)]; }; index++; total += result[key]; }; mean = (total + 1)/2.0 as int; index = -1; for (key in keys) { if (result2[key] >= mean) { meanAgeRange = key; break; }; index++; }; if (index > 0) { prevValue = result2[keys.getAt(index)]; }; if (meanAgeRange != '') { delta = ((mean - prevValue) / result[meanAgeRange]) as double; return (meanAgeRange.substring(1,3).toInteger() + (delta * ageUnit)) as int; } else { return 0 };"
              }
            },
            "top popular city (applicants)": {
              "terms": {
                "field": "city",
                "size": 1,
                "order": {
                  "number of visitors": "desc"
                }
              },
              "aggs": {
                "number of visitors": {
                  "sum": {
                    "field": "numberOfApplicants"
                  }
                }
              }
            },            
            "men (interviewed)": {
              "scripted_metric": {
                "init_script": "_agg['men'] = [];",
                "map_script": "if (doc['gender'].value.startsWith('m')) { _agg['men'].add(doc['numberOfInterviews'].value); };",
                "combine_script": "result = 0; for (t in _agg['men']) { result += t; }; return result;",
                "reduce_script": "totalMen = 0; for (a in _aggs) { totalMen += a; }; return totalMen;"
              }
            },
            "women (interviewed)": {
              "scripted_metric": {
                "init_script": "_agg['women'] = [];",
                "map_script": "if (doc['gender'].value.startsWith('f')) { _agg['women'].add(doc['numberOfInterviews'].value); };",
                "combine_script": "result = 0; for (t in _agg['women']) { result += t; }; return result;",
                "reduce_script": "totalWomen = 0; for (a in _aggs) { totalWomen += a; }; return totalWomen;"
              }
            },            
            "average age (interviewed)": {
              "scripted_metric": {
                "init_script": "keys = ['[18-24]', '[25-34]', '[35-44]', '[45-54]', '[55-64]', '[65+]']; for (key in keys) { _agg[key] = []; };",
                "map_script": "_agg[doc['ageRange'].value].add(doc['numberOfInterviews'].value);",
                "combine_script": "keys = ['[18-24]', '[25-34]', '[35-44]', '[45-54]', '[55-64]', '[65+]']; result = [:]; for (key in keys) { result[key] = 0; }; for (key in keys) { for (value in _agg[key]) { result[key] += value; }; }; return result;",
                "reduce_script": "keys = ['[18-24]', '[25-34]', '[35-44]', '[45-54]', '[55-64]', '[65+]']; result = [:]; result2 = [:]; total = 0; index = 0; meanAgeRange = ''; ageUnit = 10; prevValue = 0; for (key in keys) { result[key] = 0; result2[key] = 0; }; for (key in keys) { for (value in _aggs[key]) { result[key] += value; }; }; for (key in keys) { if (index == 0) { result2[key] = result[keys.getAt(index)]; } else { result2[key] = result[keys.getAt(index)] + result2[keys.getAt(index - 1)]; }; index++; total += result[key]; }; mean = (total + 1)/2.0 as int; index = -1; for (key in keys) { if (result2[key] >= mean) { meanAgeRange = key; break; }; index++; }; if (index > 0) { prevValue = result2[keys.getAt(index)]; }; if (meanAgeRange != '') { delta = ((mean - prevValue) / result[meanAgeRange]) as double; return (meanAgeRange.substring(1,3).toInteger() + (delta * ageUnit)) as int; } else { return 0 };"
              }
            },
            "top popular city (interviewed)": {
              "terms": {
                "field": "city",
                "size": 1,
                "order": {
                  "number of visitors": "desc"
                }
              },
              "aggs": {
                "number of visitors": {
                  "sum": {
                    "field": "numberOfInterviews"
                  }
                }
              }
            },              
            "men (hired)": {
              "scripted_metric": {
                "init_script": "_agg['men'] = [];",
                "map_script": "if (doc['gender'].value.startsWith('m')) { _agg['men'].add(doc['numberOfHires'].value); };",
                "combine_script": "result = 0; for (t in _agg['men']) { result += t; }; return result;",
                "reduce_script": "totalMen = 0; for (a in _aggs) { totalMen += a; }; return totalMen;"
              }
            },
            "women (hired)": {
              "scripted_metric": {
                "init_script": "_agg['women'] = [];",
                "map_script": "if (doc['gender'].value.startsWith('f')) { _agg['women'].add(doc['numberOfHires'].value); };",
                "combine_script": "result = 0; for (t in _agg['women']) { result += t; }; return result;",
                "reduce_script": "totalWomen = 0; for (a in _aggs) { totalWomen += a; }; return totalWomen;"
              }
            },            
            "average age (hired)": {
              "scripted_metric": {
                "init_script": "keys = ['[18-24]', '[25-34]', '[35-44]', '[45-54]', '[55-64]', '[65+]']; for (key in keys) { _agg[key] = []; };",
                "map_script": "_agg[doc['ageRange'].value].add(doc['numberOfHires'].value);",
                "combine_script": "keys = ['[18-24]', '[25-34]', '[35-44]', '[45-54]', '[55-64]', '[65+]']; result = [:]; for (key in keys) { result[key] = 0; }; for (key in keys) { for (value in _agg[key]) { result[key] += value; }; }; return result;",
                "reduce_script": "keys = ['[18-24]', '[25-34]', '[35-44]', '[45-54]', '[55-64]', '[65+]']; result = [:]; result2 = [:]; total = 0; index = 0; meanAgeRange = ''; ageUnit = 10; prevValue = 0; for (key in keys) { result[key] = 0; result2[key] = 0; }; for (key in keys) { for (value in _aggs[key]) { result[key] += value; }; }; for (key in keys) { if (index == 0) { result2[key] = result[keys.getAt(index)]; } else { result2[key] = result[keys.getAt(index)] + result2[keys.getAt(index - 1)]; }; index++; total += result[key]; }; mean = (total + 1)/2.0 as int; index = -1; for (key in keys) { if (result2[key] >= mean) { meanAgeRange = key; break; }; index++; }; if (index > 0) { prevValue = result2[keys.getAt(index)]; }; if (meanAgeRange != '') { delta = ((mean - prevValue) / result[meanAgeRange]) as double; return (meanAgeRange.substring(1,3).toInteger() + (delta * ageUnit)) as int; } else { return 0 };"
              }
            },
            "top popular city (hired)": {
              "terms": {
                "field": "city",
                "size": 1,
                "order": {
                  "number of visitors": "desc"
                }
              },
              "aggs": {
                "number of visitors": {
                  "sum": {
                    "field": "numberOfHires"
                  }
                }
              }
            }              
          }
        }
      }
    }
  }
}
```

### Overview education details query
```
GET test-dka/projects/_search
{
  "query": 
  {
    "bool": {
      "filter": [
        {
          "terms": {
            "customerId": [
              1122
            ]
          }
        },
        {
          "terms": {
            "departmentId": [
              18957,
              18967,
              18985              
            ]
          }
        },
        {
          "terms": {
            "projectCategory": [
              "seller"
            ]
          }          
        },
        {
          "has_child": {
            "type": "candidates",
            "filter": [
              {
                "range": {
                  "date": {
                    "gte": "2010-01-01",
                    "lte": "2016-12-01"
                  }
                }
              }
            ]
          }
        }
      ] 
    }
  },
  "from": 0, 
  "size": 0,
  "aggs": {
    "average education level stats": {
      "children": {
        "type": "candidates"
      },
      "aggs": {
        "filtered average education level stats": {
          "filter": {
            "range": {
              "date": {
                "gte": "2010-01-01",
                "lte": "2016-12-01"
              }
            }
          },
          "aggs": {
            "for category": {
              "filter": {
                "term": {
                  "isApplied": true
                }
              },
              "aggs": {
                "average education level": {
                  "scripted_metric": {
                    "init_script": "_agg['key'] = ''",
                    "map_script": "key = doc['highestEducationLevel'].value; _agg['key'] = key; _agg[key] = 1;",
                    "combine_script": "return _agg;",
                    "reduce_script": "keys = []; result = [:]; for (a in _aggs) { if (a['key'] != null && a['key'] != '') { if (!(a['key'] in keys))  { keys.add(a['key']); }; if (result[a['key']] == null) { result[a['key']] = 0; }; result[a['key']] += a[a['key']]; }; }; index = 0; total = 0; result2 = [:];  for (key in keys) { if (index == 0) { result2[key] = result[key]; } else { result2[key] = result[key] + result2[keys.getAt(index - 1)];  }; index++;  total += result[key]; }; mean = (total + 1)/2 as int; index = -1; meanEducationLevel =''; for (key in keys) { if (result2[key] >= mean) { meanEducationLevel = key; break; }; index++; }; return meanEducationLevel;"
                  }
                }
              }
            }
          }
        }
      }
    }
  }  
}
```

```
GET test-dka/candidates/_search
{
  "query":
  {
    "bool": {
      "filter": [
        {
          "term": {
            "longitude": "undefined"
          }          
        } 
      ]
    }
  },
  "from": 0,
  "size": 10000
}
```

```
POST test-dka/candidates/_update_by_query
{
  "query":
  {
    "bool": {
      "filter": [
        {
          "term": {
            "latitude": "undefined"
          }          
        } 
      ]
    }
  },
  "script": {
    "inline": "ctx._source.latitude='Norway';",
    "lang": "painless"
  }   
}
```














