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
두번째 명령은 현재 우리가 *customer*라는 이름을 가지는 1개의 인덱스를 가지고 있고 