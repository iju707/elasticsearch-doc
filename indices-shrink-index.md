# 인덱스 축소
인덱스 축소 API는 기존의 인덱스를 줄어든 주파편으로 구성된 새로운 인덱스로 축소시키는 것 입니다. 요청되는 대상인덱스의 주파편 수는 기존 인덱스의 약수가 되어야 합니다. 예로 *8*개의 주파편을 가지는 인덱스는 *4*, *2*, *1*로 축소가 가능합니다. 또는 *15*개의 주파편을 가지는 인덱스는 *5*, *3*, *1*로 축소가 가능합니다. 소수의 파편으로 이뤄진 인덱스의 경우에는 무조건 *1*로만 축소가 가능합니다. 축소하기전에 인덱스의 모든 (주 또는 복제)파편이 동일한 노드에 존재해야 합니다.

축소는 다음의 과정으로 처리됩니다.
1. 원본 인덱스와 동일한 설정이지만 적은 개수의 주파편으로 구성된 새로운 인덱스를 생성합니다.
2. 원본에서 대상으로 세그먼트를 하드링크 방식으로 처리합니다. 만약 시스템이 하드링크 방식을 지원하지 않는 경우에는 대상 인덱스로 모든 세그먼트를 복사합니다. 대신 처리과정이 더 길어집니다.
3. 닫혀진 인덱스가 다시 열리는 것처럼 대상 인덱스를 복구합니다.

## 인덱스 축소 준비
인덱스를 축소하기 위해서는 읽기전용으로 변경을 하고 모든 (주 또는 복제본) 파편이 동일한 노드에 적재되어야 합니다. 그리고 인덱스의 상태는 ```green```으로 표시되어야 합니다.

다음 명령을 통하여 처리가 가능합니다.
```
curl -XPUT 'localhost:9200/my_source_index/_settings?pretty' -d'
{
  "settings": {
    "index.routing.allocation.require._name": "shrink_node_name", 
    "index.blocks.write": true 
  }
}'
```
> ```index.routing.allocation.require._name```에 적재하고자 하는 노드의 명을 *shrink_node_name*에 기입하면 됩니다. 추가적인 정보는 [파편 적재 필터링](shard-allocation-filtering.md)를 참조하시면 됩니다.<br>
> ```index.blocks.write```를 *true*로 변경하여 인덱스 삭제와 같은 메타데이터 변경은 가능하지만 쓰기는 안되는 상태로 변경합니다.

인덱스를 재구성하는데 약간의 시간이 소요됩니다. 처리과정은 [cat 복구 API](cat-recovery.md)를 이용하여 추적하거나 [클러스터 상태확인 API](cluster-health.md)의 ```wait_for_no_relocationg_shards``` 옵션을 통하여 모든 파편이 적재될 때 까지 대기할 수 있습니다.

## 인덱스 축소
다음은 ```my_source_index```를 ```my_target_index```로 축소하는 것 입니다.
```
curl -XPOST 'localhost:9200/my_source_index/_shrink/my_target_index?pretty'
```
축소처리가 시작될 때 까지 대기하지 않고 클러스터에 *target_index*가 추가되면 즉시 결과를 반환합니다.
> 인덱스 축소는 다음 조건을 충족해야합니다.
> * 대상 인덱스가 존재하지 않습니다.
> * 원본 인덱스는 대상 인덱스보다 주파편이 많아야 합니다.
> * 원본 인덱스의 주파편 개수는 대상 인덱스의 배수여야 합니다.
> * 인덱스는 1개 파편으로 구성될 때 한계치인 *2,147,483,519*개의 문서를 초과하지 않습니다.
> * 축소를 처리하는 노드에는 원본 인덱스의 전체복사가 가능할 정도의 디스크 용량이 있어야합니다.

```_shrink``` 인덱스 API는 [인덱스 생성API](indices-create-index)와 유사합니다. 동일하게 ```settings```, ```aliases``` 옵션을 제공합니다.
```
curl -XPOST 'localhost:9200/my_source_index/_shrink/my_target_index?pretty' -d'
{
  "settings": {
    "index.number_of_replicas": 1,
    "index.number_of_shards": 1, 
    "index.codec": "best_compression" 
  },
  "aliases": {
    "my_search_indices": {}
  }
}'
```
