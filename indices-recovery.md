# 인덱스 복구
인덱스 복구 API는 지속적인 인덱스 파편 복구 정보를 제공합니다. 복구 상태는 특정 인덱스들 또는 클러스터 수준으로 제공되어 집니다.

예로들어, 다음 명령은 ```index1```과 ```index2``` 두 인덱스의 복구 정보를 보여줍니다.
```
curl -XGET 'localhost:9200/index1,index2/_recovery?human&pretty'
```
만약 클러스터 수준으로 보고 싶을 경우에는 인덱스 부분을 제외하고 사용하시면 됩니다.
```
curl -XGET 'localhost:9200/_recovery?human&pretty'
```
응답결과는 다음과 같습니다.
```json
{
  "index1" : {
    "shards" : [ {
      "id" : 0,
      "type" : "SNAPSHOT",
      "stage" : "INDEX",
      "primary" : true,
      "start_time" : "2014-02-24T12:15:59.716",
      "start_time_in_millis": 1393244159716,
      "total_time" : "2.9m",
      "total_time_in_millis" : 175576,
      "source" : {
        "repository" : "my_repository",
        "snapshot" : "my_snapshot",
        "index" : "index1"
      },
      "target" : {
        "id" : "ryqJ5lO5S4-lSFbGntkEkg",
        "hostname" : "my.fqdn",
        "ip" : "10.0.1.7",
        "name" : "my_es_node"
      },
      "index" : {
        "size" : {
          "total" : "75.4mb",
          "total_in_bytes" : 79063092,
          "reused" : "0b",
          "reused_in_bytes" : 0,
          "recovered" : "65.7mb",
          "recovered_in_bytes" : 68891939,
          "percent" : "87.1%"
        },
        "files" : {
          "total" : 73,
          "reused" : 0,
          "recovered" : 69,
          "percent" : "94.5%"
        },
        "total_time" : "0s",
        "total_time_in_millis" : 0
      },
      "translog" : {
        "recovered" : 0,
        "total" : 0,
        "percent" : "100.0%",
        "total_on_start" : 0,
        "total_time" : "0s",
        "total_time_in_millis" : 0,
      },
      "start" : {
        "check_index_time" : "0s",
        "check_index_time_in_millis" : 0,
        "total_time" : "0s",
        "total_time_in_millis" : 0
      }
    } ]
  }
}
```
위 결과에서 단일 인덱스에서 단일 파편에 대한 복구된 것을 알 수 있습니다. 위 경우 복구의 원본은 스냅샷 저장소이고, 대상은 ```my_es_node```라는 이름을 가진 노드란 것을 확인할 수 있습니다.

추가적으로, 복구된 파일의 수와 비율, 바이트의 수와 비율을 표시해줍니다.

일부 경우에는 더 상세한 정보가 필요할 수 있습니다. ```detailed=true```라는 옵션을 추가하시면 물리적 파일정보까지 표시해줍니다.
```
GET _recovery?human&detailed=true
```
응답결과는 다음과 같습니다.
```json
{
  "index1" : {
    "shards" : [ {
      "id" : 0,
      "type" : "STORE",
      "stage" : "DONE",
      "primary" : true,
      "start_time" : "2014-02-24T12:38:06.349",
      "start_time_in_millis" : "1393245486349",
      "stop_time" : "2014-02-24T12:38:08.464",
      "stop_time_in_millis" : "1393245488464",
      "total_time" : "2.1s",
      "total_time_in_millis" : 2115,
      "source" : {
        "id" : "RGMdRc-yQWWKIBM4DGvwqQ",
        "hostname" : "my.fqdn",
        "ip" : "10.0.1.7",
        "name" : "my_es_node"
      },
      "target" : {
        "id" : "RGMdRc-yQWWKIBM4DGvwqQ",
        "hostname" : "my.fqdn",
        "ip" : "10.0.1.7",
        "name" : "my_es_node"
      },
      "index" : {
        "size" : {
          "total" : "24.7mb",
          "total_in_bytes" : 26001617,
          "reused" : "24.7mb",
          "reused_in_bytes" : 26001617,
          "recovered" : "0b",
          "recovered_in_bytes" : 0,
          "percent" : "100.0%"
        },
        "files" : {
          "total" : 26,
          "reused" : 26,
          "recovered" : 0,
          "percent" : "100.0%",
          "details" : [ {
            "name" : "segments.gen",
            "length" : 20,
            "recovered" : 20
          }, {
            "name" : "_0.cfs",
            "length" : 135306,
            "recovered" : 135306
          }, {
            "name" : "segments_2",
            "length" : 251,
            "recovered" : 251
          },
           ...
          ]
        },
        "total_time" : "2ms",
        "total_time_in_millis" : 2
      },
      "translog" : {
        "recovered" : 71,
        "total_time" : "2.0s",
        "total_time_in_millis" : 2025
      },
      "start" : {
        "check_index_time" : 0,
        "total_time" : "88ms",
        "total_time_in_millis" : 88
      }
    } ]
  }
}
```
실제 복구된 파일과 사이즈 정보의 상세 목록을 보여줍니다.

또한 각각 복구 단계(인덱스 검색, 트랜잭션로그 재실행, 인덱스 시작)에 걸린 밀리초 단위의 시간도 보여줍니다.

위에서는 복구가 모두 완료된 인덱스에 대한 정보만 있었습니다. 모든 복구는 진행중이던, 완료되었던 클러스터 통계에 포함되며, 언제든 모니터링 할 수 있습니다. 만약 ```active_only=true``` 옵션을 사용하시면 진행중인 복구에 대해서만 볼 수 있습니다.

아래 복구에 관련된 옵션 목록 입니다.

| 파라미터 |  내용 |
| -- | -- |
| ```detailed``` | 상세보기 입니다. 물리적인 파일의 정보를 보고자 할 경우 사용하시면 됩니다. 기본값 : *false* |
| ```active_only``` | 현재 수행중인 복구에 대한 정보만 표출합니다. 기본값 : *false* |

출력결과에 대한 상세정보는 다음과 같습니다.

| 속성명 | 내용 |
| -- | -- |
| ```id``` | 파편 ID |
| ```type``` | 복구 유형 (*store, snapshot, replica, relocating*) |
| ```stage``` | 복구 단계<br><ul><li>init : 복구가 아직 진행안됨</li><li>index : 인덱스의 메타데이터를 읽고 원본에서 대상으로 복사</li><li>start : 엔진 시작, 사용하기 위해 인덱스을 열기</li><li>translog : 트랜잭션 로그 재실행</li><li>finalize : 최종 복구 마무리</li><li>done : 복구 완료</li></ul> |
| ```primary``` | 만약 파편이 주파편이면 *true* 아니면 *false* |
| ```start_time``` | 복구 시작시간 |
| ```stop_time``` | 복구 완료시간 |
| ```total_time_in_millis``` | 밀리초 단위 파편 복구 전체 시간 |
| ```source``` | 복구 원본 <br><ul><li>스냅샷으로 부터 복구할 경우 저장소 설명</li><li>다른 경우에는 원본 노드의 설명</li></ul> |
| ```target``` | 대상 노드 |
| ```index``` | 물리적 인덱스 복구에 관련된 통계 |
| ```translog``` | 트랜잭션 복구에 관련된 통계 |
| ```start``` | 인덱스를 열고 시작하기 까지 시간에 관련된 통계 |
