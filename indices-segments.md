# 인덱스 세그먼트
파편 수준의 생성된 루씬 인덱스에 대한 저수준 세그먼트 정보를 제공합니다. 파편과 인덱스의 상태, 최적화에 사용될 정보, 삭제로 인한 낭비되고 있는 데이터 등등을 보여줍니다.

API 요청 주소에는 특정 인덱스, 인덱스들 또는 전체를 포함할 수 있습니다.
```
curl -XGET 'http://localhost:9200/test/_segments'
curl -XGET 'http://localhost:9200/test1,test2/_segments'
curl -XGET 'http://localhost:9200/_segments'
```
응답결과는 다음과 같습니다.
```json
{
    ...
        "_3": {
            "generation": 3,
            "num_docs": 1121,
            "deleted_docs": 53,
            "size_in_bytes": 228288,
            "memory_in_bytes": 3211,
            "committed": true,
            "search": true,
            "version": "4.6",
            "compound": true
        }
    ...
}
```
#### _0
세그먼트에 대한 이름이 JSON 객체의 키로 구성됩니다. 이 이름은 파일을 생성할 때 사용되며, 이 세그먼트가 포함된 파편의 디렉터리 안에 이 이름으로 시작하여 파일을 만듭니다.
#### generation
새로운 세그먼트 기록이 필요할 경우 증가하는 generation 번호 입니다. 세그먼트 이름은 이 숫자로부터 파생됩니다.
#### num_docs
세그먼트 안에 삭제되지 않고 저장된 문서의 수 입니다.
#### deleted_docs
세그먼트 안에 저장되어있는 삭제된 문서의 수 입니다. 만약 이 숫자가 0보다 크다면, 병합처리를 통해서 공간을 재활용할 수 있다는 것 입니다.
#### size_in_bytes
바이트 단위로, 세그먼트가 사용한 디스크 공간입니다.
#### memory_in_bytes
세그먼트는 검색을 좀 더 효율적으로 처리하기 위하여 일부 데이터를 메모리에 저장해야합니다. 이 숫자는 이러한 목적으로 사용된 메모리 공간의 크기를 가리킵니다. 만약 -1이라면 Elasticsearch에서 이 숫자를 계산할 수 없는 상태라고 보시면 됩니다.
#### committed
세그먼트가 파일 시스템에 동기화가 되었는지 여부 입니다. 커밋된 세그먼트는 재기동 되어도 문제 없습니다. 만약 커밋에 실패한다고 해도 걱정 안하셔도 됩니다. 커밋되지 않은 세그먼트 정보는 트랜젝션로그에 저장이 되며 다음 기동시 Elasticsearch가 변경사항을 다시 적용하여 세그먼트를 복구해줍니다.
#### search
검색이 가능한지 여부 입니다. 만약 이 항목이 *false*라면 아마도 세그먼트가 디스크에 기록은 되었지만 검색이 가능하도록 갱신이 아직 안되어 있는 상태라고 보시면 됩니다.
#### version
이 세그먼트에 사용된 루씬의 버전입니다.
#### compound
세그먼트가 통합파일에 저장된지 여부 입니다. 만약 *true*라면 루씬에서 파일 디스크립터 공간을 절약하기 위하여 다수의 세그먼트 파일을 한개로 병합했다는 의미입니다.

## 상세 모드
디버깅 등을 위하여 추가적인 정보가 필요할 때는 ```verbose``` 플래그를 사용하시면 됩니다.
> 추가적으로 표출되는 상세 정보는 아직 실험적이며 언제든 변경될 수 있습니다.

```
curl -XGET 'http://localhost:9200/test/_segments?verbose=true'
```
응답결과는 다음과 같습니다.
```json
{
    ...
        "_3": {
            ...
            "ram_tree": [
                {
                    "description": "postings [PerFieldPostings(format=1)]",
                    "size_in_bytes": 2696,
                    "children": [
                        {
                            "description": "format 'Lucene50_0' ...",
                            "size_in_bytes": 2608,
                            "children" :[ ... ]
                        },
                        ...
                    ]
                },
                ...
                ]

        }
    ...
}
```