# 인덱스 닫기/열기 {#indices-open-close}

인덱스 닫기/열기 API는 인덱스를 닫고 향후에 열도록 하는 것 입니다. 닫혀진 인덱스는 클러스터에 거의 영향을 미치지 않습니다.(단, 인덱스의 메타데이터를 관리하는 것은 제외) 그리고, 모든 읽기/쓰기 명령이 제한됩니다. 닫혀진 인덱스는 열기 과정을 통하여 원래상태로 복구할 수 있습니다.

REST API는 ```/{index}/_close```와 ```/{index}/_open```입니다.

```
curl -XPOST 'localhost:9200/my_index/_close?pretty'
curl -XPOST 'localhost:9200/my_index/_open?pretty'
```

동시에 여러개의 인덱스를 열고 닫을 수 있습니다. 없는 인덱스를 가리켜서 처리할 경우에는 오류가 발생됩니다. 이 경우 ```ignore_unavailable=true``` 옵션을 통하여 무시할 수 있습니다.

모든 인덱스를 ```_all``` 또는 ```*```을 사용하여 한번에 열고 닫을 수 있습니다.

삭제와 유사하게 모든 인덱스를 와일드카드나 ```_all```을 사용하여 가리키는 것을 ```action.destructive_requires_name```을 *true*로 설정하여 방지할 수 있습니다. 클러스터 설정 변경 API를 통하여 처리할 수 있습니다.

닫혀진 인덱스는 삭제가 되는 것이 아니기 때문에 디스크용량을 차지하는 문제점이 있습니다. 그래서 인덱스 닫기를 못하게 막고자 할 경우에는 ```cluster.indices.close.enable```옵션을 *false*로 변경하면 됩니다. 기본값은 *true*입니다.

## 활성파편 대기 {#_wait_for_active_shards}

인덱스 열기는 해당 파편을 할당해야하므로, 인덱스 생성에 ```wait_for_active_shards```를 설정하면 인덱스 열기를 잘 수 행할 수 있습니다. 인덱스 열기 API에 설정된 ```wait_for_active_shards``` 옵션의 기본값은 0이며, 이것은 해당 파편이 할당되는 것을 기다리지 않는다는 것 입니다.

> 2018-04-05 : 6.2 버전 업데이트