# 쿼리 언어란?
Elasticsearch에서는 쿼리를 수행하기 위하여 JSON 방식의 문법을 제공하고 있습니다. [쿼리 DSL](query-dsl.md)부분을 참고하세요. 쿼리문은 매우 복잡하고 어려울 수 있습니다. 학습하기 좋게 간단한 예제로 알아보도록 하겠습니다.

마지막 예제로 돌아가서 다시한번 쿼리를 실행해보도록 하겠습니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { "match_all": {} }
}'
```
```query``` 부분은 우리가 검색하고자 하는 조건에 대한 쿼리문을 지칭하는 것이고, ```match_all```은 쿼리문의 상세 내용입니다. 우리가 지정한 인덱스에 대하여 전체 문서를 조회하라는 의미입니다.

```query```말고도 다른 파라미터를 추가할 수 있습니다. 직전의 예제에서는 ```sort```를 사용하였다면 여기서는 결과의 목록 개수를 의미하는 ```size```를 해보도록 하겠습니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { "match_all": {} },
  "size": 1
}'
```
만약 ```size``` 항목이 정의되지 않으면 자동으로 10이라는 값을 설정하게 됩니다.

다음 예제는 ```match_all```의 결과에서 11번부터 20번째의 문서를 반환하는 예제입니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 10
}'
```
```from``` 파라미터는 검색의 결과 시작순번(0부터 시작)을 지칭하고 ```size```는 ```from```부터 몇개의 문서를 반환받을 것인지에 대한 내용입니다. 이 두가지를 잘 활용하면 페이징 기능을 쉽게 구현할 수 있습니다. ```from```은 따로 정의하지 않으면 0으로 설정됩니다.

아래 예제는 ```match_all```을 수행하면서 그 결과를 *balance* 항목을 기준으로 내림차순하여 정렬한 뒤 10개(```size```의 기본값)을 반환하는 것 입니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { "match_all": {} },
  "sort": { "balance": { "order": "desc" } }
}'
```