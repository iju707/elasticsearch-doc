# 설치하기
Elasticsearch는 최소 Java 8버전을 요구합니다. 이 문서에서는 Oracle JDK 1.8.0_73을 기준으로 하겠습니다. 플랫폼 별로 JDK 설치가 상이하기 때문에 여기서 자세히 다루지는 않겠습니다. Oracle JDK의 설치방법은 [Oracle 홈페이지](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)에서 확인하시기 바랍니다.

Elasticsearch를 설치하기 전에 꼭 Java 버전을 확인하고 필요에 따라 설치/업그레이드를 진행하시기 바랍니다.
```shell
java -version
echo $JAVA_HOME
```

Java가 설치되어있다면 Elasticsearch를 다운받고 실행할 수 있습니다. 바이너리는 이전 버전까지 포함하여 [www.elastic.co/downloads](http://www.elastic.co/downloads)에서 찾을 수 있습니다.