# 상세 분석
만약 분석결과에 대한 추가적인 정보가 필요할 경우에는 ```explain``` 옵션을 *true*로 지정하면 됩니다.(기본값 *false*) 각각 토큰에 대한 모든 정보를 표출하게 됩니다. ```attributes``` 옵션을 사용하면 필요한 정보만 필터링할 수 있습니다.
> 추가 상세정보는 아직 실험적이기 때문에 언제든 변경될 수 있습니다.

```
curl -XGET 'localhost:9200/_analyze?pretty' -d'
{
  "tokenizer" : "standard",
  "filter" : ["snowball"],
  "text" : "detailed output",
  "explain" : true,
  "attributes" : ["keyword"] 
}'
```
> 위 경우에는 ```keyword``` 속성만 표출됩니다.

응답결과는 다음과 같습니다.
```json
{
  "detail" : {
    "custom_analyzer" : true,
    "charfilters" : [ ],
    "tokenizer" : {
      "name" : "standard",
      "tokens" : [ {
        "token" : "detailed",
        "start_offset" : 0,
        "end_offset" : 8,
        "type" : "<ALPHANUM>",
        "position" : 0
      }, {
        "token" : "output",
        "start_offset" : 9,
        "end_offset" : 15,
        "type" : "<ALPHANUM>",
        "position" : 1
      } ]
    },
    "tokenfilters" : [ {
      "name" : "snowball",
      "tokens" : [ {
        "token" : "detail",
        "start_offset" : 0,
        "end_offset" : 8,
        "type" : "<ALPHANUM>",
        "position" : 0,
        "keyword" : false 
      }, {
        "token" : "output",
        "start_offset" : 9,
        "end_offset" : 15,
        "type" : "<ALPHANUM>",
        "position" : 1,
        "keyword" : false 
      } ]
    } ]
  }
}
```
> ```attributes```에 명기한 것 처럼 ```keyword``` 속성만 표출됩니다.