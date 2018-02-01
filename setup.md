# Elasticsearch 설치하기

이번 섹션은 Elasticsearch를 어떻게 설치하고 실행하는 방법에 대하여 다룰 것 입니다. 주요 내용은 다음과 같습니다.

* 다운로드 받기
* 설치하기
* 실행하기
* 설정하기

## 지원되는 플랫폼

공식적으로 지원되는 운영체제와 JVM에 대한 표는 [Support Matrix](https://www.elastic.co/support/matrix)를 참고하시면 됩니다. Elasticsearch는 목록화된 환경에서 테스트를 완료하였으며, 아마 다른 환경에서도 충분히 실행이 가능할 것 입니다.

## Java(JVM) 버전

Elasticsearch는 Java를 사용하여 개발되었습니다. 따라서 실행을 위하여 최소 [Java 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 이상을 요구합니다. Oracle의 Java 뿐만아니라 OpenJDK 또한 지원합니다. Elasticsearch 노드와 클라이언트에서는 동일한 버전의 JVM을 사용해야 합니다.

Java 버전 **1.8.0_131 또는 그이상**을 권장드립니다. 만약 잘못된 JVM 버전으로 실행한다면 Elasticsearch는 기동되지 않을 것 입니다.

Elasticsearch에서 사용할 Java 버전은 `JAVA_HOME` 환경변수를 통해서 설정하시면 됩니다.