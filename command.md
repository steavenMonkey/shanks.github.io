[TOC]

# 常用命令总结

##一、git命令

```
##删除缓冲区文件
git rm -r --cached 文件名


```



##二、gradle

```
#编译
./gradlew build --stacktrace

#此时将生成后缀为ipr的IntelliJ IDEA工程文件，在IntelliJ IDEA中直接打开(Open)该文件即可
./gradlew idea
```



##三、linux

```
#查看端口占用情况
lsof -i:8080
```



## 四、homebrew

```
禁止homebrew 自动update
export HOMEBREW_NO_AUTO_UPDATE=true
```



## 五、docker

```
参考文章：http://tuohuang.info/setup-docker-on-mac-with-xhyve-without-gui#.XAdHkxMzbOQ
## mac 下安装docker xhyve 是为了摆脱VirtualBox设置和更新
brew install docker docker-compose docker-machine xhyve docker-machine-driver-xhyve


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

