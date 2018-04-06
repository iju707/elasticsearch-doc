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

> 이 인덱스에 인덱스 삭제와 같은 메타데이터 변경은 가능하지만 쓰기 동작을 못하도록 설정합니다.

## 인덱스 분할 {#_splitting_an_index}

```my_source_index```라는 인덱스를 ```my_target_index```라는 새로운 인덱스로 분할하고자 하는 경우 다음과 같습니다.

```
curl -XPOST 'localhost:9200/my_source_index/_split/my_target_index?pretty' -H 'Content-Type: application/json' -d'
{
  "settings": {
    "index.number_of_shards": 2
  }
}'
```

위 요청에 대한 응답은 신규 인덱스가 클러스터 상태에 추가되면 바로 반환 됩니다. - 분할 동작이 시작될 때까지 기다리진 않습니다.

> **중요**
> 인덱스는 다음의 요구사항을 만족할 때만 분할처리 됩니다.
> * 신규 인덱스는 존재하면 안됩니다.
> * 인덱스는 신규 인덱스보다 적은 주파편을 가져야 합니다.
> * 신규 인덱스의 주 파편수는 기존 인덱스의 주 파편수에 대한 배수가 되어야 합니다.
> * 복제 처리를 진행할 노드는 기존 인덱스의 두번 복사될 만큼의 충분한 공간을 가지고 있어야 합니다.

```_split``` API는 [인덱스 생성 API](indices-create-index.md)와 유사하며, 신규 인덱스에 ```settings```와 ```aliases``` 파라미터를 지원합니다.

```
curl -XPOST 'localhost:9200/my_source_index/_split/my_target_index?pretty' -H 'Content-Type: application/json' -d'
{
  "settings": {
    "index.number_of_shards": 5 
  },
  "aliases": {
    "my_search_indices": {}
  }
}'
```

> ```index.number_of_shards```는 신규 인덱스의 파편수 입니다. 대상 인덱스의 파편수에 배수가 되어야 합니다.

> **참고** ```_split``` 요청에 매핑관련은 정의될 수 없습니다. ```index.analysis.*```와 ```index.similarity.*``` 설정은 대상 인덱스의 설정으로 덮어지게 됩니다.

## 분할 처리 모니터링 {#_monitoring_the_split_process}

분할 처리는 [_cat recovery API](cat-recovery.md)로 모니터링할 수 있습니다. 또는 [클러스터 상태 API](cluster-health.md)를 가지고 ```wait_for_status```를 ```yellow```로 설정해서 모든 주파편이 할당될 때까지 대기할 수 있습니다.

```_split``` API는 다른 파편이 할당되기전, 클러스터 상태에 신규 인덱스가 추가되면 바로 결과를 반환합니다. 이때 모든 파편은 ```unassigned``` 상태가 됩니다. 다른 이유로 신규 인덱스가 할당이 안되면 해당 노드에 할당될 때까지 주파편은 ```unassigned``` 상태로 남게 됩니다.

주 파편이 할당되면, ```initializing``` 상태로 변경이 되며, 분할 처리가 시작됩니다. 분할이 완료되면 파편은 ```active```로 바뀌게 됩니다. 그럼 Elasticsearch는 다른 복제본 할당을 시도하며, 주 파편을 다른 노드에 이동할지 결정합니다.

## 