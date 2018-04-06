# 인덱스 롤오버 {#indices-rollover-index}

인덱스 롤오버 API는 기존에 존재하는 인덱스가 너무 크거나 오래되었을 경우 신규 인덱스인 별칭으로 보관하는 것 입니다.

API는 1개의 별칭과 다수의 ```conditions```로 처리합니다. 별칭은 필히 1개의 인덱스만 지칭해야합니다. 인덱스가 특정 조건에 만족하면 새로운 인덱스를 생성하고 별칭을 신규인덱스로 연결합니다.

```
curl -XPUT 'localhost:9200/logs-000001 ?pretty' -d'
{
  "aliases": {
    "logs_write": {}
  }
}'

# Add > 1000 documents to logs-000001

curl -XPOST 'localhost:9200/logs_write/_rollover ?pretty' -d'
{
  "conditions": {
    "max_age":   "7d",
    "max_docs":  1000
  }
}'
```

> ```logs_write```라는 별칭을 가지는 ```logs-000001```이라는 인덱스를 생성합니다.

> ```logs_write```가 가리키는 인덱스가 생성된지 7일이 넘고, 1000개 이상의 문서를 가지고 있으면 ```logs-000002```라는 인덱스를 생성하고 ```logs_write```는 ```logs-000002```를 가리키도록 변경합니다.

위 요청에 대한 결과는 다음과 같습니다.

```json
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "old_index": "logs-000001",
  "new_index": "logs-000002",
  "rolled_over": true, 
  "dry_run": false, 
  "conditions": { 
    "[max_age: 7d]": false,
    "[max_docs: 1000]": true
  }
}
```

> ```rolled_over``` 인덱스가 롤오버 되었는지 여부
> ```dry_run``` 롤오버가 드라이런인지 여부
> ```conditions``` 각각의 조건에 대한 결과

## 신규 인덱스에 대한 명칭 {#_naming_the_new_index}

기존의 인덱스가 *-*와 숫자로 끝난다면(예 ```logs-000001```), 롤오버로 생성되는 신규인덱스는 동일한 패턴에 숫자를 증가시켜서 자동으로 생성됩니다.(예 ```logs-000002```) 숫자는 기존 인덱스의 명칭에 따라 6개의 0이 추가되는 숫자 패턴으로 생성됩니다.

만약 기존 인덱스가 해당 규칙을 따르지 않는다면, 다음과 같은 방법으로 신규인덱스의 명칭을 지정할 수 있습니다.

```
curl -XPOST 'localhost:9200/my_alias/_rollover/my_new_index_name?pretty' -d'
{
  "conditions": {
    "max_age":   "7d",
    "max_docs":  1000
  }
}'
```

## 날짜 계산을 활용한 롤오버 API {#_using_date_math_with_the_rollover_api}

롤오버 인덱스의 명칭이 [날짜 계산](date-math-index-names.md)을 활용하여 생성한다면 좀 더 유용해집니다.(예 ```logstash-2016.02.03```) 롤오버 API에서 날짜 계산을 지원하지만 인덱스명의 끝을 *-숫자*(롤오버시 증가됨)로 끝내야 합니다.(예 ```logstash-2016.02.03-1```) 예로 들어,

```
# PUT /<logs-{now/d}-1> with URI encoding:

curl -XPUT 'localhost:9200/%3Clogs-%7Bnow%2Fd%7D-1%3E ?pretty' -d'
{
  "aliases": {
    "logs_write": {}
  }
}'

curl -XPUT 'localhost:9200/logs_write/log/1?pretty' -d'
{
  "message": "a dummy log"
}'

# Wait for a day to pass

curl -XPOST 'localhost:9200/logs_write/_rollover ?pretty' -d'
{
  "conditions": {
    "max_docs":   "1"
  }
}'
```

> 오늘 날짜 기준으로 인덱스 명을 생성합니다. (예 ```logs-2016.10.31-1```)

> 바로 롤오버를 한다면 오늘 날짜 기준으로 신규 인덱스가 ```logs-2016.10.31-000002```로 생성되고, 24시간 이후 한다면 ```logs-2016.11.01-000002```가 생성될 것 입니다.

관련해서는 [날짜 계산](date-math-index-names.md)를 참고하시면 됩니다. 예로 들어, 최근 3일 생성된 인덱스를 조회할 경우에는 다음과 같이 하시면 됩니다.

```
curl -XGET 'localhost:9200/<logs-{now/d}-*>,<logs-{now/d-1d}-*>,<logs-{now/d-2d}-*>/_search?pretty'
```

## 신규 인덱스 정의 {#_defining_the_new_index}

[인덱스 템플릿](indices-templates.md)를 사용하여 신규 인덱스에 대한 설정, 매핑, 별칭 등을 정의할 수 있습니다. 추가로 [인덱스 생성 API](indices-create-index.md)와 같이 요청본문에 ```settings```, ```mappings```, ```aliases```를 정의할 수 있습니다. 요청본문에 정의된 설정은 해당 인덱스의 템플릿 설정을 덮어씁니다. 다음 예제는 ```rollover```요청으로 ```index.number_of_shards``` 옵션을 설정하는 것 입니다.

```
curl -XPUT 'localhost:9200/logs-000001?pretty' -d'
{
  "aliases": {
    "logs_write": {}
  }
}'
curl -XPOST 'localhost:9200/logs_write/_rollover?pretty' -d'
{
  "conditions" : {
    "max_age": "7d",
    "max_docs": 1000
  },
  "settings": {
    "index.number_of_shards": 2
  }
}'
```

## 드라이런 {#_dry_run}

롤오버 API는 실제 수행은 하지 않고 요청 조건만 확인할 수 있는 ```dry_run``` 옵션을 제공합니다.

```
curl -XPUT 'localhost:9200/logs-000001?pretty' -d'
{
  "aliases": {
    "logs_write": {}
  }
}'
curl -XPOST 'localhost:9200/logs_write/_rollover?dry_run&pretty' -d'
{
  "conditions" : {
    "max_age": "7d",
    "max_docs": 1000
  }
}'
```

## 파편활성화 대기 {#_wait_for_active_shards_4}

인덱스 축소 명령은 축소된 인덱스를 새로 생성하기 때문에, 인덱스 생성시 [파편활성화 대기](indices-create-index.md#create-index-wait-for-active-shards) 설정을 따라갑니다.

> 2018-04-06 : 6.2 버전