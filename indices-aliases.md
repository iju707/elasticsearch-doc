# 인덱스 별칭
Elasticsearch의 API는 무언가 특정 인덱스나 다수의 인덱스를 처리할 때 인덱스 이름을 기준으로 동작합니다. 인덱스 별칭API는 모든 API가 자동으로 실제 인덱스로 변경이 되는 별칭을 가능하게 해줍니다. 별칭은 한개 이상의 인덱스에 매핑이 가능하며, 그렇게 지정하면 자동으로 확장처리가 됩니다. 또한 특정 값에 대한 검색, 분기를 위하여 필터링처리를 할 수 도 있습니다. 그렇지만 인덱스와 동일한 이름으로 생성은 불가능 합니다.

다음은 ```text1```인덱스에 ```alias1``` 별칭을 부여하는 것 입니다.
```
curl -XPOST 'localhost:9200/_aliases?pretty' -d'
{
    "actions" : [
        { "add" : { "index" : "test1", "alias" : "alias1" } }
    ]
}'
```
다음은 해당 별칭을 제거하는 것 입니다.
```
curl -XPOST 'localhost:9200/_aliases?pretty' -d'
{
    "actions" : [
        { "remove" : { "index" : "test1", "alias" : "alias1" } }
    ]
}'
```
이름 변경의 경우에는 1개의 요청에 ```remove```와 ```add```를 수행하시면 됩니다. 아주 간결한 동작이기 때문에 순간적으로 인덱스를 가리키지 못하는 것에 대한 걱정은 안하셔도 됩니다.
```
curl -XPOST 'localhost:9200/_aliases?pretty' -d'
{
    "actions" : [
        { "remove" : { "index" : "test1", "alias" : "alias1" } },
        { "add" : { "index" : "test2", "alias" : "alias1" } }
    ]
}'
```
별칭에 1개 이상의 인덱스를 지정하는 것도 ```add```를 사용하여 처리할 수 있습니다.
```
curl -XPOST 'localhost:9200/_aliases?pretty' -d'
{
    "actions" : [
        { "add" : { "index" : "test1", "alias" : "alias1" } },
        { "add" : { "index" : "test2", "alias" : "alias1" } }
    ]
}'
```
또는 인덱스를 배열로 전달하여 처리할 수 있습니다.
```
curl -XPOST 'localhost:9200/_aliases?pretty' -d'
{
    "actions" : [
        { "add" : { "indices" : ["test1", "test2"], "alias" : "alias1" } }
    ]
}'
```
하나의 작업에 다수의 별칭을 지정하기 위한 ```aliases```구문을 지원합니다.

또 다른 예제로, 와일드카드를 사용하여 특정이름으로 시작하는 한개 이상의 인덱스를 지정할 수 도 있습니다.
```
curl -XPOST 'localhost:9200/_aliases?pretty' -d'
{
    "actions" : [
        { "add" : { "index" : "test*", "alias" : "all_test_indices" } }
    ]
}'
```
위 경우 조건과 일치한 현재 생성된 인덱스들에 대해서 별칭이 지정되는 것이며, 추후에 동일한 패턴으로 신규 인덱스가 추가/삭제 되더라도 자동으로 갱신하지는 않습니다.

하나 이상의 인덱스를 가리키는 별칭에 인덱스 오류가 발생합니다.

별칭을 활용하여 인덱스를 교체할 수 도 있습니다.
```
curl -XPUT 'localhost:9200/test     ?pretty'
curl -XPUT 'localhost:9200/test_2   ?pretty'
curl -XPOST 'localhost:9200/_aliases?pretty' -d'
{
    "actions" : [
        { "add":  { "index": "test_2", "alias": "test" } },
        { "remove_index": { "index": "test" } }  
    ]
}'
```
> 실수로 ```test```라는 인덱스를 추가한 경우 ```test_2```라는 정상적인 인덱스를 만들고 <br>
> 별칭 API를 이용하여 별칭을 지정하고 [인덱스 삭제](indices-delete-index.md)와 동일한 ```remove_index```을 활용하여 삭제할 수 있습니다.

## 필터링된 별칭
필터를 활용한 인덱스를 이용하면 동일한 인덱스에 다른 "View"를 만들 수 있습니다. 필터링은 다른 검색, 카운트, 삭제 등의 쿼리에서 사용되는 쿼리 DSL 구문을 활용하여 정의할 수 있습니다.

필터링된 별칭을 만들기 위해 매핑정보에 해당 필드가 존재해야 합니다.
```
curl -XPUT 'localhost:9200/test1?pretty' -d'
{
  "mappings": {
    "type1": {
      "properties": {
        "user" : {
          "type": "keyword"
        }
      }
    }
  }
}'
```
그런 다음 ```user```라는 필드를 기준으로 필터링된 별칭을 생성할 수 있습니다.
```
curl -XPOST 'localhost:9200/_aliases?pretty' -d'
{
    "actions" : [
        {
            "add" : {
                 "index" : "test1",
                 "alias" : "alias2",
                 "filter" : { "term" : { "user" : "kimchy" } }
            }
        }
    ]
}'
```
