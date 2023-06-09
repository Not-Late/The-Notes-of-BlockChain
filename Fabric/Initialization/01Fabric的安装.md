# 一、环境准备

## 1.1 apt换源
https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/

sudo gedit /etc/apt/sources.list

sudo apt update

## 1.2 安装Git
sudo apt-get install git

## 1.3 安装Docker（包括docker、docker-compose）
### 1.3.1安装
sudo apt-get -y install docker-compose

### 1.3.2验证是否安装成功
docker --version

如果成功则显示Docker version 20.10.21, build 20.10.21-0ubuntu1~22.04.3

docker-compose --version

如果成功则显示docker-compose version 1.29.2, build unknown

### 1.3.3启动
sudo systemctl start docker（启动）

sudo systemctl enable docker（开机自启）

### 1.3.4设置权限
sudo usermod -a -G docker gyl（设置完之后记得重启）

## 1.4 Docker加速器
https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors


## 1.5 安装Go
首先到链接 https://go.dev/doc/install 去下载Linux版本的压缩包

切换sudo su

rm -rf /usr/local/go && tar -C /usr/local -xzf go1.20.4.linux-amd64.tar.gz

gedit /etc/profile

将export PATH=$PATH:/usr/local/go/bin这句代码添加到/etc/profile文件中去
（这是用于系统范围的安装，/etc/profile是一个全局的系统配置文件，用于设置全局的环境变量和默认的系统环境。它是在系统启动时由各个用户共享的，因此这样设置了之后系统中的每个用户都能共享这个环境变量。）

gedit ~/.bashrc

将source /etc/profile这句代码添加进去（作用是给gyl用户添加环境变量，source是为了生效，以后想要给哪个用户添加go环境变量，只要在该用户的.bashrc文件中加上代码source /etc/profile就行，root用户的.bashrc文件中也有这一句代码，删了之后root用户就没有go环境了）

## 1.6 配置go代理
为了加快速度

go env -w GO111MODULE=on

go env -w GOPROXY=https://goproxy.cn,direct


# 二、安装fabric-samples
## 2.1 手动创建脚本
https://github.com/hyperledger/fabric/blob/main/scripts/bootstrap.sh

（上面的url默认是指向最新的文件，但是我使用的是旧版本，可以打开history找到其他的版本）

下载上面的文件到本地之后，用gedit打开修改binaries=false

sudo chmod u+x bootstrap.sh

./bootstrap.sh

## 2.2 安装binaries
https://github.com/hyperledger/fabric/releases/download/v2.4.1/hyperledger-fabric-linux-amd64-2.4.1.tar.gz

https://github.com/hyperledger/fabric-ca/releases/download/v1.5.2/hyperledger-fabric-ca-linux-amd64-1.5.2.tar.gz

tar -xzvf 压缩包名 -C ../myFabric/fabric-samples


# 三、测试fabric-samples
## 3.1 Bring up the test network
### 3.1.1进入测试网络的目录
cd fabric-samples/test-network

### 3.1.2先清除之前运行的结点或者建立的通道
./network.sh down

### 3.1.3开启网络
./network.sh up

## 3.2





