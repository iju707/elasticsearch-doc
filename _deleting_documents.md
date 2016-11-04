# 문서 삭제
문서를 삭제하는 것은 매우 간단합니다. 아래 예제는 이전에 만들었던 ID가 2인 문서를 삭제하는 것 입니다.
```
curl -XDELETE 'localhost:9200/customer/external/2?pretty&pretty'
```
[쿼리를 사용한 삭제 API](docs-delete-by-query.md)를 사용하시면 특정 조건에 맞는 문서만 삭제할 수 도 있습니다. 전체 인덱스를 삭제하는 것보다 쿼리를 사용한 삭제 API를 이용해서 전체 문서를 삭제하는 것이 더 효율적임을 주목할 필요가 있습니다.