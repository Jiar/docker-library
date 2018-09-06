# phenix


#### 介绍
基于[phenix](https://github.com/JaneyChan/phenix)博客系统制作的镜像

#### 镜像地址
[hub.docker.com/r/jiar/phenix](https://hub.docker.com/r/jiar/phenix)

#### 说明
镜像内置三个系统：博客系统服务器(server)、博客系统前端界面(front)、博客系统后台管理界面(admin)，以及一个数据库(mariadb)。

##### 路径
- server：/var/www/serve/phenix_server
- front：/var/www/html/phenix_front
- admin：/var/www/html/phenix_admin

##### 端口
- mariadb：3306
- server：8060
- front：8061
- admin：8062

mariadb数据库数据路径：`/var/lib/mysql`，
你需要映射到一个host上的路径来保存数据，并且确保路径存在切有读写权限（目前发现在mac上无法使用，推测是mac的权限问题，暂时没有找到好的解决方案）。

##### ARG以及ENV：
- DB_CONFIG_DATABASE：数据库名字
- DB_CONFIG_USER：数据库帐号
- DB_CONFIG_PASSWORD：数据库密码
- JWT_CONFIG_SECRET：JWT密钥，默认phenix_secret
- JWT_CONFIG_EXPRISESIN：JWT失效时长，默认604800(为7天失效时间7*24*60*60)

#### 使用方式

- 第一步，执行一下命令，为容器设置端口映射、地址映射、相关变量，并启动容器：
```
docker run -p 3306:3306 -p 7010:8060 -p 7011:8061 -p 7012:8062 -e DB_CONFIG_DATABASE="phenix" -e DB_CONFIG_USER="root" -e DB_CONFIG_PASSWORD="db_password" -e JWT_CONFIG_SECRET="secret" -e JWT_CONFIG_EXPRISESIN=604800 -v /var/lib/mysql:/var/lib/mysql --privileged jiar/phenix:tag
```

- 第二步，进入容器
```
docker exec -it CONTAINERID bash
```

- 第三步，执行一下命令，为 mariadb 数据库初始化，设置密码：
1. 修改`/var/lib/mysql`目录所有者
```
chown -R mysql:mysql /var/lib/mysql
```
2. 开启`mariadb`数据库
```
systemctl start mariadb
systemctl enable mariadb
mysql_secure_installation
```
注意，执行`mysql_secure_installation`时，会要求设置初始化密码，设置的密码必须跟`docker run`时使用的`DB_CONFIG_PASSWORD`值一致。

- 第四步，创建数据库以及表：
1. 执行`mysql -u root -p $DB_CONFIG_PASSWORD`进入`sql`模式
2. 执行`CREATE DATABASE $DB_CONFIG_DATABASE;`创建数据库
3. 执行`use $DB_CONFIG_DATABASE`切换到当前数据库
4. 执行`source /var/www/serve/phenix_server/database.sql`创建表
5. 执行`exit;`退出`sql`模式

- 第五步（可选），如果需要在容器外部访问`mariadb`，需要修改权限，进入`sql`模式下后，使用如下命令修改，其中`password`为数据库密码，需要与`DB_CONFIG_PASSWORD`的值一致。
```
grant all privileges on *.* to root@"%" identified by "password" with grant option;
flush privileges; 
```

- 第六步，启动`supervisor`，命令如下：
```
supervisord -c /etc/supervisor/supervisord.conf
```
`supervisor`的配置中启用了`node`来运行`server`，启用了`nginx`来运行`front`和`admin`。


- 最后一步，使用如下命令设置管理员帐号：
```
node /var/www/serve/phenix_server/createAdmin.js "Admin_User" "Admin_Password" "Admin_Email"
```

#### 升级版本
更新版本的时候，如果数据库路径不变，且数据库路径下有完成的数据，则不需要执行第三、四、五步骤。
执行`systemctl start mariadb`、`systemctl enable mariadb`以及第六步骤即可。

