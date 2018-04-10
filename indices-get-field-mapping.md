# 필드 매핑 조회 {#indices-get-field-mapping}

속성 매핑조회 API는 1개 이상의 필드에 대한 매핑정의를 검색할 수 있습니다. [매핑조회 API](indices-get-mapping.md)처럼 타입 매핑에 대한 전체 정보가 필요없을 때 유용합니다.

예로, 다음의 매핑을 가정하겠습니다.

```
curl -XPUT 'localhost:9200/publications?pretty' -H 'Content-Type: application/json' -d'
{
    "mappings": {
        "_doc": {
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
}'
```

```title``` 필드의 매핑정보를 조회하는 명령입니다.

```
curl -XGET 'localhost:9200/publications/_mapping/_doc/field/title?pretty'
```

응답결과는 다음과 같습니다.

```json
{
   "publications": {
      "mappings": {
         "_doc": {
            "title": {
               "full_name": "title",
               "mapping": {
                  "title": {
                     "type": "text"
                  }
               }
            }
         }
      }
   }
}
```

## 다중 인덱스, 타입, 필드 {#_multiple_indices_types_and_fields}

필드 매핑조회API는 한번의 요청으로 1개이상의 인덱스나 타입에 대한 다수의 필드 매핑정보를 가져올 수 있습니다. 일반적은 API 문법은 ```host:port/{index}/{type}/_mapping/field/{field}```입니다. 여기서 ```{index}```, ```{type}```, ```{field}```는 쉼표(,)로 구분하거나 와일드카드를 이용하여 나열할 수 있습니다. 모든 인덱스에 대한 매핑정보를 조회하고자 할 경우에는 ```{index}```에 ```_all```를 사용하시면 됩니다. 몇가지 예제를 보겠습니다.

```
curl -XGET 'localhost:9200/twitter,kimchy/_mapping/field/message?pretty'
curl -XGET 'localhost:9200/_all/_mapping/tweet,book/field/message,user.id?pretty'
curl -XGET 'localhost:9200/_all/_mapping/tw*/field/*.id?pretty'
```

## 필드 명시 {#_specifying_fields}

필드 매핑조회API는 쉼표(,)를 이용하여 한개 이상의 필드를 명시할 수 있습니다.
예로 ```author```의 ```id```를 조회할 경우, 전체 이름인 ```author.id```를 사용해야합니다.

```
curl -XGET 'localhost:9200/publications/_mapping/_doc/field/author.id,abstract,name?pretty'
```

응답은

```json
{
   "publications": {
      "mappings": {
         "_doc": {
            "author.id": {
               "full_name": "author.id",
               "mapping": {
                  "id": {
                     "type": "text"
                  }
               }
            },
            "abstract": {
               "full_name": "abstract",
               "mapping": {
                  "abstract": {
                     "type": "text"
                  }
               }
            }
         }
      }
   }
}
```

와일드카드도 지원합니다.

```
curl -XGET 'localhost:9200/publications/_mapping/_doc/field/a*?pretty'
```

응답은

```json
{
   "publications": {
      "mappings": {
         "_doc": {
            "author.name": {
               "full_name": "author.name",
               "mapping": {
                  "name": {
                     "type": "text"
                  }
               }
            },
            "abstract": {
               "full_name": "abstract",
               "mapping": {
                  "abstract": {
                     "type": "text"
                  }
               }
            },
            "author.id": {
               "full_name": "author.id",
               "mapping": {
                  "id": {
                     "type": "text"
                  }
               }
            }
         }
      }
   }
}
```

## 추가 옵션 {#_other_options}

```include_defaults```
```include_defaults=true```옵션을 요청에 추가하면 일반적으로 생략되는 기본값을 포함하여 결과를 반환합니다. 

> 2018-04-10 : 6.2 버전 업데이트