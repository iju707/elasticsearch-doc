# 인덱스 삭제
직전에 생성했던 인덱스를 삭제하고 다시 조회해보겠습니다.
```
curl -XDELETE 'localhost:9200/customer?pretty&pretty'
curl -XGET 'localhost:9200/_cat/indices?v&pretty'
```
응답결과는 다음과 같습니다.
```
health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
```
위 의미는 성공적으로 인덱스가 삭제되었고 클러스터 안에 아무것도 없는 상태가 되었다는 것 입니다.

다시 전으로 돌아가서 전체적인 API 명령을 보도록 하겠습니다.
```
curl -XPUT 'localhost:9200/customer?pretty'
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d'
{
  "name": "John Doe"
}'
curl -XGET 'localhost:9200/customer/external/1?pretty'
curl -XDELETE 'localhost:9200/customer?pretty'
```
사용한 명령을 자세히 보시면 Elasticsearch에서 데이터에 어떤 패턴을 가지고 접근하는지 알 수 있습니다.
```
[REST Verb] /[Index]/[Type]/[ID]
```
이 REST API 명령 패턴을 잘 기억하고 계시면 Elasticsearch를 마스터하는데 좋은 기반이 될 것 입니다.