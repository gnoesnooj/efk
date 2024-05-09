# EFK stack

- Elasticsearch
- Fluentd
- Kibana

## Introduction

DDIB 프로젝트를 위한 EFK Stack 설정입니다.

## Launching the EFK stack

### Requirements

- Docker
- Docker Compose

### Run

```bash
cd // 해당 repo clone 한 폴더 //
docker-compose up --build
이후 모든 컨테이너 실행 확인 후 {host}:5601 로 이동합니다.
es와 연결해준다. 여기선 http://es01:9200 으로 연결하도록 설정되어 있습니다..
```


### Testing with sample data

