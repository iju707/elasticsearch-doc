# 설정 조회
설정 조회 API를 통하여 인덱스(들)에 대한 설정정보를 검색할 수 있습니다.
```
curl -XGET 'localhost:9200/twitter/_settings?pretty'
```
## 복수의 인덱스/타입
설정 조회 API는 한번의 요청을 통하여 다수의 인덱스에 대한 설정을 조회할 수 있습니다. 요청 문법은 ```host:port/{index}/_settings```이며, ```{index}```는 쉼표(,) 구분방식의 인덱스 또는 별칭 목록을 사용할 수 있습니다. 모든 인덱스에 대한 정보를 조회하고자 하는 경우에는 ```_all```을 사용하면 됩니다. 와일드카드 방식 또한 지원합니다. 몇 가지 예는 다음과 같습니다.
```
curl -XGET 'localhost:9200/twitter,kimchy/_settings?pretty'
curl -XGET 'localhost:9200/_all/_settings?pretty'
curl -XGET 'localhost:9200/log_2013_*/_settings?pretty'
```
## 설정이름을 통한 필터링
와일드카드 일치 방식을 이용하여 설정이름으로 원하는 결과만 추출할 수 있습니다.
```
curl -XGET 'http://localhost:9200/2013-*/_settings/name=index.number_*'
```