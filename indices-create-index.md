# 인덱스 생성
인덱스 생성 API를 통하여 인덱스를 인스턴스화 할 수 있습니다. Elasticsearch에서는 
## 인덱스 설정
인덱스 별로 특정 설정을 지정하여 생성할 수 있습니다.
```
curl -XPUT 'localhost:9200/twitter?pretty' -d'
{
    "settings" : {
        "index" : {
            "number_of_shards" : 3, 
            "number_of_replicas" : 2 
        }
    }
}'
```

> ```number_of_shards```의 기본값은 **5**입니다.
> ```number_of_replicas```의 기본값은 **1**입니다. 

