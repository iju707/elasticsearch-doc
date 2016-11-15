# 동기화된 정리
Elasticsearch에서는 각각의 파편에 대한 색인활동을 추적합니다. 5분동안 아무런 색인명령을 받지 않은 파편은 자동으로 비활성화로 변경이 됩니다. 이것은 Elasticsearch에게 불필요한 파편 자원을 줄일 수 있게 하고, ```synced_flush```라고 불리는 특수한 정리작업을 가능하게 해줍니다. 동기화된 정리는 일반적인 정리 작업을하고 모든 파편에 대하여 동기화 식별자(*sync_id*)를 추가합니다.

동기화 식별자는 색인 작업이 없을 때 추가되었으므로, 두개의 파편 루씬 인덱스가 동일한지 확인할 때 사용할 수 있습니다. 이렇게 동기화 식별자를 이용하여 빠르게 비교하는 것은 복구나 재시작 중에 가장 무거운 처리과정을 생략할 수 있게 해줍니다. 이 경우 세그먼트 파일을 복사할 필요가 없고 복구과정 중 트랜젝션로그 재실행을 바로 할 수 있습니다. 동기화 식별자가 할당되는건 정리작업이 병행된다는 것이므로 다시 생각해보면 트랜젝션로그는 없을테니 복구과정의 속도향상이 이뤄지게 되는 것 입니다.

시간기반 데이터와 같이 전혀 또는 거의 업데이트가 이뤄지지 않는 인덱스에 대하여 매우 유용합니다. 보통 동기화 식별자가 없이는 복구과정이 오래걸릴 만큼의 수 많은 인덱스를 만들게 됩니다.

만약 파편에 마킹이 되어있는지 아닌지 확인하려면 [인덱스 통계 API](indices-stats.md)에서 ```commit``` 부분을 보시면 됩니다.
```
curl -XGET 'localhost:9200/twitter/_stats/commit?level=shards&pretty'
```
응답결과는 다음과 비슷할 것 입니다.
```json
{
   ...
   "indices": {
      "twitter": {
         "primaries": {},
         "total": {},
         "shards": {
            "0": [
               {
                  "routing": {
                     ...
                  },
                  "commit": {
                     "id": "te7zF7C4UsirqvL6jp/vUg==",
                     "generation": 2,
                     "user_data": {
                        "sync_id": "AU2VU0meX-VX2aNbEUsD" ,
                        ...
                     },
                     "num_docs": 0
                  }
               }
               ...
            ],
            ...
         }
      }
   }
}
```
> ```user_data```의 ```sync_id```가 동기화 식별자임을 알 수 있습니다.

## 동기화된 정리 API
동기화된 정리 API는 관리자에게 수동으로 할 수 있도록 해줍니다. 색인을 멈추고 자동으로 인덱스의 동기화된 정리를 위해 5분 정도의 대기시간을 원치않는 계획된 클러스터 재시작에서 유용합니다.

유용하지만 API에 대하여 두가지 주의사항이 있습니다.
1. 동기화된 정리는 비용이 많이 드는 작업입니다. 진행중인 색인 작업이 있다면 그 작업이 특정 파편에 대한 동기화된 정리의 실패원인이 됩니다. 다시 말해 만약 파편이 동기화된 정리작업 중이라면 다른 파편은 할 수 없다는 것 입니다. 아래에 더 자세히 알아보겠습니다.
2. ```sync_id```는 파편이 다시 정리된다면 삭제됩니다. 정리작업이 동기화 식별자가 저장된 루씬 커밋 위치를 교체하기 때문입니다. 트랜젝션로그의 커밋되지않은 동작은 동기화 식별자를 지우지 않습니다. 실사용에서는 인덱스의 색인동작은 언제든 Elasticsearch에 의해 발생된 정리작업으로 인하여 동기화 식별자를 삭제하게 됩니다.

> 색인중에 동기화된 정리를 요청해도 상관은 없습니다. 만약 파편이 대기상태면 성공할 것이고 그렇지 않으면 실패할 것 입니다. 성공한 파편은 더 빠른 복구시간을 가지게 될 것 입니다.

```
curl -XPOST 'localhost:9200/twitter/_flush/synced?pretty'
```
응답결과에는 얼마만큼의 파편이 동기화된 정리에 성공 또는 실패하였는지에 대한 정보가 있습니다.

아래 예제는 두개의 파편과 한개의 복제본으로 구성된 인덱스의 동기화된 정리가 성공한 경우입니다.
```json
{
   "_shards": {
      "total": 2,
      "successful": 2,
      "failed": 0
   },
   "twitter": {
      "total": 2,
      "successful": 2,
      "failed": 0
   }
}
```
아래는 진행중인 동작으로 인하여 한개의 파편그룹이 오류가 발생한 경우입니다.
```json
{
   "_shards": {
      "total": 4,
      "successful": 2,
      "failed": 2
   },
   "twitter": {
      "total": 4,
      "successful": 2,
      "failed": 2,
      "failures": [
         {
            "shard": 1,
            "reason": "[2] ongoing operations on primary"
         }
      ]
   }
}
```
> 위 오류는 색인 작업이 동시에 진행되어 동기화된 정리가 실패했다는 것을 보여줍니다. HTTP 응답코드는 ```404 CONFLICT```으로 출력됩니다.

때때로 실패는 파편본에 한정될 수 있습니다. 실패한 사본은 빠른 복구에 적합하지 않지만 성공한 사본은 계속 복구를 진행할 수 있습니다. 위 경우 다음과 같은 응답결과가 발생합니다.
```json
{
   "_shards": {
      "total": 4,
      "successful": 1,
      "failed": 1
   },
   "twitter": {
      "total": 4,
      "successful": 3,
      "failed": 1,
      "failures": [
         {
            "shard": 1,
            "reason": "unexpected error",
            "routing": {
               "state": "STARTED",
               "primary": false,
               "node": "SZNr2J_ORxKTLUCydGX4zA",
               "relocating_node": null,
               "shard": 1,
               "index": "twitter"
            }
         }
      ]
   }
}
```
> 파편본에 대한 동기화된 정리가 실패하는 경우 HTTP 응답코트는 ```409 CONFLICT```로 발생합니다.

동기화된 정리 API는 한번의 요청으로 한개 이상의 인덱스를 처리할 수 있습니다. 또는 ```_all```을 사용하여 전체 인덱스 처리도 가능합니다.
```
curl -XPOST 'localhost:9200/kimchy,elasticsearch/_flush/synced?pretty'
curl -XPOST 'localhost:9200/_flush/synced?pretty'
```