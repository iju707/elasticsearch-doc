# 검색 API
간단하게 검색을 해보도록 하겠습니다. 검색에 대한 조건을 전달하는 방식으로는 두가지가 있습니다.
* [REST request URI의 파라미터 방식](../search-uri-request.md)
* [REST request Body 방식](../search-request-body.md)

request body 방식이 좀 더 다양한 것을 할 수 있고 JSON 포맷을 사용하여 읽기가 용이합니다. 두가지 방식 모두에 대해서 해보도록 하겠습니다.

검색을 위한 REST API는 ```_search```를 사용합니다. 아래 예제는 *bank* 인덱스에 있는 모든 문서를 *account_number*의 오름차순으로 조회합니다.
```
curl -XGET 'localhost:9200/bank/_search?q=*&sort=account_number:asc&pretty'
```
