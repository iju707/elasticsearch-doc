# 필터링을 해보자
이전 내용에서 검색 결과 중 ```_score```항목에 대해서는 설명을 생략했었습니다. 이 것은 우리가 정의한 검색 조건에 얼마나 맞는지에 대한 수치입니다. 수치가 높을 수 록 더 정확한 문서이고, 수치가 낮을 수록 부정확한 문서입니다.

문서들을 필터링할 때를 제외하고는 점수 항목을 처리할 필요가 없습니다. 그래서 Elasticsearch는 검색 쿼리를 분석해서 불필요한 점수 계산을 하지 않도록 최적화를 자동으로 해줍니다.

[```bool``` 쿼리](query-dsl-bool-query.md)에서 ```filter```를 사용하여 다른 조건으로 검색된 결과에서 점수 계산을 다시 하지 않고(기존 점수를 유지) 필터링을 할 수 있습니다.

다음 예제를 통해서 [```range``` 쿼리](query-dsl-range-query.md)에 대해서 알아보겠습니다. ```ragne```는 숫자나 날짜 항목을 가지고 검색된 문서 에서 범위를 가지고 필터링 할 수 있는 조건 입니다.

아래 예제는 모든 문서를 조회한 뒤 *balance* 항목이 20000에서 30000 사이의 범위인 것을 필터링해보도록 하겠습니다. 다시 말해 *balance*가 20000이상 30000이하인 문서를 검색하는 것 입니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": {
    "bool": {
      "must": { "match_all": {} },
      "filter": {
        "range": {
          "balance": {
            "gte": 20000,
            "lte": 30000
          }
        }
      }
    }
  }
}'
```
위 내용을 분석해보면 ```bool``` 쿼리안에 ```match_all```과 ```range```를 사용하였습니다. 쿼리의 내용에는 다른 내용도 충분히 들어갈 수 있습니다. ```range``` 필터링을 하실때는 모든 문서가 범위안에 들어가지 않도록 주의를 하셔야 합니다. 그렇지 않으면 전체 문서를 검색할테니까요.

```match_all```, ```match```, ```bool```, ```range``` 쿼리 말고도 사용가능한 여러가지 쿼리가 있지만 여기서 다루지는 않겠습니다. 지금까지 배운 쿼리를 가지고 어떻게 동작하는지 잘 이해하신다면 다른 쿼리에 대하여 배우고 경험하는데는 어려움이 없을 것 입니다.