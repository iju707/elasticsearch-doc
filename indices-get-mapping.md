# 매핑 조회 {#indices-get-mapping}

매핑조회API는 인덱스나 인덱스/타입에 정의된 매핑정보를 검색할 수 있게 해줍니다.

```
curl -XGET 'localhost:9200/twitter/_mapping/tweet?pretty'
```

## 다중 인덱스 및 타입 {#_multiple_indices_and_types}

매핑조회API는 한번의 요청에 1개 이상의 인덱스나 타입 매핑에 대한 정보를 가져올 수 있습니다. 일반적으로 ```host:port/{index}/_mapping/{type}```의 문법을 가지게 됩니다. 여기서 ```{index}```나 ```{type}```은 쉼표(,)를 이용하여 나열할 수 있습니다. 만약에 모든 인덱스에 대한 매핑을 조회하고자 할 경우에는 ```{index}```에 ```_all```을 입력하시면 됩니다. 예제를 보겠습니다.

```
curl -XGET 'localhost:9200/_mapping/_doc?pretty'
curl -XGET 'localhost:9200/_all/_mapping/_doc?pretty'
```

모든 인덱스 및 타입에 대한 매핑을 얻고자 할 경우에는 두가지 방법이 있습니다.

```
curl -XGET 'localhost:9200/_all/_mapping?pretty'
curl -XGET 'localhost:9200/_mapping?pretty'
```

> 2018-04-06 : 6.2 버전