1. 构建
`docker build -f ./jenkins_build/Dockerfile -t myjenk .`

1. docker 安装启动Jenkins

下载Jenkins
  `docker pull jenkins/jenkins:lts`

查看

```
docker images
docker inspect jenkins镜像ID
```

创建一个Jenkins目录
`mkdir /data/jenkins_home`

配置端口
8085:8080
50000:50000

安装启动Jenkins容器
```
docker run -d --name jenkins -p 8085:8080 -p 50000:50000 -v /data/jenkins_home:/var/jenkins_home jenkins/jenkins:lts;
备注：
-d //启动在后台
--name //容器名字
-p //端口映射（8081：宿主主机端口，8080：容器内部端口）
-v //数据卷挂载映射（/data/jenkins_home：宿主主机目录，另外一个即是容器目录）
jenkins/jenkins:lts //Jenkins镜像（最新版）
```

查看Jenkins启动情况
`docker ps | grep jenkins;`

管理Jenkins

查看密码
在安装完成后，默认生成了一个登录密码，首次登录需要这个密码。
密码路径：var/jenkins_home/secrets/initialAdminPassword //容器内部
查找密码：
docker exec -it jenkins_01 bash //进入jenkins容器
cat /var/jenkins_home/secrets/initialAdminPassword //查看密码

1. 配置git credentials
1. pipeline or node
1. docker in docker
1. mvn 命令 https://www.jianshu.com/p/6f57c322e50e

docker run -d --name jenkins2 --user root -p 8086:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock jenkins/jenkins
apt-get update && apt-get install -y docker.io

<!-- docker run --name jenkins -p 8080:8080 -p 50000:50000
-v /var/run/docker.sock:/var/run/docker.sock
-v $(which docker):/usr/bin/docker
-v /home/jenkins_home:/var/jenkins_home
jenkins/jenkins -->

### mysql

``` sh
sudo /usr/local/mysql/support-files/mysql.server start
sudo /usr/local/mysql/support-files/mysql.server stop
sudo /usr/local/mysql/support-files/mysql.server restart
```

如果期间出现错误, 更多的是权限问题, 把 /usr/local/mysql/data 设置为读写

`sudo chmod -R 777 /usr/local/mysql/data`

如果忘记了密码

1. 关闭mysql, 可强制关闭进程
  `sudo pkill -9 mysql`

2. 本地启动
  `sudo /usr/local/mysql/support-files/mysql.server start --skip-grant-tables`

3. 修改mysql.user表里面的password字段

```
update mysql.user set password='' where host='localhost';
flush privileges;
```

4. 退出重启, 即可在本地免密登录, 之后按自己需求设置相应的用户密码