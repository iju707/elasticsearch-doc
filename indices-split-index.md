# 인덱스 분할 {#indices-split-index}

인덱스 분할 API는 기존의 인덱스를 신규 인덱스로 분할하게 합니다. 각각의 원본 주파편이 새로운 인덱스의 2개 이상의 주파편으로 분할이 됩니다.

> **주의** ```_split``` API는 향후 분할을 위하여 대상이 되는 인덱스 생성시 ```number_of_routing_shards```를 설정해야 합니다. 이 요구사항은 Elasticsearch 7.0에서 삭제될 예정입니다.

