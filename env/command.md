[TOC]

# 常用命令总结

##一、git



```
##删除缓冲区文件
git rm -r --cached 文件名

#强制远程分支覆盖本地分支
git fetch --all
git reset --hard origin/master
git reset --hard origin/your_branch

修改仓库地址
git remote set-url origin https://code.ii-ai.tech/saas/apis/skydon.git

```

> 参考资料 https://www.jianshu.com/p/f7b5431418d2

##二、gradle

```
#编译生成wrapper文件夹
./gradle wrapper
./gradlew build --stacktrace

#此时将生成后缀为ipr的IntelliJ IDEA工程文件，在IntelliJ IDEA中直接打开(Open)该文件即可
./gradlew idea

```



##三、linux

```
#查看端口占用情况
lsof -i:8080
netstat -anl | grep 端口号

#设置免密登录
$ ssh-copy-id  -i ~/.ssh/id_rsa root@ip地址

#安装unzip
yum install zip unzip

1.zip命令
zip -r myfile.zip ./*
将当前目录下的所有文件和文件夹全部压缩成myfile.zip文件,－r表示递归压缩子目录下所有文件.

2.unzip命令
unzip -o -d /home/sunny myfile.zip
把myfile.zip文件解压到 /home/sunny/
-o:不提示的情况下覆盖文件；
-d:-d /home/sunny 指明将文件解压缩到/home/sunny目录下；

3.其他
zip -d myfile.zip smart.txt
删除压缩文件中smart.txt文件
zip -m myfile.zip ./rpm_info.txt
向压缩文件中myfile.zip中添加rpm_info.txt文件

```



## 四、homebrew

```
禁止homebrew 自动update
export HOMEBREW_NO_AUTO_UPDATE=true
```



## 五、docker

```
参考文章：
http://tuohuang.info/setup-docker-on-mac-with-xhyve-without-gui#.XAdHkxMzbOQ
阿里云docker镜像加速
	https://cr.console.aliyun.com/cn-hangzhou/mirrors
## mac 下安装docker xhyve 是为了摆脱VirtualBox设置和更新
brew install docker docker-compose docker-machine xhyve docker-machine-driver-xhyve

#查看所有的镜像
docker images
docker rmi IMAGE_ID

#pull镜像
docker pull

#docker ps 查看容器
docker ps
-a :显示所有的容器，包括未运行的。

-f :根据条件过滤显示的内容。

--format :指定返回值的模板文件。

-l :显示最近创建的容器。

-n :列出最近创建的n个容器。

--no-trunc :不截断输出。

-q :静默模式，只显示容器编号。

-s :显示总的文件大小。


#docker 删除容器
docker container rm containerId
docker container prune

#清理掉所有处于终止状态的容器
docker container prune

#docker 查看所有已经创建的包括终止状态的容器
docker container ls -a

#停止容器
docker stop containerId

#查看容器运行后的详情
docker inspect 启动后的容器ID

#使用dockerFile 构建镜像
docker build -t="new image name" .

#运行一个docker
docker run --name tomcat -p 8083:8080 8f51a3114092

docker run --name ofcms -p 8080:8080 26e8500c8e33

docker exec -ti b40232682bea sh

docker-compose -f docker-compose.yml up -d
docker-compose -f docker-compose.yml restart
```

docker私库操作

```
1、docker daemon-》insecure registries 里添加仓库地址：端口
2、docker login 仓库地址：端口
3、docker tag 仓库地址：端口/image:version
4、docker push 仓库地址：端口/image:version
```





## 六、mysql

```
#mac 安装mysql
brew install mysql

#设置环境变量
export PATH="/usr/local/Cellar/mysql/5.7.18_1/bin:$PATH"

#启动
mysql.server start

#终端连接命令
mysql -u root -proot000000 -h 127.0.0.1 -P 3306 -D mysql

mysql -u saas_skydon_user_02 -pskydon_ur_02_pd_34(8&6F3&+p -h rm-bp18g0150979o8v4t.mysql.rds.aliyuncs.com -P 3306 -D mysql

#排查数据库最近的死锁情况
show engine innodb status


    alter user root@localhost identified with mysql_native_password by 'root000000';
CREATE USER 'ofcms'@'localhost' IDENTIFIED WITH mysql_native_password BY 'ofcms';

#查询mysql版本


#建库
CREATE DATABASE IF NOT EXISTS ofcms DEFAULT CHARACTER SET = utf8mb4;

#mysql8 设置简单密码报错ERRO): ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
mysql> SHOW VARIABLES LIKE 'validate_password%';
set global validate_password.policy=0;
set global validate_password.length=4;

然后退出后再执行
mysql_secure_installation
参考：https://www.jianshu.com/p/b437566ccf98

#查看镜像
docker images

```

### 1、Access denied for user 'root '@'localhost'

```bash
ALTER USER 'root'@'localhost' IDENTIFIED BY 'root000000' PASSWORD EXPIRE NEVER;
```

### 2、查询mysql版本

```
SHOW VARIABLES WHERE Variable_name = 'version';
```

## 7、vim

```shell
#查找替换 　https://blog.csdn.net/v1v1wang/article/details/5418098　
$> %s/vivian/sky/(等同于 :g/vivian/s//sky/) 替换每一行的第一个 vivian 为 sky

```

## 8、postgreSQL

```bash
#安装
$ brew install postgresql
To have launchd start postgresql now and restart at login:
  brew services start postgresql
Or, if you don't want/need a background service you can just run:
  pg_ctl -D /usr/local/var/postgres start
```

## 9、redis

```
安装
brew install redis
默认安装地址 /usr/local/Cellar/redis/5.0.2

建立连接
本地连接 redis-cli
远程连接 redis-cli -h host -p port -a password		redis-cli -h 192.168.1.253 -p 6369 -a Iindeed1008

redis-cli -h r-bp1bb221647e0bf4.redis.rds.aliyuncs.com -p 6379 -a Bxh^redis!@#$9764^

常用命令
keys * 查询所有的键，会遍历所有的键值，复杂度O(n)
dbsize 查询键总数，直接获取redis内置的键总数变量，复杂度O(1)
exists key 存在返回1，不存在返回0
del key [key...] 返回结果为成功删除键的个数
expire key seconds 当超过过期时间，会自动删除，key在seconds秒后过期
expireat key timestamp 键在秒级时间戳timestamp后过期
pexpire key milliseconds 当超过过期时间，会自动删除，key在milliseconds毫秒后过期
pexpireat key milliseconds-timestamp key在豪秒级时间戳timestamp后过期
ttl 命令可以查看键hello的剩余过期时间，单位：秒（>0剩余过期时间；-1没设置过期时间；-2键不存在）
pttl是毫秒
type key 如果键hello是字符串类型，则返回string；如果键不存在，则返回none
rename key newkey 
renamenx key newkey 只有newkey不存在时才会被覆盖
```

