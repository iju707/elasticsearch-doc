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
