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
## 별칭 추가
다음과 같은 형식으로 별칭을 추가할 수 있습니다.
```
PUT /{index}/_alias/{name}
```
여기서
* ```index``` 참조될 인덱스 이름. ```*``` 또는 ```_all``` 또는 와일드카드방식 또는 쉼표(,)로 구분된 목록을 사용할 수 있습니다.
* ```name``` 별칭의 이름으로 필수값입니다.
* ```routing``` 별칭에 적용될 라우팅 값입니다. (선택)
* ```filter``` 별칭에 적용될 필터링 값입니다. (선택)

또한 복수형태인 ```_aliases```를 사용할 수 도 있습니다.

### 예시
시간 기반 별칭
```
curl -XPUT 'localhost:9200/logs_201305/_alias/2013?pretty'
```
사용자 기반 별칭
1. ```user_id``` 필드를 가진 매핑정보가 있는 인덱스를 생성합니다.
```
curl -XPUT 'localhost:9200/users?pretty' -d'
{
    "mappings" : {
        "user" : {
            "properties" : {
                "user_id" : {"type" : "integer"}
            }
        }
    }
}'
```
2. 특정 사용자를 기준으로 별칭을 지정합니다.
```
curl -XPUT 'localhost:9200/users/_alias/user_12?pretty' -d'
{
    "routing" : "12",
    "filter" : {
        "term" : {
            "user_id" : 12
        }
    }
}'
```

## 인덱스 생성시 별칭 지정
[인덱스를 생성](indices-create-index.md#create-index-aliases)할 때 별칭정보를 추가할 수 있습니다.
```
curl -XPUT 'localhost:9200/logs_20162801?pretty' -d'
{
    "mappings" : {
        "type" : {
            "properties" : {
                "year" : {"type" : "integer"}
            }
        }
    },
    "aliases" : {
        "current_day" : {},
        "2016" : {
            "filter" : {
                "term" : {"year" : 2016 }
            }
        }
    }
}'
```
## 별칭 삭제
별칭 삭제 API는 다음과 같습니다.
```
DELETE /{index}/_alias/{name}
```
* ```index``` 참조될 인덱스 이름. ```*``` 또는 ```_all``` 또는 와일드카드방식 또는 쉼표(,)로 구분된 목록을 사용할 수 있습니다.
* ```name``` 별칭 이름. ```*``` 또는 ```_all``` 또는 와일드카드방식 또는 쉼표(,)로 구분된 목록을 사용할 수 있습니다.

역시 복수형으로 ```_aliases```를 사용할 수 있습니다. 예제로
```
curl -XDELETE 'localhost:9200/logs_20162801/_alias/current_day?pretty'
```
## 별칭 검색
인덱스별칭 조회 API는 별칭이나 인덱스 이름으로 필터링 할 수 있습니다. 이 API는 마스터에 전달하여 요청된 인덱스별칭에 대해서 처리합니다. 그리고 검색된 인덱스 별칭만 표시합니다.

가능한 옵션은
* ```index``` 별칭을 가져올 인덱스의 이름입니다. 와일드카드나 쉼표(,)로 구분하여 목록으로 지정할 수 있습니다. 또는 인덱스의 별칭으로 사용할 수 있습니다.
* ```alias``` 요청에 응답받을 별칭 이름입니다. 인덱스 옵션과 동일하게 와일드카드나 쉼표(,)로 구분된 목록을 지정할 수 있습니다.
* ```ignore_unavailable``` 선택한 인덱스가 존재하지 않을 경우에 대한 옵션입니다. 만약 ```true```로 하면 그 인덱스들은 무시가 됩니다.

기본적인 문법은 
```
GET /{index}/_alias/{alias}
```
입니다.
### 예제
```logs_20162801``` 인덱스의 모든 별칭을 조회하는 예제입니다.
```
curl -XGET 'localhost:9200/logs_20162801/_alias/*?pretty'
```
응답은 다음과 같습니다.
```json
{
 "logs_20162801" : {
   "aliases" : {
     "2016" : {
       "filter" : {
         "term" : {
           "year" : 2016
         }
       }
     }
   }
 }
}
```
또는 *2016* 이름을 가지는 모든 별칭을 조회하는 것 입니다.
```
curl -XGET 'localhost:9200/_alias/2016?pretty'
```
응답은 다음과 같습니다.
```json
{
  "logs_20162801" : {
    "aliases" : {
      "2016" : {
        "filter" : {
          "term" : {
            "year" : 2016
          }
        }
      }
    }
  }
}
```
또는 *20*으로 시작하는 모든 별칭을 조회하는 것 입니다.
```
curl -XGET 'localhost:9200/_alias/20*?pretty'
```
응답은 다음과 같습니다.
```json
{
  "logs_20162801" : {
    "aliases" : {
      "2016" : {
        "filter" : {
          "term" : {
            "year" : 2016
          }
        }
      }
    }
  }
}
```
## 별칭 유무확인
인덱스 별칭 조회 API에서 HEAD method를 사용하여 유무를 확인할 수 있습니다. 다른 API와 동일하게 HTTP 응답코드로 결과가 반환됩니다. 있는 경우 '''200'', 없는 경우 '''404'''로 반환됩니다.
```
curl -XHEAD 'localhost:9200/_alias/2016?pretty'
curl -XHEAD 'localhost:9200/_alias/20*?pretty'
curl -XHEAD 'localhost:9200/logs_20162801/_alias/*?pretty'
```
> Windows 버전 curl의 경우 -XHEAD를 지원하지 않기 때문에, --head 옵션을 사용하면 됩니다.