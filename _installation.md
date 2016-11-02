# 설치하기
Elasticsearch는 최소 Java 8버전을 요구합니다. 이 문서에서는 Oracle JDK 1.8.0_73을 기준으로 하겠습니다. 플랫폼 별로 JDK 설치가 상이하기 때문에 여기서 자세히 다루지는 않겠습니다. Oracle JDK의 설치방법은 [Oracle 홈페이지](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)에서 확인하시기 바랍니다.

Elasticsearch를 설치하기 전에 꼭 Java 버전을 확인하고 필요에 따라 설치/업그레이드를 진행하시기 바랍니다.
```shell
java -version
echo $JAVA_HOME
```

Java가 설치되어있다면 Elasticsearch를 다운받고 실행할 수 있습니다. 바이너리는 이전 버전까지 포함하여 [www.elastic.co/downloads](http://www.elastic.co/downloads)에서 찾을 수 있습니다. ```zip```, ```tar```, ```deb```, ```rpm``` 방식으로 다운받을 수 있습니다.

여기서는 ```tar```를 사용하겠습니다.

먼저 Elasticsearch 5.0.0의 ```tar```(Windows 사용자의 경우에는 ```zip```)를 다운받겠습니다.
```
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.0.0.tar.gz
```
압축을 해지합니다.(Windows의 경우에는 ```unzip```)
```
tar -xvf elasticsearch-5.0.0.tar.gz
```
현재 디렉터리에 압축이 해지될 것 입니다. 그럼 ```bin``` 폴더로 이동하겠습니다.
```
cd elasticsearch-5.0.0/bin
```
다음의 명령으로 단일클러스터 방식의 노드를 실행할 수 있습니다. (Windows의 경우에는 ```elasticsearch.bat```)
```
./elasticsearch
```
정상적으로 진행이 된다면 다음과 같은 로그가 표시될 것 입니다.
```
[2016-09-16T14:17:51,251][INFO ][o.e.n.Node               ] [] initializing ...
[2016-09-16T14:17:51,329][INFO ][o.e.e.NodeEnvironment    ] [6-bjhwl] using [1] data paths, mounts [[/ (/dev/sda1)]], net usable_space [317.7gb], net total_space [453.6gb], spins? [no], types [ext4]
[2016-09-16T14:17:51,330][INFO ][o.e.e.NodeEnvironment    ] [6-bjhwl] heap size [1.9gb], compressed ordinary object pointers [true]
[2016-09-16T14:17:51,333][INFO ][o.e.n.Node               ] [6-bjhwl] node name [6-bjhwl] derived from node ID; set [node.name] to override
[2016-09-16T14:17:51,334][INFO ][o.e.n.Node               ] [6-bjhwl] version[5.0.0], pid[21261], build[f5daa16/2016-09-16T09:12:24.346Z], OS[Linux/4.4.0-36-generic/amd64], JVM[Oracle Corporation/Java HotSpot(TM) 64-Bit Server VM/1.8.0_60/25.60-b23]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [aggs-matrix-stats]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [ingest-common]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [lang-expression]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [lang-groovy]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [lang-mustache]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [lang-painless]
[2016-09-16T14:17:51,967][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [percolator]
[2016-09-16T14:17:51,968][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [reindex]
[2016-09-16T14:17:51,968][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [transport-netty3]
[2016-09-16T14:17:51,968][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded module [transport-netty4]
[2016-09-16T14:17:51,968][INFO ][o.e.p.PluginsService     ] [6-bjhwl] loaded plugin [mapper-murmur3]
[2016-09-16T14:17:53,521][INFO ][o.e.n.Node               ] [6-bjhwl] initialized
[2016-09-16T14:17:53,521][INFO ][o.e.n.Node               ] [6-bjhwl] starting ...
[2016-09-16T14:17:53,671][INFO ][o.e.t.TransportService   ] [6-bjhwl] publish_address {192.168.8.112:9300}, bound_addresses {{192.168.8.112:9300}
[2016-09-16T14:17:53,676][WARN ][o.e.b.BootstrapCheck     ] [6-bjhwl] max virtual memory areas vm.max_map_count [65530] likely too low, increase to at least [262144]
[2016-09-16T14:17:56,731][INFO ][o.e.h.HttpServer         ] [6-bjhwl] publish_address {192.168.8.112:9200}, bound_addresses {[::1]:9200}, {192.168.8.112:9200}
[2016-09-16T14:17:56,732][INFO ][o.e.g.GatewayService     ] [6-bjhwl] recovered [0] indices into cluster_state
[2016-09-16T14:17:56,748][INFO ][o.e.n.Node               ] [6-bjhwl] started
```
별다른 설정을 하지 않았으니, 위 경우에는 랜덤으로 생성된 *I8hydUG*라는 명으로 단일 클러스터 방식의 마스터 노드가 동작함을 알 수 있습니다. 마스터가 어떤 의미를 가지는지 모른다고 걱정안해도 됩니다. 일단 여기서 중요한 건 1개 클러스터의 1개 노드로 동작을 시작했다는 것만 아시면 됩니다.

직전에 언급된 것 처럼, 클러스터나 노드에 대한 이름을 정의할 수 있습니다. Elasticsearch를 기동할 때 다음과 같이 명령을 전달하시면 됩니다.
```
./elasticsearch -Ecluster.name=my_cluster_name -Enode.name=my_node_name
```
기동시 출력된 메시지를 확인해보시면 노드에 접속가능한 HTTP 주소(```192.168.8.112```)와 포트(```9200```)을 알 수 있습니다. 기본적으로 Elasticsearch는 ```9200```포트를 REST API용으로 사용하고 있습니다. 설정을 통하여 변경도 가능합니다.