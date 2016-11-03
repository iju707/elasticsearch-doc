# 인덱스 생성
*customer*라는 이름을 가지는 인덱스를 만들고 생성된 인덱스 목록을 확인해보겠습니다.
```
curl -XPUT 'localhost:9200/customer?pretty&pretty'
curl -XGET 'localhost:9200/_cat/indices?v&pretty'
```
첫번째 명령은 PUT을 이용하여 *customer*라는 이름의 인덱스를 생성한 것 입니다. 뒤에 붙는 *pretty*의 경우에는 JSON 응답을 보기 좋게 바꿔주는 옵션입니다.

응답결과는 다음과 같습니다.
```
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   customer 95SQ4TSUT7mWBT7VNHH67A   5   1          0            0       260b           260b
```
두번째 명령은 현재 우리가 *customer*라는 이름을 가지는 1개의 인덱스를 가지고 있고 5개의 주파편과 1개의 복제본(기본값)을 가지고 있으며 0개의 문서가 있다는 것을 알 수 있습니다.

*customer* 인덱스의 상태가 *yellow*인 것을 알 수 있습니다. 이전에 이야기했듯 *yellow* 상태는 일부 복제본이 아직 할당되지 않은 상태입니다. Elasticsearch가 기본적으로 1개의 인덱스에 대한 1개의 복제본을 생성하기 때문에 발생한 것 입니다. 우리가 지금 1개의 노드만 기동하고 있기 때문에 클러스터에 노드를 추가하기 전까지는 복제본을 생성할 수 없는 상태인 것 입니다. 나중에 두번째 노드가 추가되고 복제본이 생성된다면 인덱스에 대한 상태가 *green*으로 변경될 것 입니다. 