# centos-py


#### 介绍
2版本：基于centOS系统提供了python2环境，同时内置了supervisor服务
3版本：基于centOS系统提供了python2、python3环境，同时内置了supervisor服务

#### 镜像地址
[hub.docker.com/r/jiar/centos-py](https://hub.docker.com/r/jiar/centos-py)

#### 使用方式
需要supervisor的话，要先进入容器开启
```
FROM jiar/centos-py:tag

supervisord -c /etc/supervisor/supervisord.conf
supervisorctl start all
```
