[TOC]

# linux python安装

```bash
wget https://repo.anaconda.com/archive/Anaconda3-5.3.0-Linux-x86_64.sh
yum install -y bzip2
bash Anaconda3-5.3.0-Linux-x86_64.sh -u

echo 'export PATH="~/anaconda3/bin:$PATH"' >> ~/.bashrc

conda create --name bxh python=3.5

#进入pyton虚拟环境
source activate bxh
#安装依赖
pip install -r requirment.txt

#requirment.txt内容如下
oss2
Flask==1.0.2
Pillow
PyYAML==3.12
requests==2.18.4
grequests
gevent
```



