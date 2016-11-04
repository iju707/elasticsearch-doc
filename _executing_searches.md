# 검색을 해보자
기본적인 검색의 파라미터에 대하여 알아봤습니다. 지금부터는 쿼리 DSL을 좀더 심오하게 알아보도록 하겠습니다.

일단, 검색되는 문서의 속성에 대해 살펴보도록 하겠습니다. 기본적으로 검색을 하게되면 JSON 방식으로 문서의 전체 내용을 반환하게 됩니다. 이 내용은 결과 중 ```_source```에 표시됩니다. 그런데, 만약 전체 문서의 내용이 필요없을 경우에는 어떻게 할까요? 다음과 같이 쿼리를 수행하시면 원하는 속성만 추출할 수 있습니다.

아래 예제는 검색된 결과 문서 중 *account_number*와 *balnace*의 두가지 항목을 추출하는 것 입니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { "match_all": {} },
  "_source": ["account_number", "balance"]
}'
```
위 내용을 실행해보면 ```_source``` 항목이 매우 간결해짐을 알 수 있습니다. 결과의 목록은 동일하게 ```_source```에 있지만 그 내용에는 *account_number*와 *balance*만 존재합니다.

SQL과 비교하자면 ```SELECT```와 ```FROM```사이의 속성 목록과 같은 내용이라고 보시면 됩니다.

다시 ```query``` 부분으로 돌아가보겠습니다. 직전에 모든 문서의 목록을 가져오려면 ```match_all```을 사용한다고 했습니다. 이젠 [```match``` 쿼리](../query-dsl-match-query.md)에 대해서 알아보겠습니다. 이 것은 특정 속성(또는 속성들)에 일치하는 문서를 검색할 수 있습니다.

아래 예제는 *account_number*가 20인 문서를 검색하는 것 입니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { "match": { "account_number": 20 } }
}'
```
다음은 *address*에 "mill"을 포함하고 있는 모든 문서를 검색합니다.(대소문자 구분안함)
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { "match": { "address": "mill" } }
}'
```
다음은 *address*에 "mill" 또는 "lane"을 포함하고 있는 모든 문서를 검색합니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { "match": { "address": "mill lane" } }
}'
```
```match```의 다른 버전인 ```match_parse```를 사용하여 *address*에 "mill lane"을 포함하고 있는 결과를 검색해보겠습니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { "match_phrase": { "address": "mill lane" } }
}'
```
다음으로 [```bool(ean)``` 쿼리](../query-dsl-bool-query.md)에 대하여 알아보겠습니다. ```bool```은 참/거짓 로직을 사용하여 쿼리를 좀 더 다양하게 확장할 수 있게 해줍니다.

```match```를 사용해서 한 것과 동일하게 아래 예제도 *address*에 "mill"과 "lane" 모두를 포함하고 있는 문서를 검색해보겠습니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": {
    "bool": {
      "must": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```
위 예제를 보시면 ```bool must```는 하위에 정의된 모든 조건을 만족하는 문서를 반환하게 됩니다.

반대로, 아래 예제는 *address*에 "mill" 또는 "lane"을 포함하고 있는 문서를 반환합니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": {
    "bool": {
      "should": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```
위 예제를 보시면 ```bool should```는 하위에 정의된 조건 중 하나라도 만족하는 문서를 반환하게 됩니다.

아래 예제는 위 두개의 예제와는 반대로 "mill"과 "lane"을 포함하지 않는 문서를 반환합니다.
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d'
{
  "query": {
    "bool": {
      "must_not": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```
