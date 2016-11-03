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