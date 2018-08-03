# centos-py-node


#### 介绍
继承python2、python3、supervisor、node环境，node版本从6开始，支持每个大版本的最高版本。

#### 镜像地址
[hub.docker.com/r/jiar/centos-py-node](https://hub.docker.com/r/jiar/centos-py-node)

#### 使用方式
需要supervisor的话，要先进入容器开启
```
FROM jiar/centos-py-node:tag

supervisord -c /etc/supervisor/supervisord.conf
supervisorctl start all
```
