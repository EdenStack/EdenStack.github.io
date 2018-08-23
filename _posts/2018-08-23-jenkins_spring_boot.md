---
layout:     post
title:      使用 Jenkins 自动化部署 SpringBoot 应用
date:       2018-08-23 14:55:39
summary:    deploy spring boot app with Jenkins
author:     Tneciv
categories: DevOps
thumbnail:  fab fa-jenkins
tags:
 - spring boot
 - Jenkins
 
---

# 创建 Jenkins 任务

## Step 1 : 参数化构建过程

```
添加字符参数
名称 ：BUILD_ID
默认值： dontKillMe
```

## Step 2 : Build

```
Root POM ：pom.xml //相对路径
Goals and options： clean package -Dmaven.test.skip=true
```

## Setp 3 : Post Steps

```
cp apigateway/target/*.jar /opt/jarbak
/opt/restart.sh 8085 apigateway/target/*.jar
```

## Steps 4 : Build with Parameters

```shell
#! /bin/bash
# param1 : port should be killed , param2 : jar should be started
# ./restart.sh 8085 /target/apigateway.jar
echo "端口号：$1";
pid=`netstat -nlp | grep -w $1 | sed -r 's#.* (.*)/.*#\1#'`
echo "Pid $pid will be killed"
kill -9 $pid
sleep 3s
echo "启动服务：$2"
nohup java -jar $2 &
sleep 30s
```