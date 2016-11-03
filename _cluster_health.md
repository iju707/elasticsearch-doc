# 클러스터 상태확인
우리의 클러스터가 어떻게 동작하고 있는지 기본적인 상태확인부터 시작해보겠습니다. 여기서는 curl 명령을 사용하여 진행할 것 이며, 필요에 따라 HTTP/REST 명령를 지원하는 어떤 방법이든 사용해도 상관없습니다. 일단, 동일한 서버에 Elasticsearch를 기동하고 명령창이 실행된 상태라고 가정하겠습니다.

상태확인을 위해서, [_cat API](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat.html)를 사용하면 됩니다.
```
curl -XGET 'localhost:9200/_cat/health?v&pretty'
```
응답결과는 다음과 같습니다.
```
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1475247709 17:01:49  elasticsearch green           1         1      0   0    0    0        0             0                  -                100.0%
```
우리의 클러스터명이 *elasticsearch*라는 것을 확인할 수 있고 정상적으로 잘 동작하고 있다는 *green* 상태입니다.

상태확인 메시지를 통해서 status 항목에 *green*, *yellow*, *red*라는 정보를 볼 수 있습니다. 
* *green*은 모든 기능이 정상적으로 동작하고 있다는 것입니다.
* *yellow*는 전체적인 기능은 수행하고 있으나 일부 복제본이 아직 할당되지 않은 상태입니다.
* *red*는 어떤 이유로 인하여 데이터를 사용할 수 없는 상태입니다.

상태가 *red*라고 해서 사용 못하는 것이 아닌, 부분적인 기능(사용가능한 파편을 사용하여 검색요청을 수행)을 할 수 있는 상태입니다. 그렇지만 데이터 손실을 방지하기 위하여 가능하면 빨리 조치를 취해야할 것 입니다.

추가적인 내용을 보면 1개의 노드를 가지고 있고, 데이터가 없는 상태이기 때문에 0개의 파편이 있습니다. 실수로 동일 컴퓨터에서 1개 이상의 노드를 구동했다고 한다면, 클러스터 명은 기본값(elasticsearch)으로 되어있고 Elasticsearch가 유니캐스트방식으로 동일 장비에 다른 노드가 있는지 확인하여 모든 것을 단일 클러스터에 합류시킵니다. 이때는 응답결과에 1개 이상의 노드가 보일 것 입니다.

클러스터에 있는 노드 정보 또한 확인할 수 있습니다.
```
curl -XGET 'localhost:9200/_cat/nodes?v&pretty'
```
응답결과는 다음과 같습니다.
```
ip        heap.percent ram.percent cpu load_1m load_5m load_15m node.role master name
127.0.0.1           10           5   5    4.46                        mdi      *      PB2SGZY
```
여기를 보면 *PB2SGZY* 이름을 가지는 1개의 노드가 클러스터에 구성되어있음을 알 수 있습니다.