---
title: docker-compose部署gocron定时任务管理系统
date: 2020-09-30 09:56:34
tags:
    - Docker
categories:
    - Docker
---

## 简介

> 使用Go语言开发的轻量级定时任务集中调度和管理系统, 用于替代Linux-crontab

- [github 地址](https://github.com/ouqiang/gocron)
- [gocron wiki](https://github.com/ouqiang/gocron/wiki)

## `docker-compose.yaml`

```yaml
version: "3.3"

networks:
    gocron:
        driver: bridge

services:
    gocron:
        container_name: gocron
        image: ouqg/gocron
        restart: always
        volumes:
            - ${CONF_DIR}:/app/conf
            - ${LOG_DIR}:/app/log
        ports:
            - "5920:5920"
        networks:
            - gocron
        command: ./gocron web
        tty: true

```

- `${CONF_DIR}` docker-compose.yaml 目录内添加`.env` 文件设置 conf 目录、log 目录
- 第一次启动时打开 `http://localhost:5920`，会跳转到安装页面，填写 MySQL 信息，管理员信息即可

---
