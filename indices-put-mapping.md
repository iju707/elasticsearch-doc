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

## 필드매핑 수정
일반적으로 매핑에 존재하는 기존 필드는 수정이 불가능합니다. 그렇지만 다음의 경우는 예외가 됩니다.
* 신규 [속성](properties.md)은 [객체 데이터타입](object.md) 필드에 추가될 수 있습니다.
* 신규 [다중필드](multi-fields.md)는 기존 필드에 추가될 수 있습니다.
* [doc_values](doc-values.md) 는 비활성화 될 수 있지만, 다시 활성화 할 순 없습니다.
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
> ```name``` 필드에 ```last``` 필드를 추가합니다. <br>
> ```user_id```에 ```ignore_above``` 속성을 기본값인 0에서 100으로 변경합니다.

각각의 [매핑 파라미터](mapping-params.md)는 기존의 필드에 수정이 되던 안되던 표시할 수 있습니다.
## 다른 타입간의 필드 충돌
다른 타입에 있는 동일한 이름의 동일한 인덱스 필드는 내부적으로는 동일한 필드이기 때문에 동일한 매핑을 가져야합니다. 한개 이상의 타입에 속하는 필드를 가지고 [매핑 파라미터 수정](indices-put-mapping.md#updating-field-mappings)을 시도할 경우 예외처리가 발생합니다. 이럴 경우에는 ```update_all_types```라는 파라미터를 지정하여 동일 인덱스의 동일이름을 가지는 모든 필드를 찾아 수정해야합니다.
> 이 규칙에서 제외되는 파라미터 - 각각 필드에 별개로 설정될 수 있는 - 는 [매핑 타입간 공유되는 필드](mapping.md#field-conflicts) 섹션에서 찾을 수 있습니다.

예로 들어 다음은 오류가 발생합니다.
```
curl -XPUT 'localhost:9200/my_index?pretty' -d'
{
  "mappings": {
    "type_one": {
      "properties": {
        "text": { 
          "type": "text",
          "analyzer": "standard"
        }
      }
    },
    "type_two": {
      "properties": {
        "text": { 
          "type": "text",
          "analyzer": "standard"
        }
      }
    }
  }
}'
```
> 동일한 매핑을 가지는 ```text```라는 필드를 포함하는 두개의 타입을 가지는 인덱스를 생성합니다.


```
curl -XPUT 'localhost:9200/my_index/_mapping/type_one ?pretty' -d'
{
  "properties": {
    "text": {
      "type": "text",
      "analyzer": "standard",
      "search_analyzer": "whitespace"
    }
  }
}'
```
> ```type_one```에 있는 ```search_analyzer``` 정보를 수정하려고 하면 ```Merge failed with failures...```라는 메시지와 함께 오류가 발생합니다.

그렇지만 다음과 같이 처리하면 됩니다.
```
curl -XPUT 'localhost:9200/my_index/_mapping/type_one?update_all_types &pretty' -d'
{
  "properties": {
    "text": {
      "type": "text",
      "analyzer": "standard",
      "search_analyzer": "whitespace"
    }
  }
}'
```
> ```update_all_types```라는 파라미터를 추가하여 ```type_one```과 ```type_two```에 있는 ```text``` 필드의 정보를 수정합니다.