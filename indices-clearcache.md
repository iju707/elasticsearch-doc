# 캐시 초기화
캐시 초기화 API는 모든 캐시 또는 한개 이상의 인덱스에 할당된 특정 캐시를 초기화 할 수 있습니다.
```
curl -XPOST 'localhost:9200/twitter/_cache/clear?pretty'
```
API는 기본적으로 모든 캐시를 초기화 합니다. 특정 캐시를 초기화하고자 할 경우에는 ```query```, ```fielddata``` 또는 ```request```를 설정하여 할 수 있습니다.

특정 필드에 관련된 모든 캐시는 쉼표(,) 단위의 목록으로 구성된 ```fields```를 파라미터로 하여 지정하여 초기화 할 수 있습니다.
## 다중 인덱스
캐시 초기화 API는 한번의 요청으로 한개 이상의 인덱스에 대한 처리를 할 수 있습니다. 또는 ```_all```(생략가능)을 사용하여 모든 인덱스 처리도 가능합니다.

```
curl -XPOST 'localhost:9200/kimchy,elasticsearch/_cache/clear?pretty'
curl -XPOST 'localhost:9200/_cache/clear?pretty'
```