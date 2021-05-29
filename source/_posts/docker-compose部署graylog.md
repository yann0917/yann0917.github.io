---
title: docker-compose部署graylog
date: 2021-03-25 10:56:18
tags:
    - Docker
categories:
    - Docker
---

## 简介

> 开源的完整日志管理工具，功能和ELK类似，但比ELK简单。

包括以下四个组件：

* mongodb
* elasticsearch
* graylog-server
* graylog-web

生产环境部署方案
![arch](../images/graylog_architec_bigger_setup.png)

## `docker-compose.yaml`

```yaml
version: '3'

services:
  # MongoDB: https://hub.docker.com/_/mongo/
  mongo:
    image: mongo:4.2
    container_name: graylog_mongo
    volumes:
      - mongo_data:/data/db
    networks:
      - graylog
  # Elasticsearch: https://www.elastic.co/guide/en/elasticsearch/reference/7.10/docker.html
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch-oss:7.10.2
    container_name: graylog_elasticsearch
    volumes:
      - es_data:/usr/share/elasticsearch/data
    environment:
      - http.host=0.0.0.0
      - transport.host=localhost
      - network.host=0.0.0.0
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    deploy:
      resources:
        limits:
          memory: 1g
    networks:
      - graylog
  # Graylog: https://hub.docker.com/r/graylog/graylog/
  graylog:
    image: graylog/graylog:4.0
    container_name: graylog
    volumes:
      - graylog_data:/usr/share/graylog/data
      # Mount local configuration directory into Docker container
      - ./config:/usr/share/graylog/data/config
      - ./plugin:/usr/share/graylog/data/plugin
    # environment:
    #   # CHANGE ME (must be at least 16 characters)!
    #   - GRAYLOG_PASSWORD_SECRET=somepasswordpepper
    #   # Password: admin
    #   - GRAYLOG_ROOT_PASSWORD_SHA2=8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918
    #   - GRAYLOG_HTTP_EXTERNAL_URI=http://127.0.0.1:9000/

    entrypoint: /usr/bin/tini -- wait-for-it elasticsearch:9200 --  /docker-entrypoint.sh
    networks:
      - graylog
    restart: always
    depends_on:
      - mongo
      - elasticsearch
    ports:
      # Graylog web interface and REST API
      - 9000:9000
      # Syslog TCP
      - 1514:1514
      # Syslog UDP
      - 1514:1514/udp
      # GELF TCP
      - 12201:12201
      # GELF UDP
      - 12201:12201/udp
      # 测试数据写入端口
      - 5555:5555
# Volumes for persisting data, see https://docs.docker.com/engine/admin/volumes/volumes/
volumes:
  mongo_data:
    driver: local
  es_data:
    driver: local
  graylog_data:
    driver: local
networks:
  graylog:
    driver: bridge
```

### 目录结构

```bash
.
├── README.md
├── config
│   ├── graylog.conf
│   ├── log4j2.xml
│   └── node-id
├── data
├── docker-compose.yml
└── plugin
    └── graylog-integrations-plugins-4.0.3
        └── plugin
            └── graylog-plugin-integrations-4.0.3.jar
```

* `config` 目录存放自定义配置文件
* `plugin` 目录存放插件文件

### 自定义配置文件

```bash
mkdir config
cd config
wget https://raw.githubusercontent.com/Graylog2/graylog-docker/4.0/config/graylog.conf
wget https://raw.githubusercontent.com/Graylog2/graylog-docker/4.0/config/log4j2.xml
```

### 插件安装

```bash
mkdir plugin
cd plugin
wget https://downloads.graylog.org/releases/graylog-integrations/graylog-integrations-plugins-4.0.3.tgz
tar -xvzf graylog-integrations-plugins-4.0.3.tgz graylog-integrations-plugins-4.0.3/plugin/graylog-plugin-integrations-4.0.3.jar
```

## 参考资料

> [graylog installation docker](https://docs.graylog.org/en/4.0/pages/installation/docker.html)

---
