# 인덱스 분할 {#indices-split-index}

인덱스 분할 API는 기존의 인덱스를 신규 인덱스로 분할하게 합니다. 각각의 원본 주파편이 새로운 인덱스의 2개 이상의 주파편으로 분할이 됩니다.

> **주의** `_split` API는 향후 분할을 위하여 대상이 되는 인덱스 생성시 `number_of_routing_shards`를 설정해야 합니다. 이 요구사항은 Elasticsearch 7.0에서 삭제될 예정입니다.

인덱스가 분할될 수 있는 횟수(와 각각의 원본 파편이 분할될 수 있는 수)는 ```index.number_of_routing_shards``` 설정에따라 결정됩니다. 라우팅 파편의 수는 일관된 해싱방법으로 문서를 파편상 분산시키는데 사용되는 해싱공간을 지칭합니다. 예로 들어, 5개의 파편 인덱스가 ```number_of_routing_shards```를 30(5 x 2 x 3)으로 설정되어있으면, 2 또는 3으로 분할 될 수 있습니다. 다시말해 다음과 같습니다.

* 5 -> 10 -> 30 (2개로 분할, 3개로 분할)
* 5 -> 15 -> 30 (3개로 분할, 2개로 분할)
* 5 -> 30 (6개로 분할)

분할은 다음과 같이 동작합니다.

1. 새로운 인덱스를 기존 인덱스와 동일한 설정으로 만들되, 주파편의 수를 크게하여 생성합니다.
2. 세그먼트를 원본 인덱스에서 신규 인덱스로 하드링킹 합니다. (만약 시스템이 하드링킹을 지원하지 않으면, 모든 세그먼트를 신규 인덱스로 복사를 합니다. 이럴경우 처리과정의 시간소요가 증가하게 됩니다)
3. 로우레벨 파일이 생성되면 모든 문서는 다시 해시처리가 되고 다른 파편에 속한 문서를 삭제합니다.
4. 신규 인덱스를 닫고 다시 여는 과정으로 복구를 합니다.

## 인덱스 분할 준비 {#_preparing_an_index_for_splitting}

인덱스를 라우팅 파편 요소를 포함하여 생성합니다.

```
curl -XPUT 'localhost:9200/my_source_index?pretty' -H 'Content-Type: application/json' -d'
{
    "settings": {
        "index.number_of_shards" : 1,
        "index.number_of_routing_shards" : 2 
    }
}'
```

> 인덱스를 2개의 파편으로 분할 할 수 있게 합니다. 다른 의미로, 분할 동작을 한번만 수행 가능합니다.

분할을 위하여 인덱스를 읽기 전용으로 변경해야하며, [상태](cluster-health.md)를 ```green```이 되어야 합니다.

다음 요청으로 읽기전용으로 바꿀 수 있습니다.

```
curl -XPUT 'localhost:9200/my_source_index/_settings?pretty' -H 'Content-Type: application/json' -d'
{
  "settings": {
    "index.blocks.write": true 
  }
}'
```

> 