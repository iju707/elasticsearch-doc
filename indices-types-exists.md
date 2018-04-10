# 타입 유무확인 {#indices-types-exists}

인덱스(들)에 해당 타입(들)이 있는지 확인할 때 사용합니다.

```
curl -XHEAD 'localhost:9200/twitter/_mapping/tweet?pretty'
```

HTTP 응답코드를 이용하여 결과를 확인할 수 있습니다. 만약 있는 경우에는 ```200```을, 없는 경우에는 ```404```를 반환합니다.

> 2018-04-10 : 6.2 버전 업데이트

