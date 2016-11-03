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
