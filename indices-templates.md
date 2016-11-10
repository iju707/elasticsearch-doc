# 인덱스 템플릿
인덱스 템플릿은 새로운 인덱스를 생성할 때 적용될 내용을 미리 템플릿으로 만들 수 있게 해줍니다. 템플릿은 설정과 매핑 정보를 포함할 수 있으며, 간단한 패턴 템플릿을 통하여 새로운 인덱스에 적용할지 말지를 결정할 수 있습니다.
> 템플릿은 인덱스 생성 시점에만 적용이 됩니다. 템플릿의 내용을 변경하더라도 기존의 인덱스에 영향을 주지 않습니다.

예로,
```
curl -XPUT 'localhost:9200/_template/template_1?pretty' -d'
{
  "template": "te*",
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "type1": {
      "_source": {
        "enabled": false
      },
      "properties": {
        "host_name": {
          "type": "keyword"
        },
        "created_at": {
          "type": "date",
          "format": "EEE MMM dd HH:mm:ss Z YYYY"
        }
      }
    }
  }
}'
```
> 인덱스 템플릿의 경우에는 C언어 스타일의 주석(```/* */```)를 지원합니다. JSON 객체 안에 어디든 주석을 기입할 수 있습니다.

아래 예제는 ```template_1```이라는 이름의 템플릿을 정의하는 것 입니다. 템플릿 패턴을 ```te*```로 하였기 때문에 ```te*``` 패턴에 일치하는 이름의 인덱스를 생성할 경우 템플릿의 설정과 매핑정보를 따르게 됩니다.

그리고 인덱스 템플릿 안에 별칭 정보를 포함하는 것도 가능합니다.
```
curl -XPUT 'localhost:9200/_template/template_1?pretty' -d'
{
    "template" : "te*",
    "settings" : {
        "number_of_shards" : 1
    },
    "aliases" : {
        "alias1" : {},
        "alias2" : {
            "filter" : {
                "term" : {"user" : "kimchy" }
            },
            "routing" : "kimchy"
        },
        "{index}-alias" : {} 
    }
}'
```
> 내용 중 ```{index}``` 부분은 향후에 인덱스가 생성될 시점에 실제 인덱스 이름으로 치환이 됩니다.

