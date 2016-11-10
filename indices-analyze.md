# 분석
텍스트에 해단 분석처리를 수행하면 토큰 단위로 분리하여 결과를 반환해줍니다.

특정 인덱스를 정의하지 않고 기존에 구성된 분석기를 사용해서 할 수 있습니다.
```
curl -XGET 'localhost:9200/_analyze' -d '
{
  "analyzer" : "standard",
  "text" : "this is a test"
}'
```
응답결과는 다음과 같습니다.
```json
{
  "tokens" : [
    {
      "token" : "this",
      "start_offset" : 0,
      "end_offset" : 4,
      "type" : "<ALPHANUM>",
      "position" : 0
    },
    {
      "token" : "is",
      "start_offset" : 5,
      "end_offset" : 7,
      "type" : "<ALPHANUM>",
      "position" : 1
    },
    {
      "token" : "a",
      "start_offset" : 8,
      "end_offset" : 9,
      "type" : "<ALPHANUM>",
      "position" : 2
    },
    {
      "token" : "test",
      "start_offset" : 10,
      "end_offset" : 14,
      "type" : "<ALPHANUM>",
      "position" : 3
    }
  ]
}
```
배열을 사용하여 다수의 문자열을 분석할 수 있습니다.
```
curl -XGET 'localhost:9200/_analyze' -d '
{
  "analyzer" : "standard",
  "text" : ["this is a test", "the second text"]
}'
```
또는 토크나이저, 토큰 필터, 문자 필터 등을 이용하여 임시적인 분석기를 만들어서 사용할 수 있습니다. 토큰 필터는 짧게 ```filter``` 파라미터로 사용할 수 있습니다.
```
curl -XGET 'localhost:9200/_analyze' -d '
{
  "tokenizer" : "keyword",
  "filter" : ["lowercase"],
  "text" : "this is a test"
}'

curl -XGET 'localhost:9200/_analyze' -d '
{
  "tokenizer" : "keyword",
  "filter" : ["lowercase"],
  "char_filter" : ["html_strip"],
  "text" : "this is a <b>test</b>"
}'
```
> 5.0.0버전 부터는 ```filters```, ```char_filters```, ```token_filters```는 삭제되었으므로, ```filter```, ```char_filter```를 사용해야 합니다.

커스텀 토크나이저, 토큰 필터, 문자 필터는 다음과 같이 사용하시면 됩니다.
```
curl -XGET 'localhost:9200/_analyze' -d '
{
  "tokenizer" : "whitespace",
  "filter" : ["lowercase", {"type": "stop", "stopwords": ["a", "is", "this"]}],
  "text" : "this is a test"
}'
```
응답결과는 다음과 같습니다.
```json
{
  "tokens" : [
    {
      "token" : "test",
      "start_offset" : 10,
      "end_offset" : 14,
      "type" : "word",
      "position" : 3
    }
  ]
}
```
아니면 특정 인덱스를 지정하여 수행할 수 있습니다.
```
curl -XGET 'localhost:9200/test/_analyze' -d '
{
  "text" : "this is a test"
}'
```
위에 예제는 "this is a test"라는 문자열을 ```test``` 인덱스에 할당된 분석기를 사용하여 분석한 결과를 보여줍니다.
또는, ```analyzer``` 옵션을 사용하여 다른 분석기를 쓸 수 있습니다.
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

모든 옵션을 Request Body 또는 Request Parameter 방식으로 수행할 수 있습니다.
```
curl -XGET 'localhost:9200/_analyze?tokenizer=keyword&filter=lowercase&text=this+is+a+test'
```
또한 하위 호환을 위하여, 문자열 파라미터를 Request Body에 추가할 수 있습니다. 이때는 JSON 포맷이 아닌 순수 문자열만 들어가게 됩니다.