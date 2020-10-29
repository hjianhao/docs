# Docker安装
## 更新软件
``` bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt autoremove
```

## 安装 docker 前准备
### 卸载旧版本
``` bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```

### 安装依赖使 apt 能够使用基于 https 的仓库
``` bash
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common -y
 ```
 
### 添加 docker 的离线 gpg key
``` bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
 
如果在公司proxy后安装，可以用curl -x 指定proxy。 同时使用-k 指定不做证书校验
 
### 验证 key 的信息
 ``` bash
sudo apt-key fingerprint 0EBFCD88
    
pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
```

### 设置 docker 各版本的安装源（此处是 stable 版本）
``` bash
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

## 开始安装docker

### 更新软件目录
``` bash
sudo apt-get update
```

### 安装最新版本的 docker
``` bash
sudo apt-get install docker-ce docker-ce-cli containerd.io -y
```

### 启动测试
``` bash
# 启动 docker 守护进程
sudo service docker start
# 运行测试
sudo docker run hello-
```

## 免 sudo 使用 docker 命令

### 背景
相信大家在一台新机器上面安装 docker 时候、都会发现 docker 在安装完成之后、如果你想直接使用使用 docker 命令来运行 docker 相关的操作、会爆出类似于下面的错误。
``` bash
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.26/images/json: dial unix /var/run/docker.sock: connect: permission denied
```

报错显示权限不够、那么如何解决这个问题呢？

官方文档给出了解决方案，那就是将你添加到 docker 这个用户组里面即可。
* 如果还没有 docker group 就添加一个：
``` bash
sudo groupadd docker
```

* 将相应的用户加入该 group 内。然后退出并重新登录就生效啦。
``` bash
sudo gpasswd -a ${USER} docker
```

* 重启 docker 服务
``` bash
sudo service docker restart
```

* 切换当前会话到新 group 或者重启 X 会话
``` bash
newgrp - docker
```


