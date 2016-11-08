# 매핑 추가
매핑 추가 API는 신규 인덱스를 생성할 때 타입 매핑을 추가하거나 기존 인덱스에 신규 타입을 추가하거나, 기존 타입에 필드를 추가할 수 있습니다.
```
curl -XPUT 'localhost:9200/twitter ?pretty' -d'
{
  "mappings": {
    "tweet": {
      "properties": {
        "message": {
          "type": "text"
        }
      }
    }
  }
}'
```
> ```message```라는 필드를 사용하여 ```tweet```이라는 [매핑타입](mapping.md#mapping-type)을 가지는 ```twitter```라는 [인덱스를 생성](indices-create-index.md)하는 것 입니다.


```
curl -XPUT 'localhost:9200/twitter/_mapping/user ?pretty' -d'
{
  "properties": {
    "name": {
      "type": "text"
    }
  }
}'
```
> 매핑생성 API를 이용하여 ```user```라는 이름을 가지는 매핑을 추가하는 것 입니다.


```
curl -XPUT 'localhost:9200/twitter/_mapping/tweet ?pretty' -d'
{
  "properties": {
    "user_name": {
      "type": "text"
    }
  }
}'
```
> 매핑생성 API를 이용하여 ```tweet```라는 매핑에 ```user_name```이라는 필드를 추가하는 것 입니다.

타입 매핑을 어떻게 정의하는지에 대한 상세한 내용은 [매핑 섹션](mapping.md)에서 다루도록 하겠습니다.

## 다중인덱스 처리
매핑생성 API는 단건요청을 가지고 다수의 인덱스에 대한 처리도 지원합니다. 다음과 같은 포맷으로 하시면 됩니다.
```
PUT /{index}/_mapping/{type}
{ body }
```
* ```{index}``` [다중 인덱스](multi-index.md)나 와일드카드를 지원합니다.
* ```{type}``` 갱신할 매핑 타입의 이름입니다.
* ```{body}``` 적용할 매핑에 대한 정보입니다.