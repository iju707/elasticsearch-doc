# 인덱스 목록 확인
현재 보유하고 있는 인덱스 목록을 확인해보겠습니다.
```
curl -XGET 'localhost:9200/_cat/indices?v&pretty'
```
그리고 응답결과는 다음과 같습니다.
```
health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
```
위 내용을 보면 아직 인덱스가 존재하지 않음을 알 수 있습니다.