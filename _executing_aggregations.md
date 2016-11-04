# 집계를 해보자
집계는 데이터를 그룹화 하고 통계를 수행할 수 있도록 해줍니다. 쉽게 생각하면 SQL의 GROUP BY 구문이나 집계함수를 떠올리시면 됩니다. Elasticsearch에서는 한개의 요청으로 검색의 결과와 집계의 결과를 동시에 받을 수 있습니다. 간결하고 단순화된 API를 사용해서 쿼리와 다수의 집계정보를 한번의 요청에 결과를 수신받아 네트워크 비용을 줄일 수 있습니다.

간단한 예제로 시작해보겠습니다. 계정의 주를 기준으로 그룹화를 하고 문서개수 상위 10(기본값)개의 주 정보를 출력해보겠습니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}'
```
SQL 과 비교해보면 다음과 같습니다.
```sql
SELECT state, COUNT(*) FROM bank GROUP BY state ORDER BY COUNT(*) DESC
```
응답결과는 아래와 같습니다.
```json
{
  "took": 29,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_state" : {
      "doc_count_error_upper_bound": 20,
      "sum_other_doc_count": 770,
      "buckets" : [ {
        "key" : "ID",
        "doc_count" : 27
      }, {
        "key" : "TX",
        "doc_count" : 27
      }, {
        "key" : "AL",
        "doc_count" : 25
      }, {
        "key" : "MD",
        "doc_count" : 25
      }, {
        "key" : "TN",
        "doc_count" : 23
      }, {
        "key" : "MA",
        "doc_count" : 21
      }, {
        "key" : "NC",
        "doc_count" : 21
      }, {
        "key" : "ND",
        "doc_count" : 21
      }, {
        "key" : "ME",
        "doc_count" : 20
      }, {
        "key" : "MO",
        "doc_count" : 20
      } ]
    }
  }
}
```
결과의 내용을 보면 ID(Idaho) 주가 27개의 계정을, TX(Texas) 주가 27개의 계정을 AL(Alabama)주가 25개의 계정을 가지고 있음을 알 수 있습니다.

우리가 ```size=0```으로 지정한 이유는 검색에 대한 결과는 필요가 없고, 집계한 결과만 필요하기 때문입니다.

이어서, 직전과 같이 문서 개수 상위 10개의 주에 대하여 평균 잔액을 계산해보도록 하겠습니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}'
```
```average_balance```를 ```group_by_state``` 안에 어떻게 작성했는지 잘 보시기 바랍니다. 이것은 모든 집계의 기본 패턴이 됩니다. 집계 안의 집계는 데이터에 대한 원하는 요약을 만들어내는데 사용됩니다.

이어서, 결과를 평균 잔액기준으로 내림차순 정렬해보겠습니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword",
        "order": {
          "average_balance": "desc"
        }
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}'
```
아래 예제는 연령그룹(20대, 30대, 40대)별, 성별로 그룹화 한 뒤 평균 잔액을 계산하는 것 입니다.
```
curl -XGET 'localhost:9200/bank/_search?pretty' -d'
{
  "size": 0,
  "aggs": {
    "group_by_age": {
      "range": {
        "field": "age",
        "ranges": [
          {
            "from": 20,
            "to": 30
          },
          {
            "from": 30,
            "to": 40
          },
          {
            "from": 40,
            "to": 50
          }
        ]
      },
      "aggs": {
        "group_by_gender": {
          "terms": {
            "field": "gender.keyword"
          },
          "aggs": {
            "average_balance": {
              "avg": {
                "field": "balance"
              }
            }
          }
        }
      }
    }
  }
}'
```
여기서 다루지는 않지만 많은 집계 기능들이 있습니다. [집계 가이드](search-aggregations.md)를 참고하셔서 더 많은 기능을 구현해보시기 바랍니다.