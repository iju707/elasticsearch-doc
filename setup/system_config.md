# 중요한 시스템 설정

이상적으로는, Elasticsearch가 서버에 단독으로 실행되고 사용가능한 모든 리스스를 이용하는것 입니다. 그렇게 되기 위해서 Elasticsearch에 기본적으로 허용되는 것 이상으로 자원에 접근이 가능하도록 OS 설정을 해야합니다.

다음은 운영환경 이관될 때 꼭 설정해야하는 것 입니다.

* [JVM Heap size](/setup/system_config/heap_size.md)
* [swapping 중지](/setup/system_config/setup_configuration_memory.md)
* [File Descriptor 증가](/setup/system_config/file_descriptions.md)
* [충분한 가상메모리 확보](/setup/system_config/vm_max_map_count.md)
* [충분한 Thread 확보](/setup/system_config/max_number_of_threads.md)

## 개발환경 VS 운영환경

기본적으로 Elasticsearch는 개발환경에서 동작함을 가정합니다. 위의 설정이 알맞게 설정되지 않더라도, 로그파일에 경고가 기록될 뿐 Elasticsearch 노드를 기동하고 실행할 수 있습니다.

`network.host`과 같은 네트워크 설정이 구성되면, Elasticsearch는 운영모드로 이동했다고 가정하며 이때부터 경고가 아닌 예외처리로 취급하게 됩니다. 예외처리가 발생되면 Elasticsearch 노드는 실행할 수 없게 됩니다. 잘못 구성된 서버로 인하여 데이터를 손실하지 않도록 하는 중요한 안전요소입니다.