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
