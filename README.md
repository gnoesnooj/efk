# EFK stack

- Elasticsearch
- Fluentd
- Kibana

## Introduction

SpringBoot 기반에서 Logback 을 통해 Fluentd 로 로그를 전송합니다.

이후 Fluentd 는 수집된 로그를 Elastic Search 로 저장하며, ES 에 저장된 로그들은 Kibana를 통해 시각화 및 확인이 가능합니다.

해당 방식은 `FLUENT_TEXT` 방식으로 EFK 를 동작합니다.

FILE 방식은 주석처리된 부분을 참고해주시면 될 것 같습니다.

## Launching the EFK stack

### Requirements

- Docker
- Docker Compose
- Logback
- Fluentd
- Elastic Search
- Kibana

### Run

```bash

cd // 해당 repo clone 한 폴더 경로 //
docker-compose up --build
이후 모든 컨테이너 실행 확인 후 {host}:5601 로 이동합니다.
es와 연결해준다. 여기선 http://es01:9200 으로 연결하도록 설정되어 있습니다.

```


### Testing with sample data

1. SpringBoot 의 logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <property name="CONSOLE_LOG_PATTERN"
              value="%d{yyyy-MM-dd HH:mm:ss.SSS} %highlight(%-5level) %magenta(%-4relative) --- [ %thread{10} , %X{traceId:-} , %X{spanId:-} ] %cyan(%logger{20}) : %msg%n"/>
    <!--프로젝트 최상단을 기준으로 아래 경로에 로그 파일(.log)이 저장된다.-->
<!--    <property name="LOG_PATH" value="/var/log/joonseong"/>-->
    <!--        <property name="LOG_PATH" value="./logs"/>-->
    <!--저장되는 로그 파일의 이름을 지정한다.-->
<!--    <property name="FILE_NAME" value="jslogging-logs"/>-->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
        </encoder>
    </appender>

    <appender name="FLUENT_TEXT" class="ch.qos.logback.more.appenders.DataFluentAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!-- 에러로그를 설정하고 로그의 레벨이 맞으면 onMatch, 아니라면 onMismatch  -->
            <level>info</level>
        </filter>
        <!-- Check tag and label fluentd info: https://docs.fluentd.org/configuration/config-file-->
        <tag>applog</tag>
        <label>log</label>
        <!--로컬환경-->
        <remoteHost>localhost</remoteHost>
        <!--배포환경-->
<!--    <remoteHost>배포서버</remoteHost>-->
        <port>9882</port>
    </appender>

    <root level = "INFO">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="FLUENT_TEXT"/>
    </root>
</configuration>

```

이후 `application.yml` 에서 해당 파일의 위치를 설정해줍니다.

```yaml
logging:
  level:
    실행한:
      프로젝트:
        폴더경로: info
  config: classpath:logback.xml
```
