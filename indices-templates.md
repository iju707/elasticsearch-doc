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
## 템플릿 삭제
인덱스 템플릿을 삭제할 때는 템플릿의 이름을 기준으로 할 수 있습니다.(아래 경우 ```template_1```)
```
curl -XDELETE 'localhost:9200/_template/template_1?pretty'
```
## 템플릿 조회
인덱스 템플릿의 이름을 기준으로 내용을 조회할 수 있습니다.
```
curl -XGET 'localhost:9200/_template/template_1?pretty'
```
또는 와일드카드 또는 쉼표(,)를 사용하여 다수의 템플릿 정보를 확인할 수 있습니다.
```
curl -XGET 'localhost:9200/_template/temp*?pretty'
curl -XGET 'localhost:9200/_template/template_1,template_2?pretty'
```
모든 템플릿에 대한 정보를 조회하고자 할 경우에는 다음과 같습니다.
```
curl -XGET 'localhost:9200/_template?pretty'
```
## 템플릿 유무확인
HEAD method를 사용하여 템플릿의 존재를 확인할 수 있습니다.
```
curl -XHEAD 'localhost:9200/_template/template_1?pretty'
```
다른 유무확인 API와 동일하게 HTTP 응답코드를 가지고 결과를 확인할 수 있습니다. 존재할 경우에는 ```200```, 존재하지 않는 경우에는 ```404```가 발생합니다.
## 한개 이상의 템플릿이 적용되는 경우
만약 인덱스의 이름을 기준으로 템플릿이 한개 이상 적용이 되는 경우에는 설정과 매핑정보를 통합하여 인덱스에 적용합니다. 통합을 할 때 ```order```라는 파라미터를 추가하여 통합되는 순서를 적용할 수 있습니다. 이 순서에 따라 작은 숫자부터 적용이 되고, 큰 숫자는 나중에 통합이 됩니다. 다시 말해 큰 숫자의 템플릿이 작은 숫자의 템플릿 정보를 덮어씁니다.
```
curl -XPUT 'localhost:9200/_template/template_1?pretty' -d'
{
    "template" : "*",
    "order" : 0,
    "settings" : {
        "number_of_shards" : 1
    },
    "mappings" : {
        "type1" : {
            "_source" : { "enabled" : false }
        }
    }
}'
curl -XPUT 'localhost:9200/_template/template_2?pretty' -d'
{
    "template" : "te*",
    "order" : 1,
    "settings" : {
        "number_of_shards" : 1
    },
    "mappings" : {
        "type1" : {
            "_source" : { "enabled" : true }
        }
    }
}'
```
위 예제에서 모든 ```type1``` 매핑에 대한 ```_source``` 정렬이 안되지만, 인덱스 이름이 ```te```로 시작하면 