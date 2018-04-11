# 인덱스 설정 수정 {#indices-update-settings}

실시간으로 인덱스단위의 설정을 변경할 수 있습니다.

모든 인덱스에 대해 수정할 경우에는 ```/_settings```를, 특정 인덱스(들)에 대한 수정일 경우에는 ```{index}/_settings```를 사용하여 요청할 수 있습니다. Request Body에는 수정할 설정정보를 포함하면 됩니다. 예와 같이 요청바디에 업데이트할 설정을 포함하시면 됩니다.

```
curl -XPUT 'localhost:9200/twitter/_settings?pretty' -H 'Content-Type: application/json' -d'
{
    "index" : {
        "number_of_replicas" : 2
    }
}'
```

설정을 기본값으로 되돌리고 싶은 경우에는 ```null```값을 전달하면 됩니다.

```
curl -XPUT 'localhost:9200/twitter/_settings?pretty' -H 'Content-Type: application/json' -d'
{
    "index" : {
        "refresh_interval" : null
    }
}'
```

인덱스들에 대한 실시간적으로 동적 수정이 가능한 설정정보는 [인덱스 모듈](index-modules.md)에서 확인할 수 있습니다. 기존의 설정이 변경되는 것을 방지하고자 할때는, ```preserve_existing``` 파라미터를 ```true```로 요청하면 됩니다.

## 대량 색인 작업 {#bulk}

인덱스 설정 변경 API의 동적 설정변경을 통하여 색인에 대한 대량작업을 처리할 수 있습니다. 대량 작업 전에 다음과 같이 설정을 변경합니다.

```
curl -XPUT localhost:9200/test/_settings -d '{
    "index" : {
        "refresh_interval" : "-1"
    }
}'
```

(또는 경우에 따라서 인덱스를 다른 복제본 없이 한 뒤, 끝나고 나서 복제본을 추가하시면 됩니다)

그리고 나서 대량 색인작업 후 다시 기본값으로 원복을 하면 됩니다.

```
curl -XPUT localhost:9200/test/_settings -d '{
    "index" : {
        "refresh_interval" : "1s"
    }
}'
```

마지막으로 강제 병합처리를 호출하면 됩니다.

```
curl -XPOST 'http://localhost:9200/test/_forcemerge?max_num_segments=5'
```

## 인덱스 분석정보 수정 {#update-settings-analysis}

인덱스에 대한 새로운 [분석기](analysis.md)를 설정할 수 있습니다. 그러나 이것을 하기전에 [인덱스를 닫은 뒤](indices-open-close.md) 설정을 하고 다시 [인덱스를 여는](indices-open-close.md) 과정이 필요합니다.

예로 ```myindex```에 ```content```라는 분석기를 설정할 경우 다음과 같이 하시면 됩니다.

```
curl -XPOST 'localhost:9200/myindex/_close'

curl -XPUT 'localhost:9200/myindex/_settings' -d '{
  "analysis" : {
    "analyzer":{
      "content":{
        "type":"custom",
        "tokenizer":"whitespace"
      }
    }
  }
}'

curl -XPOST 'localhost:9200/myindex/_open'
```

> 2018-04-11 : 6.2 버전 업데이트