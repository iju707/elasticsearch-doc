# 데이터를 수정해보자
Elasticsearch는 데이터 조작 또는 검색을 준 실시간적으로 가능하게 해줍니다. 기본적으로 검색을 할 때 결과가 나오기 까지 데이터의 색인/수정/삭제로 인한 갱신지연으로 1초 정도의 시간이 필요합니다. SQL 같은 다른 플랫폼과의 큰 차이점은 트랜젝션이 끝난 뒤 즉시 데이터를 사용할 수 있다는 점 입니다.

## 문서들의 인덱싱/교체
이전에 한개의 문서를 인덱싱하는 명령에 대해서 배웠습니다. 다시 한번 호출해보도록 하겠습니다.
```
curl -XPUT 'localhost:9200/customer/external/1?pretty&pretty' -d'
{
  "name": "John Doe"
}'
```
ID가 1이고, *external* 타입으로 *customer* 인덱스에 1개의 문서를 입력하였습니다. 기존에 있는 동일한 ID(1)로 다른 문서를 동일하게 입력한다면 Elasticsearch는 기존것 위로 새로운 문서를 교체할 것 입니다.
```
curl -XPUT 'localhost:9200/customer/external/1?pretty&pretty' -d'
{
  "name": "Jane Doe"
}'
```
ID가 1번인 문서에 대하여 *name* 항목을 *John Doe*에서 *Jane Doe*로 변경하였습니다. 만약 동일한 문서의 내용이지만 다른 ID를 사용한다면 기존의 문서는 건들지 않은 채로 신규 문서를 생성합니다.
```
curl -XPUT 'localhost:9200/customer/external/2?pretty&pretty' -d'
{
  "name": "Jane Doe"
}'
```
ID가 2인 신규 문서가 색인되었습니다.

인덱싱할 때 ID 항목은 선택사항입니다. 만약 명기하지 않으면 Elasticsearch가 해당 문서에 사용할 ID를 임의적으로 생성합니다. Elasticsearch가 만든(또는 이전 예제에 명기한) 실제 ID는 인덱스 API 호출