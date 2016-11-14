# 인덱스 파편 저장소
인덱스들의 파편본들에 대한 저장정보를 제공합니다. 저장 정보는 어떤 노드에 파편본들의 존재유무, 파편본 할당ID(각각 파편본에 대한 유일식별자), 파편 인덱스를 열거나 엔진 실패로 발생한 예외상황을 제공합니다.

기본적으로 적어도 1개의 미할당 본을 가지고 있는 파편에 대한 정보가 표시됩니다. 만약 클러스터 상태가 *yellow*이면 한개 이상의 미할당 복제본을 가지고 있다는 것 입니다. 만약 클러스터가 *red* 상태이면 미할당된 주파편이 있다는 것 입니다.

특정 인덱스 또는 인덱스들, 전체에 대한 API 요청은 다음과 같습니다.
```
curl -XGET 'http://localhost:9200/test/_shard_stores'
curl -XGET 'http://localhost:9200/test1,test2/_shard_stores'
curl -XGET 'http://localhost:9200/_shard_stores'
```
파편에 대한 범주는 ```status``` 파라미터를 통해서 변경할 수 있습니다. 기본적으로 *yellow*, *red*가 사용됩니다. *yellow*는 1개 이상의 미할당된 복제본이 있는 파편에 대한 정보목록이며 *red*는 1개 이상의 미할당된 주파편이 있는 파편에 대한 정보목록입니다. 만약 *green*을 사용하면 전체 파편에 대한 정보를 볼 수 있습니다.
```
curl -XGET 'http://localhost:9200/_shard_stores?status=green'
```
응답결과는 인덱스별, 파편 ID별로 그룹핑 되어 표시됩니다.
```json
{
    ...
   "0": { 
        "stores": [ 
            {
                "sPa3OgxLSYGvQ4oPs-Tajw": { 
                    "name": "node_t0",
                    "transport_address": "local[1]",
                    "attributes": {
                        "mode": "local"
                    }
                },
                "allocation_id": "2iNySv_OQVePRX-yaRH_lQ", 
                "legacy_version": 42, 
                "allocation" : "primary" | "replica" | "unused", 
                "store_exception": ... 
            },
            ...
        ]
   },
    ...
}
```
> 자료의 Key는 저장 정보의 파편ID를 가리킵니다.
> 
> ```stores```에는 파편의 모든 본에 대한 정보를 가지고 있습니다.
>
> *sPa3OgxLSYGvQ4oPs-Tajw* 부분은 파편본이 저장된 노드의 정보입니다.
> 
> ```allocation_id```는 파편본에 대한 할당 ID 입니다.
> 
> ```legacy_version```은 파편본의 버전입니다. (레가시 파편본에서만 가능하며 현재 Elasticsearch 버전에서는 활성화되어 있지 않습니다)
> 
> ```allocation```은 파편본의 상태를 나타내며, 주파편/복제본/미사용 등으로 나타납니다.
> 
> ```store_exception```은 엔진단에서 또는 인덱스 파편을 열기하는데 발생한 예외입니다.