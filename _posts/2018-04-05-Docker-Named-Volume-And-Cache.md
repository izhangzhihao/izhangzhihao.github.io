---
layout: post
title: Docker Named Volume And Cache
categories: Docker
description: Docker Named Volume And Cache
keywords: Docker
---

# 背景

昨天需要部署一下 ckan，拉下来 ckan 的代码后，看到其中其实是包含 docker-compose.yml 的，就直接`docker-compose up -d --build`起来了。

## 问题

但是后来根据需要改了配置文件后，总是不生效! 去 docker container 里面看配置文件还是旧的。也试过了

```sh
docker-compose build --no-cache
docker-compose up -d
```

还是不行，

```sh
docker system prune
```

也试过了，确定在构建 docker image的时候没有使用 cache。

## 这就奇怪了

仔细看 Dockerfile 和 docker-compose.yml 的时候发现了：

```Dockerfile
ENV CKAN_HOME /usr/lib/ckan
ENV CKAN_VENV $CKAN_HOME/venv
ADD . $CKAN_VENV/src/
```

```yml
volumes:
  ckan_home:
```

对比起来看的话，因为配置文件的路径就是`src/production.ini`，通过`ADD . $CKAN_VENV/src/`将代码以及配置文件拷贝到了 docker image 里面去。但是！代码的绝对路径其实是：`/usr/lib/ckan/venv/src/`, 而在docker-compose.yml里面整个`/usr/lib/ckan`都是 volume 啊！这样就算docker image 是最新的，因为旧的 volume 还在，所以看到的配置还是旧的！！！

先跑一下`docker-compose down -v`验证一下对不对，上面的命令在停掉 docker container 的时候会把其附属的 volume 也一块删掉。然后再跑`docker-compose up -d`(此处没--build 是因为前面重新 build 的 image 已经是最新的了),起来后发现配置果然更新了！

## 解决方案

上面的临时性解决方案等以后有了更多数据就不能用了（会丢数据），我看了一下 `ckan_home` 这个 volume 并没有存需要持久化的东西，所以其实不需要使用 volume，于是就把它从 docker-compose.yml里面干掉了。所以整个问题其实跟 cache 无关，但是没仔细看的情况下一直觉得是 cache 的问题，所以跑了`docker-compose build --no-cache`好多次都没解决问题还浪费时间。仔细看了一下才发现问题所在。