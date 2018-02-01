# Virtual memory

Elasticsearch는 인덱스를 보관하기 위하여 기본적으로 [`mmapfs`](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-store.html#default_fs) 방식의 디렉터리를 사용합니다. 기본적으로 운영체제가 제한하는 mmap count는 Out Of Memory가 발생할 수 있을 정도로 매우 낮을 수 있습니다.

Linux에서 `root`권한으로 다음의 명령을 통해 제한을 증가시킬 수 있습니다.

```bash
sysctl -w vm.max_map_count=262144
```

영구적으로 설정을 위해 `/etc/sysctl.conf` 파일의 `vm.max_map_count` 항목을 수정하시면 됩니다. 재부팅 이후 `sysctl vm.max_map_count` 명령으로 정상적으로 변경되었는지 검증가능합니다.

RPM 또는 Debian 패키지를 통해 설치할 경우에는 자동으로 설정하기 때문에 추가적인 설정은 필요하지 않습니다.

> **역주** 보통 Docker Container로 Elasticsearch를 구동할 때 발생합니다.