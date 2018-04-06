# 매핑 추가 {#indices-put-mapping}

매핑 추가 API는 기존 인덱스에 신규 타입을 추가하거나, 기존 타입에 필드를 추가할 수 있습니다.

```
curl -XPUT 'localhost:9200/twitter?pretty' -H 'Content-Type: application/json' -d'
{}
'
```

> 타입 매핑없이 ```twitter```라는 [인덱스를 생성](indices-create-index.md)

```
curl -XPUT 'localhost:9200/twitter/_mapping/_doc?pretty' -H 'Content-Type: application/json' -d'
{
  "properties": {
    "email": {
      "type": "keyword"
    }
  }
}'
```

> 매핑 추가 API를 이용하여 ```_doc``` 매핑타입에 ```email``` 이라는 필드를 추가합니다.

타입 매핑을 어떻게 정의하는지에 대한 상세한 내용은 [매핑 섹션](mapping.md)에서 다루도록 하겠습니다.

## 다중인덱스 처리 {#_multi_index}

매핑추가 API는 단건요청을 가지고 다수의 인덱스에 대한 처리도 지원합니다. 다음의 예는 ```twitter-1```과 ```twitter-2```라는 인덱스의 매핑을 동시에 처리하는 것 입니다.

```
# Create the two indices
curl -XPUT 'localhost:9200/twitter-1?pretty'
curl -XPUT 'localhost:9200/twitter-2?pretty'

# Update both mappings
curl -XPUT 'localhost:9200/twitter-1,twitter-2/_mapping/_doc?pretty' -H 'Content-Type: application/json' -d'
{
  "properties": {
    "user_name": {
      "type": "text"
    }
  }
}
'
```

> ```twitter-1,twitter-2```와 같이 인덱스는 [다중 인덱스 명명](multi-index.md)나 와일드카드 형식으로 정의할 수 있습니다.

## 필드매핑 수정 {#updating-field-mapping}

일반적으로 매핑에 존재하는 기존 필드는 수정이 불가능합니다. 그렇지만 다음의 경우는 예외가 됩니다.
* 신규 [속성](properties.md)은 [객체 데이터타입](object.md) 필드에 추가될 수 있습니다.
* 신규 [다중필드](multi-fields.md)는 기존 필드에 추가될 수 있습니다.
* [ignore_above](ignore_above.md)는 수정될 수 있습니다.

예로 들어
```
curl -XPUT 'localhost:9200/my_index ?pretty' -d'
{
  "mappings": {
    "user": {
      "properties": {
        "name": {
          "properties": {
            "first": {
              "type": "text"
            }
          }
        },
        "user_id": {
          "type": "keyword"
        }
      }
    }
  }
}'
```
> ```first```이 속성인 ```name```이라는 [객체 데이터타입](object.md) 필드와 ```user_id```라는 필드를 가진 인덱스를 생성합니다.


```
curl -XPUT 'localhost:9200/my_index/_mapping/user?pretty' -d'
{
  "properties": {
    "name": {
      "properties": {
        "last": { 
          "type": "text"
        }
      }
    },
    "user_id": {
      "type": "keyword",
      "ignore_above": 100 
    }
  }
}'
```
> ```name``` 필드에 ```last``` 필드를 추가합니다.
> ```user_id```에 ```ignore_above``` 속성을 기본값인 0에서 100으로 변경합니다.

각각의 [매핑 파라미터](mapping-params.md)는 기존의 필드에 수정이 되던 안되던 표시할 수 있습니다.

> 2018-04-06 : 6.2 버전