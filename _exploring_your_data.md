# 데이터를 조회해보자
## 샘플 데이터
여태까지 간략한 문서로 했다면 지금부터는 좀더 실환경과 비슷하게 데이터를 가지고 해보도록 하겠습니다. 고객계좌정보를 준비했으며 내용은 다음과 같습니다.
```json
{
    "account_number": 0,
    "balance": 16623,
    "firstname": "Bradshaw",
    "lastname": "Mckenzie",
    "age": 29,
    "gender": "F",
    "address": "244 Columbus Place",
    "employer": "Euron",
    "email": "bradshawmckenzie@euron.com",
    "city": "Hobucken",
    "state": "CO"
}
```
테스트를 위하여 [www.json-generator.com](http://www.json-generator.com/)에서 만든 것이기 때문에 실제 내용에 대해서는 무시하셔도 됩니다.
## 샘플 데이터 적재하기
샘플 데이터 파일은 [여기](../test/resources/account.json)에서 다운받을 수 있습니다. 명령을 수행할 동일한 위치에 파일을 저장하고 다음 명령으로 적재해보도록 하겠습니다.
```
curl -XPOST 'localhost:9200/bank/account/_bulk?pretty&refresh' --data-binary "@accounts.json"
curl 'localhost:9200/_cat/indices?v'
```
응답결과는 다음과 같습니다.
```
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   bank  l7sSYV2cQXmu6_4rJWVIww   5   1       1000            0    128.6kb        128.6kb
```
*bank* 인덱스 하위로 1,000개의 문서가 *account* 타입으로 인덱싱이 되었음을 알 수 있습니다.