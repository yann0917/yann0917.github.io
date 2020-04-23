---
title: docker-compose部署jenkins
tags:
  - Docker
categories:
  - Docker
abbrlink: 34419
date: 2019-11-01 09:44:15
---

## `docker-compose.yaml`

```yaml
version: '3'
services:
  jenkins:
    image: 'jenkinsci/blueocean'
    container_name: jenkins
    restart: always
    ports:
      - '8081:8080'
      - '50000:50000'
    volumes:
      - '/var/jenkins_home:/var/jenkins_home'
```

### `jenkinsci/blueocean` 和 `jenkins/jenkins` 镜像的区别

* `jenkinsci/blueocean` 镜像包含当前的长期支持 (LTS) 的Jenkins版本 （可以投入使用） ，捆绑了所有Blue Ocean插件和功能。每次发布Blue Ocean新版本时，都会发布新镜像。
* `jenkins/jenkins` 提供了基础的镜像，常用的插件需要单独安装。

## 问题

通过 `docker-compose logs jenkins` 或 `docker logss -f jenkins` 查看 log

1. web 界面初始化安装插件时报错，将发生错误的镜像 `jenkins` 换成 `jenkins/jenkins` 问题解决
   >  - You must update Jenkins from v2.60.3 to v2.138.4 or later to run this plugin.
2. 权限问题，执行 `chown -R 1000:1000 /var/jenkins_home` 问题解决 (本地数据卷目录用户为 `root`，容器内用户为 `jenkins`(jenkins user - uid 1000))
    > Attaching to jenkins
    > jenkins    | touch: cannot touch '/var/jenkins_home/copy_reference_file.log': Permission denied
    >jenkins    | Can not write to /var/jenkins_home/copy_reference_file.log. Wrong volume permissions?

### 参考资料

1. 
2. [compose file](https://yeasy.gitbooks.io/docker_practice/content/compose/compose_file.html)
3. [Official Jenkins image to use from Docker Hub](https://jenkins.io/blog/2018/12/10/the-official-Docker-image/)
4. [Permission denied](https://github.com/jenkinsci/docker/issues/177)