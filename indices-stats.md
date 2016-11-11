# 인덱스 통계
인덱스 수준의 통계는 인덱스의 다른 명령에 의해 발생된 정보의 통계를 제공합니다. API는 인덱스 수준의 범위에서 통계를 제공합니다.(대부분의 통계는 노드 수준의 범위에서도 검색됩니다)

전체 인덱스에 대한 전반적인 수준의 통계를 보시려면 다음과 같이 하시면 됩니다.
```
curl -XGET 'localhost:9200/_stats?pretty'
```
특정 인덱스에 대한 경우에는 다음과 같습니다.
```
curl -XGET 'localhost:9200/index1,index2/_stats?pretty'
```
기본적으로 모든 통계정보가 반환됩니다. 필요한 경우에는 URI에 지정하여 출력도 가능합니다.
가능한 정보는 다음과 같습니다.

| 속성 | 내용 |
| -- | -- |
| ```docs``` | 문서 또는 삭제된 문서(아직 병합되지 않은 문서)의 수. 인덱스 갱신에 영향을 받음. |
| ```store``` | 인덱스 사이즈 |
| ```indexing``` | 색인 통계, 문서 단위의 통계를 제공하기 위해 쉼표(,)로 구분된 ```types``` 목록과 결합될 수 있습니다. |
| ```get``` | 조회 통계(조회 실패 포함) |
| ```search``` | 검색 통계(제안 통계 포함). 추가적으로 ```groups```라는 파라미터를 사용하여 통계 결과를 그룹핑(검색 동작은 1개 이상의 그룹에 속할 수 있음) 할 수 있습니다. ```groups``` 파라미터는 쉼표(,) 방식의 목록을 지원합니다. 전체의 경우에는 ```_all```을 사용하시면 됩니다. |
| ```segments``` | 열린 세그먼트의 메모리 사용량을 확인할 수 있습니다. ```include_segment_file_sizes``` 설정을 사용하면 루씬 인덱스 파일 각각의 디스크 사용량까지 확인할 수 있습니다. |
| ```completion``` | 완성 제안 통계 |
| ```fielddata``` | 필드 데이터 통계 |
| ```flush``` | 정리 통계 |
| ```merge``` | 병합 통계 |
| ```request_cache``` | [파편 요청 캐시](shard-request-cache.md) 통계 |
| ```refresh``` | 갱신 통계 |
| ```warmer``` | Warmer 통계 (더 이상 사용은 안됨) |
| ```translog``` | 트랜잭션로그 통계 |

일부 통계는 필드별로 세분화 해서 볼 수 있습니다. 쉼표(,) 방식으로 목록을 나열하시면 됩니다. 기본적으로 모든 필드는 다음을 포함합니다.

| 속성 | 내용 |
| -- | -- |
| ```fields``` | 통계에 포함된 필드 목록. 따로 필드를 정의하지 않아도 기본적으로 포함되는 내용입니다. |
| ```completion_fields``` | 완성 제안 통계에 포함된 필드 목록 |
| ```fielddata_fields``` | 필드 데이터 통계에 포함된 필드 목록 |

일부 예제입니다.
```
# Get back stats for merge and refresh only for all indices
curl -XGET 'localhost:9200/_stats/merge,refresh?pretty'
# Get back stats for type1 and type2 documents for the my_index index
curl -XGET 'localhost:9200/my_index/_stats/indexing?types=type1,type2&pretty'
# Get back just search stats for group1 and group2
curl -XGET 'localhost:9200/_stats/search?groups=group1,group2&pretty'
```
위 통계는 인덱스 단위로 ```primaries```(주파편에 대한 통계), ```total```(모든 파편에 대한 통계)의 묶음으로 제공됩니다.

파편 단위로 통계가 필요한 경우에는 ```level``` 파라미터를 ```shards```로 하시면 됩니다.

파편이 클러스터간 이동할 때, 다른 노드에서 생성되었던 통계정보는 초기화 됩니다. 반대로 생각하면, 파편을 남겨놓는다면 통계정보 또한 해당 노드에 유지됩니다.