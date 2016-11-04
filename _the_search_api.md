# 검색 API
간단하게 검색을 해보도록 하겠습니다. 검색에 대한 조건을 전달하는 방식으로는 두가지가 있습니다.
* [REST request URI의 파라미터 방식](../search-uri-request.md)
* [REST request Body 방식](../search-request-body.md)

request body 방식이 좀 더 다양한 것을 할 수 있고 JSON 포맷을 사용하여 읽기가 용이합니다. 두가지 방식 모두에 대해서 해보도록 하겠습니다.

검색을 위한 REST API는 ```_search```를 사용합니다. 아래 예제는 *bank* 인덱스에 있는 모든 문서를 *account_number*의 오름차순으로 조회합니다.
```
curl -XGET 'localhost:9200/bank/_search?q=*&sort=account_number:asc&pretty'
```
명령에 대해서 하나씩 보겠습니다. *bank* 인덱스에 대하여 *_search*를 사용해서 검색을 합니다. ```q=*```는 Elasticsearch에게 인덱스에 있는 모든 문서를 조회하라는 것 입니다. ```pretty```는 이전에도 언급되었듯 JSON 양식으로 출력하라는 것 입니다.

응답결과는 다음과 같습니다.(일부 생략)
```json
{
  "took" : 63,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : null,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "0",
      "sort": [0],
      "_score" : null,
      "_source" : {"account_number":0,"balance":16623,"firstname":"Bradshaw","lastname":"Mckenzie","age":29,"gender":"F","address":"244 Columbus Place","employer":"Euron","email":"bradshawmckenzie@euron.com","city":"Hobucken","state":"CO"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "1",
      "sort": [1],
      "_score" : null,
      "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, ...
    ]
  }
}
```
응답결과를 세부적으로 보면 다음과 같습니다.
* ```took``` - Elasticsearch가 검색을 수행하는데 걸린 밀리초 단위의 시간입니다.
* ```timed_out``` - 검색이 시간초과 발생하였는지 여부
* ```_shards``` - 검색에 사용된 파편의 개수, 검색이 성공 또는 실패한 파편의 개수
* ```hits``` - 검색결과
* ```hits.total``` - 검색조건에 일치하는 문서의 개수
* ```hits.hits``` - 실제 검색된 문서 목록 (기본값은 최초 10개 문서)
* ```sort``` - 결과의 정렬 속성(없을 경우 score 항목을 사용)
* ```_score```와```max_score``` - 일단 지금은 넘어가도록 하겠습니다.

다음은 request Body 방식으로 동일하게 검색해보도록 하겠습니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { "match_all": {} },
  "sort": [
    { "account_number": "asc" }
  ]
}'
```
> 원본 문서에는 GET 방식으로 진행하나, curl에서 GET의 Body를 지원하지만 실제 REST/HTTP 스펙상 GET은 Body를 사용하지 않는 것이 맞습니다. 그래서 POST로 변경을 하였습니다.

직전에 했던 request uri 방식에서의 ```q=*```와는 달리 JSON 형식으로 하여 검색관련 내용을 기입하고 ```_search``` API에 POST 방식으로 전송하였습니다. JSON의 상세 내용은 다음장에서 다루도록 하겠습니다.

Elasticsearch의 경우에는 검색에 대한 요청을 받고 결과를 반환하게 되면 서버에 별도의 자원이나 커서를 보관하지 않습니다. SQL과 같은 다른 플랫폼에서는 검색을 요청하면 쿼리에 대한 부분적인 결과를 수신받고 다시 요청하면 서버 측에서 보관한 검색에 대한 커서를 이어서 진행하여 페이징과 같은 기능을 수행하는 것과는 많이 다릅니다.