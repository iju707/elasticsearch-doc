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
