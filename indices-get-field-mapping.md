# 필드 매핑 조회
속성 매핑조회 API는 1개 이상의 필드에 대한 매핑정의를 검색할 수 있습니다. [매핑조회 API](indices-get-mapping.md)처럼 타입 매핑에 대한 전체 정보가 필요없을 때 유용합니다.

다음은 ```text``` 필드에 대한 매핑정보만 검색하는 예제입니다.
```
curl -XGET 'localhost:9200/twitter/_mapping/tweet/field/message?pretty'
```
결과는 다음과 같습니다.(```text```는 기본 문자 필드로 가정하겠습니다)
```json
{
   "twitter": {
      "mappings": {
         "tweet": {
            "message": {
               "full_name": "message",
               "mapping": {
                  "message": {
                     "type": "text",
                     "fields": {
                        "keyword": {
                           "type": "keyword",
                           "ignore_above": 256
                        }
                     }
                  }
               }
            }
         }
      }
   }
}
```
## 다중 인덱스, 타입, 필드
필드 매핑조회API는 한번의 요청으로 1개이상의 인덱스나 타입에 대한 다수의 필드 매핑정보를 가져올 수 있습니다. 일반적은 API 문법은 ```host:port/{index}/{type}/_mapping/field/{field}```입니다. 여기서 ```{index}```, ```{type}```, ```{field}```는 쉼표(,)로 구분하거나 와일드카드를 이용하여 나열할 수 있습니다. 모든 인덱스에 대한 매핑정보를 조회하고자 할 경우에는 ```{index}```에 ```_all```를 사용하시면 됩니다. 몇가지 예제를 보겠습니다.
```
curl -XGET 'localhost:9200/twitter,kimchy/_mapping/field/message?pretty'
curl -XGET 'localhost:9200/_all/_mapping/tweet,book/field/message,user.id?pretty'
curl -XGET 'localhost:9200/_all/_mapping/tw*/field/*.id?pretty'
```
## 필드 명시
필드 매핑조회API는 쉼표(,)를 이용하여 한개 이상의 필드를 명시할 수 있습니다. 또한 와일드카드도 사용이 가능합니다. 필드명은 두가지 방식을 따르게 됩니다.
* 전체이름 : 부모 객체 이름까지 포함한 전체 경로이름(예 ```user.id```)
* 필드이름 : 경로를 제외한 필드의 이름(예 ```{ " user" : { "id" : 1 } }```를 위한 ```id```)

```field``` 부분이 처리되는데 있어서 순서가 존재합니다. 그래서 첫번째로 찾아진 것에 대한 결과를 반환합니다. 이 부분은 인덱스나 필드들간의 이름이 모호한 경우에 중요합니다.

예로 다음과 같은 매핑이 있을 경우
```json
{
    "article": {
        "properties": {
            "id": { "type": "text" },
            "title":  { "type": "text"},
            "abstract": { "type": "text"},
            "author": {
                "properties": {
                    "id": { "type": "text" },
                    "name": { "type": "text" }
                }
            }
        }
    }
}
```
```author```의 ```id```를 검색하고자 한다면, 전체경로인 ```author.id```를 사용해야되고, ```name```은 ```author.name``` 필드를 반환할 것 입니다.
```
curl -XGET "http://localhost:9200/publications/_mapping/article/field/author.id,abstract,name"
```
결과는 다음과 같습니다.
```json
{
   "publications": {
      "article": {
         "abstract": {
            "full_name": "abstract",
            "mapping": {
               "abstract": { "type": "text" }
            }
         },
         "author.id": {
            "full_name": "author.id",
            "mapping": {
               "id": { "type": "text" }
            }
         },
         "name": {
            "full_name": "author.name",
            "mapping": {
               "name": { "type": "text" }
            }
         }
      }
   }
}
```
요청에 명기된 동일한 필드에 대하여 어떤 응답이 오는지 잘 알아야 됩니다. 매핑 결과가 반환되면 전체 이름이 있는 ```full_name```이 모든 내용에 포함됩니다. 이 것은 다중 필드를 처리할 때 매우 유용합니다.
## 추가 옵션
```include_defaults```
```include_defaults=true```옵션을 요청에 추가하면 일반적으로 생략되는 기본값을 포함하여 결과를 반환합니다. 