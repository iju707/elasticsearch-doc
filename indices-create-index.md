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

> ```number_of_shards```의 기본값은 **5**입니다.
> ```number_of_replicas```의 기본값은 **1**입니다. (주파편에 대한 각가 1개의 복제본을 의미합니다)

다음의 예제는 *twitter*라는 이름을 가지는 인덱스를 3개의 파편으로, 각각 2개의 복제본을 가지는 설정으로 [YAML](http://www.yaml.org/)방식을 사용하여 설정하는 예제입니다.

```
curl -XPUT 'localhost:9200/twitter?pretty' -d'
number_of_shards: 3
number_of_replicas: 2'
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

```
curl -XPUT 'localhost:9200/test?pretty' -d'
{
    "aliases" : {
        "alias_1" : {},
        "alias_2" : {
            "filter" : {
                "term" : {"user" : "kimchy" }
            },
            "routing" : "kimchy"
        }
    }
}'
```

## 파편 활성화 대기 {#create-index-wait-for-active-shards}

기본적으로 인덱스 생성은 각각의 파편에 대한 첫 복제가 시작되거나 타임아웃이 발생하였을 때 클라이언트로 응답을 보냅니다. 인덱스 생성에 대한 응답이 어떤일이 발생하였는지 알려줍니다.

```json
{
    "acknowledged": true,
    "shards_acknowledged": true
}
```

```acknowledged```는 클러스터에 인덱스 생성이 성공되었는지를 알려주며, ```shards_acknowledged```는 타임아웃이 발생하기 전에 각각의 인덱스 파편에 대한 복사가 시작되었는지를 알려줍니다. 인덱스 생성이 성공하였어도, ```acknowledged```나 ```shards_acknowledged```는 *false*가 발생할 수 있습니다. 간단히 말해 타임아웃 전에 수행이 되었는지 여부로 생각하시면 됩니다. 만약 ```acknowledged```가 *false*일 경우 언젠가는 만들어지겠지만 신규 인덱스 생성에 대한 클러스터 상태 갱신이 늦어져서 타임아웃이 발생하였다는 것 입니다. 또한 ```shards_acknowledged```가 *false*일 경우에는 인덱스 생성이 정상적으로 반영이 되었어도(```acknowledged=true```) 필요한 파편의 복사가 시작되기 전에 타임아웃이 발생했다는 것 입니다.

설정을 이용해서 주파편에 대해서만 기다리는 것을 다른 파편생성까지 대기할 수 있도록 변경할 수 있습니다. ```index.write.wait_for_active_shards```를 이용하면 되며, 이후 쓰기에 관련된 모든 동작에 적용이 됩니다.

```
curl -XPUT 'localhost:9200/test?pretty' -d'
{
    "settings": {
        "index.write.wait_for_active_shards": "2"
    }
}'
```

또는 Request Parameter 방식으로도 할 수 있습니다.

```
curl -XPUT 'localhost:9200/test?wait_for_active_shards=2&pretty'
```

```wait_for_active_shards```에 대한 상세한 정보는 [여기](doc-index_.md#index-wait-for-active-shards)를 참고하시기 바랍니다.

> 2018-04-05 : 6.2 버전 업데이트