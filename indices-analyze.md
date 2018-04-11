# 분석 {#indices-analyze}

텍스트에 해단 분석처리를 수행하면 토큰 단위로 분리하여 결과를 반환해줍니다.

특정 인덱스를 정의하지 않고 기존에 구성된 분석기를 사용해서 할 수 있습니다.

```
curl -XGET 'localhost:9200/_analyze' -d '
{
  "analyzer" : "standard",
  "text" : "this is a test"
}'
```

만약 text 파라미터를 문자열 배열로 정의하면, 다중값 필드로 인식하고 분석합니다.

```
curl -XGET 'localhost:9200/_analyze?pretty' -H 'Content-Type: application/json' -d'
{
  "analyzer" : "standard",
  "text" : ["this is a test", "the second text"]
}'
```

또는 토큰 필터나 문자 필터와 같은 사용자 정의 임시 분석기를 사용할 수 있습니다. 토큰 필터는 짧게 ```filter```라는 이름의 파라미터로 사용할 수 있습니다.

```
curl -XGET 'localhost:9200/_analyze?pretty' -H 'Content-Type: application/json' -d'
{
  "tokenizer" : "keyword",
  "filter" : ["lowercase"],
  "text" : "this is a test"
}'
```

```
curl -XGET 'localhost:9200/_analyze?pretty' -H 'Content-Type: application/json' -d'
{
  "tokenizer" : "keyword",
  "filter" : ["lowercase"],
  "char_filter" : ["html_strip"],
  "text" : "this is a <b>test</b>"
}'
```

> **Deprecated in 5.0.0.**
> ```filters, char_filters, token_filters```는 삭제되었으므로 대신 ```filter, char_filter```를 사용하기 바랍니다.

사용자정의 Tokenizer, 토큰 필터, 문자 필터를 요청바디에 다음과 같이 지정할 수 있습니다.

```
curl -XGET 'localhost:9200/_analyze?pretty' -H 'Content-Type: application/json' -d'
{
  "tokenizer" : "whitespace",
  "filter" : ["lowercase", {"type": "stop", "stopwords": ["a", "is", "this"]}],
  "text" : "this is a test"
}'
```

특정인덱스를 지정하여 실행할 수 도 있습니다.

```
curl -XGET 'localhost:9200/analyze_sample/_analyze?pretty' -H 'Content-Type: application/json' -d'
{
  "text" : "this is a test"
}'
```

위에 예제는 "this is a test"라는 문자열을 ```test``` 인덱스에 할당된 분석기를 사용하여 분석한 결과를 보여줍니다. 또는, ```analyzer``` 옵션을 사용하여 다른 분석기를 쓸 수 있습니다.

```
curl -XGET 'localhost:9200/test/_analyze' -d '
{
  "analyzer" : "whitespace",
  "text" : "this is a test"
}'
```

또한 필드 매핑에 설정된 분석기를 사용하여 분석할 수 있습니다.

```
curl -XGET 'localhost:9200/test/_analyze' -d '
{
  "field" : "obj1.field1",
  "text" : "this is a test"
}'
```

위 예제는 ```obj1.field1```에 설정된 분석기를 이용하여 문자열을 분석하게 됩니다. 만약 설정이 없으면 인덱스 설정을 따라가게 됩니다.

```analyze_sample``` 인덱스의 키워드 필드에 ```normalizer```가 할당됩니다.

```
curl -XGET 'localhost:9200/analyze_sample/_analyze?pretty' -H 'Content-Type: application/json' -d'
{
  "normalizer" : "my_normalizer",
  "text" : "BaR"
}'
```

또는 토큰 필터나 문자 필터 같은 사용자정의 임시 Normalizer를 사용할 수 있습니다.

```
curl -XGET 'localhost:9200/_analyze?pretty' -H 'Content-Type: application/json' -d'
{
  "filter" : ["lowercase"],
  "text" : "BaR"
}'
```

> 2018-04-11 : 6.2 버전 업데이트