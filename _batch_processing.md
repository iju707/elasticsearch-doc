# 배치 처리
각각의 문서에 대하여 인덱싱/수정/삭제와 더불어서 Elasticsearch는 [_bulk API](../docs-bulk.md)를 이용한 배치처리가 가능합니다. 네트워크에 대한 비용을 적게 사용하여 다수의 동작을 좀 더 빠르고 효율적으로 처리할 수 있게 해주는 매커니즘 입니다.

간단한 예로, 한개의 명령으로 2개의 문서(ID가 1 - Johe Doe, ID가 2 - Jane Doe)를 인덱싱해보겠습니다.
```
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty&pretty' -d'
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }'
```
아래 예제는 한개의 명령으로 ID가 1인 문서를 수정하고, ID가 2인 문서를 삭제하는 것 입니다.
```
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty&pretty' -d'
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}'
```
위 예제에서 삭제의 경우에는 ID정보만 있으면 되기 때문에 별도의 문서 정보가 없어도 됩니다.

bulk 명령은 주어진 모든 명령을 순차적으로 처리합니다. 하나의 처리가 어떤 이유로 실패했다고 해도 남아있는 다른 명령을 순차적으로 진행할 것 입니다. bulk 명령이 끝나면 각각의 명령에 대한 결과를 제공해서 어디에서 실패가 발생하였는지 확인할 수 있습니다.