# 인덱스 별칭
인덱스 별칭API는 모든 API가 자동으로 실제 인덱스로 변경이 되는 별칭을 가능하게 해줍니다.

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
