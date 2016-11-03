# 클러스터 상태확인
우리의 클러스터가 어떻게 동작하고 있는지 기본적인 상태확인부터 시작해보겠습니다. 여기서는 curl 명령을 사용하여 진행할 것 이며, 필요에 따라 HTTP/REST 명령를 지원하는 어떤 방법이든 사용해도 상관없습니다. 일단, 동일한 서버에 Elasticsearch를 기동하고 명령창이 실행된 상태라고 가정하겠습니다.

상태확인을 위해서, [_cat API](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat.html)를 사용하면 됩니다.
```
curl -XGET 'localhost:9200/_cat/health?v&pretty'
```

