---
title: docker-compose部署MySQL/Redis
tags:
  - Docker
categories:
  - Docker
abbrlink: 64499
date: 2019-10-28 13:38:03
---

## 配置文件

> Compose 模板文件支持动态读取主机的系统环境变量和当前目录下的 `.env` 文件中的变量。

`.env` 文件

```bash
# redis
REDIS_DIR=./redis

# mysql
MYSQL_DIR=./mysql
MYSQL_ROOT_HOST=%
MYSQL_ROOT_PASSWORD=hSMZYpAGbNwVmERI
```

## `docker-compose.yaml`

```yaml
version: "3"

networks:
    hyperf:
        driver: bridge

services:
    redis:
        container_name: redis
        image: redis
        restart: always
        volumes:
            - ${REDIS_DIR}/data:/data
            - ${REDIS_DIR}/redis.conf:/usr/local/etc/redis/redis.conf
        ports:
            - "6379:6379"
        networks:
            - hyperf
        # command: redis-server --requirepass ZpzyQrpUclFoikLV
        command: redis-server /usr/local/etc/redis/redis.conf
        tty: true

    mysql:
        container_name: mysql
        image: mysql
        restart: always
        environment:
            MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
            MYSQL_DATABASE: hyperf
            MYSQL_USER: hyperf
            MYSQL_PASSWORD: hyperf
        volumes:
            # 外部数据卷
            - ${MYSQL_DIR}/data:/var/lib/mysql
            # 外部初始化文件，文件名必须以 .sh 或 .sql 结尾
            - ${MYSQL_DIR}/init:/docker-entrypoint-initdb.d
            # 外部配置文件
            # - ${MYSQL_DIR}/conf.d:/etc/mysql/conf.d
        ports:
            - "3306:3306"
        networks:
            - hyperf
        command:
            - --character-set-server=utf8mb4
            - --collation-server=utf8mb4_unicode_ci
        tty: true

    hyperf:
        container_name: hyperf
        image: hyperf/hyperf
        volumes:
            - ../wwwroot:/wwwroot
        ports:
            - "9501:9501"
            - "9502:9502"
            - "9200:9200"
        networks:
            - hyperf
        # command: php /wwwroot/bin/hyperf.php start
        tty: true
```

### 参考资料

1. [compose file](https://yeasy.gitbooks.io/docker_practice/content/compose/compose_file.html)
2. [How to Create a MySql Instance with Docker Compose](https://medium.com/@chrischuck35/how-to-create-a-mysql-instance-with-docker-compose-1598f3cc1bee)
3. [使用docker-compose配置mysql数据库并且初始化用户](https://www.cnblogs.com/mmry/p/8812599.html)
