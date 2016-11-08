# 인덱스 생성
인덱스 생성 API를 통하여 인덱스를 인스턴스화 할 수 있습니다. Elasticsearch에서는 다수의 인덱스에서 각각 명령을 처리할 수 있도록 지원합니다.
## 인덱스 설정
인덱스 별로 특정 설정을 지정하여 생성할 수 있습니다.
```
curl -XPUT 'localhost:9200/twitter?pretty' -d'
{
    "settings" : {
        "index" : {
            "number_of_shards" : 3, 
            "number_of_replicas" : 2 
        }
    }
}'
```

> ```number_of_shards```의 기본값은 **5**입니다.<br>
> ```number_of_replicas```의 기본값은 **1**입니다. (주파편에 대한 각가 1개의 복제본을 의미합니다)

다음의 예제는 *twitter*라는 이름을 가지는 인덱스를 3개의 파편으로, 각각 2개의 복제본을 가지는 설정으로 [YAML](http://www.yaml.org/)방식을 사용하여 설정하는 예제입니다.
```
curl -XPUT 'localhost:9200/twitter?pretty' -d'
number_of_shards: 3
number_of_replicas: 2
'
```
동일하게 [JSON](http://www.json.org/)을 가지고 설정하는 예제입니다.
```
curl -XPUT 'localhost:9200/twitter?pretty' -d'
{
    "settings" : {
        "index" : {
            "number_of_shards" : 3,
            "number_of_replicas" : 2
        }
    }
}'
```
또는 간략하게 다음과 같이 할 수 있습니다.
```
curl -XPUT 'localhost:9200/twitter?pretty' -d'
{
    "settings" : {
        "number_of_shards" : 3,
        "number_of_replicas" : 2
    }
}'
```
> *settings* 섹션에 *index* 섹션을 꼭 명기할 필요는 없습니다.

인덱스를 생성할 때 사용되는 다른 많은 설정에 대해서는 [인덱스 모듈](index-modules.md)를 참고하시기 바랍니다.
## 매핑
인덱스를 생성할 때 1개 이상의 매핑을 정의할 수 있습니다.
```
curl -XPUT 'localhost:9200/test?pretty' -d'
{
    "settings" : {
        "number_of_shards" : 1
    },
    "mappings" : {
        "type1" : {
            "properties" : {
                "field1" : { "type" : "text" }
            }
        }
    }
}'
```
## 별칭
인덱스를 생성할 때 다수의 [별칭](indices-aliases.md)을 지정할 수 있습니다.