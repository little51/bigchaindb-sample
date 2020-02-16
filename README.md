# bigchaindb-sample

BigchainDB（https://github.com/bigchaindb/bigchaindb）是一个基于数据库理念的区块链实现，其安装过程可参见http://docs.bigchaindb.com/projects/server/en/latest/simple-deployment-template/index.html。但由于操作系统、python包依赖关系、网络环境的原因，直接按手册安装很难成功。本实践过程描述了在Ubuntu 18.04下安装BigchainDB2.0.0的全过程，并进行测试，为了简化安装过程，忽略掉了https（nginxnginx和域名）部分。

BigchainDB的主要组件：

- MongoDB：NoSQL数据数，用于存存交易数据
- Tendermint：提供拜占庭容错（BFT）的共识引擎
- BigchainDB Server：BigchainDB 服务
- monit：进程监控程序，监控Tendermint和BigchainDB Server服务
- bigchaindb_driver：BigchainDB官方python驱动

## 1、单结点安装

### 1.1、操作系统准备

#### 1.1.1、版本要求

```
lsb_release -a
```

结果应为：Ubuntu **18.04**.2 LTS或更高版本

#### 1.1.2、代理使用说明

在ubuntu18.04里，设置全局代理用apt、pip3均无作用，所以要用到代理的话，需要在apt和pip3命令后带上相应的代理参数。

```
 apt命令后，增加 -o Acquire::http::proxy="http://ip:port"
 pip3命令后，增加 --default-timeout=1000 --proxy=http://ip:port
```

#### 1.1.3、设置国内源

```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
vi /etc/apt/sources.list
```

```
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释

deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse

# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse

deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse

# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse

deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse

# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse

deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse

# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse

# 预发布软件源，不建议启用

# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse

# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
```

#### 1.1.4、更新系统

```
sudo apt update
sudo apt full-upgrade
```

### 1.2、安装python3-pip及关联组件

```
sudo apt install -y python3-pip libssl-dev
sudo pip3 install -U pip
sudo pip3 install pytest-runner
sudo pip3 install --upgrade python-rapidjson
sudo pip3 install --upgrade gevent
sudo pip3 install --upgrade cryptography
sudo pip3 install --upgrade protobuf
sudo pip3 install --upgrade testresources
```

### 1.3、安装BigchainDB

```
sudo pip3 install bigchaindb==2.0.0 --ignore-installed PyYAML
装完以后验证：
bigchaindb --version
```

### 1.4、配置BigchainDB

```
bigchaindb configure
```

第一个问题API Server bind? (default `localhost:9984`)
设置为：0.0.0.0:9984
其实的按默认值配置

### 1.5、安装MongoDB

```
sudo apt install mongodb
使用以下命令查看mongodb状态：
systemctl status mongodb
```

### 1.6、安装配置Tendermint 

```
sudo apt install -y unzip
wget https://github.com/tendermint/tendermint/releases/download/v0.31.5/tendermint_v0.31.5_linux_amd64.zip
unzip tendermint_v0.31.5_linux_amd64.zip
rm tendermint_v0.31.5_linux_amd64.zip
sudo mv tendermint /usr/local/bin
tendermint init
```

### 1.7、安装监控程序monit并配置

```
sudo apt install monit
bigchaindb-monit-config
```

### 1.8、起动BigchainDB服务

```
monit -d 1
查看服务状态：
monit status 和 monit summary
```

### 1.9、安装python版驱动

```
sudo pip3 install bigchaindb_driver
```

### 1.10 验证

```
$ python3
Python 3.6.9 (default, Nov  7 2019, 10:44:02) 
[GCC 8.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import bigchaindb_driver
>>> from bigchaindb_driver import BigchainDB
>>> bdb = BigchainDB('localhost:9984')
>>> print(bdb)
<bigchaindb_driver.driver.BigchainDB object at 0x7f27277c7b70>
```

## 2、多结点网络建立

待续。。。 。。。
