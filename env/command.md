[TOC]

# 常用命令总结

##一、git

```
##删除缓冲区文件
git rm -r --cached 文件名


```



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

#清理掉所有处于终止状态的容器
docker container prune

#docker 查看所有已经创建的包括终止状态的容器
docker container ls -a

#停止容器
docker stop containerId

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
```

