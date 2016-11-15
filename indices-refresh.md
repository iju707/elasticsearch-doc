# 인덱스 갱신
갱신 API는 검색을 위한 마지막 갱신 시점부터 모든 동작을 수행할 수 있도록 한개 이상의 인덱스를 명시적으로 갱신할 수 있습니다. 실시간적 처리는 사용된 인덱스 엔진에 따라 다릅니다. 내부적으로는 갱신 명령호출이 필요하지만, 기본적으로는 갱신은 주기적으로 예약되어있습니다.
```
curl -XPOST 'localhost:9200/twitter/_refresh?pretty'
```
## 다중 인덱스
갱신 API는 한번의 요청에 한개 이상의 인덱스를 처리할 수 있습니다. 또는 ```_all```을 사용하여 전체 인덱스 처리도 가능합니다.
```
curl -XPOST 'localhost:9200/kimchy,elasticsearch/_refresh?pretty'
curl -XPOST 'localhost:9200/_refresh?pretty'
```