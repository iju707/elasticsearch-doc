# 문서 수정
문서를 인덱싱하거나 교체할 수 있는 것 처럼 수정도 가능합니다. 실제로는 Elasticsearch에서 기존의 내용을 수정하지는 않습니다. 만약 수정요청이 오면 Elasticsearch는 기존의 문서를 인덱스에서 삭제하고 신규 문서를 인덱싱하여 한번에 수정한 것 처럼 만듭니다.

아래 예제는 기존에 ID 1로 만든 문서의 *name* 항목을 *Jane Doe*로 변경하는지 보여줍니다.
```
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty&pretty' -d'
{
  "doc": { "name": "Jane Doe" }
}'
```
아래 예제는 기존의 ID 1로 만든 문서의 *name* 항목을 *Jane Doe*로 변경하면서 *age*라는 항목을 추가하는 것 입니다.
```
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty&pretty' -d'
{
  "doc": { "name": "Jane Doe", "age": 20 }
}'
```
또는 간단한 스크립트 방식으로 문서를 수정할 수 있습니다. 아래 예제는 기존의 *age*에 5를 더하는 것 입니다.
```
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty&pretty' -d'
{
  "script" : "ctx._source.age += 5"
}'
```
이때, *ctx._source*는 현재 수정될 문서를 가리키고 있습니다.

지금까지 내용으로는 수정은 한번에 한개의 문서만 가능합니다. 하지만 뒤에서 SQL의 UPDATE-WHERE와 유사하게 조건에 따른 다수의 문서를 수정할 수 있는 Elasticsearch의 기능을 배워볼 것 입니다.