# 문서 색인/쿼리
*customer* 인덱스에 무언가를 입력해보도록 하겠습니다. 이전에도 언급되었지만 문서를 색인할 때 Elasticsearch에게 인덱스의 어떤 타입으로 할 것 인지 알려줘야 합니다.

간단하게 *customer* 인덱스에 고객 정보를 ID가 1이고 *external* 타입으로 색인해보도록 하겠습니다.
```
curl -XPUT 'localhost:9200/customer/external/1?pretty&pretty' -d'
{
  "name": "John Doe"
}'
```
응답결과는 다음과 같습니다.
```json
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "created" : true
}
```
새로운 고객 문서가 *customer* 인덱스에 *external* 타입으로 성공적으로 생성됨을 알 수 있습니다. 문서의 ID는 색인할 때 지정했던 1로 되어있습니다.

Elasticsearch는 인덱스에 문서 정보를 입력할 때 인덱스를 먼저 생성하라고 강제하지는 않습니다. 직전 예제와 다르게 인덱스가 존재하지 않는다면 Elasticsearch가 자동으로 인덱스를 생성할 것 입니다.

방금 색인한 문서에 대해서 조회해보겠습니다.
```
curl -XGET 'localhost:9200/customer/external/1?pretty&pretty'
```
응답결과는 다음과 같습니다.
```json
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : { "name": "John Doe" }
}
```
내용 중 *found*는 조회할 때 요청한 ID가 1인 문서가 검색되었는지 여부이며, *_source*는 검색된 문서에 대한 JSON 정보입니다.