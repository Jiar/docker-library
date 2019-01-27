# centos-systemctl


#### 介绍
centOS 的 docker 镜像存在问题，直接使用会导致 systemctl 权限不足。
这里使用官方提供的[解决方案](https://github.com/docker-library/docs/tree/master/centos#systemd-integration)。

#### 镜像地址
[hub.docker.com/r/jiar/centos-systemctl](https://hub.docker.com/r/jiar/centos-systemctl)

#### 使用方式
```
FROM jiar/centos-systemctl:tag
```
