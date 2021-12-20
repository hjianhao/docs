
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [网络](#网络)
  - [不校验证书](#不校验证书)
  - [设置代理](#设置代理)
- [源](#源)
  - [设置阿里云的源](#设置阿里云的源)
- [缓存](#缓存)
  - [清除缓存](#清除缓存)
- [软件](#软件)
  - [删除软件包](#删除软件包)
  - [获取软件的源码修改编译](#获取软件的源码修改编译)

<!-- /code_chunk_output -->


# 网络

## 不校验证书
编辑/etc/apt/apt.conf
``` bash
Acquire::https::Verify-Peer "false";
```

## 设置代理
编辑/etc/apt/apt.conf
不指定网站
``` bash
Acquire::http::proxy "<user>:<passwd>@domain:port";
```
指定网站
``` bash
Acquire::https::Proxy::<域名> DIRECT;
```

# 源

## 设置阿里云的源
修改/etc/apt/source.list
```
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted
deb http://mirrors.aliyun.com/ubuntu/ focal universe
deb http://mirrors.aliyun.com/ubuntu/ focal-updates universe
deb http://mirrors.aliyun.com/ubuntu/ focal multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-updates multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted
deb http://mirrors.aliyun.com/ubuntu/ focal-security universe
deb http://mirrors.aliyun.com/ubuntu/ focal-security multiverse
```

# 缓存

## 清除缓存

使用apt-get或apt 命令安装一个软件包时（或在软件中心安装 DEB 包），APT 包管理器会以 .deb 格式下载软件包及其依赖关系，并将其保存在 /var/cache/apt/archives 文件夹中

下载时，apt将deb包保存在 /var/cache/apt/archives/partial 目录下。当deb包完全下载完毕后，它会被移到 /var/cache/apt/archives 目录下。

使用如下命令清除缓存
```
sudo apt clean
```

假设你安装了包 xyz。它的 deb 文件仍然保留在缓存中。如果现在仓库中有新的 xyz 包，那么缓存中现有的这个 xyz 包就已经过时了，没有用了。使用autoclean删除无用包。
```
sudo apt clean
```

# 软件

## 删除软件包

1. 删除软件包及配置
```bash
apt purge / apt-get --purge remove
```

2. 删除不需要的依赖包
```bash
apt autoremove
```

## 获取软件的源码修改编译

1. 建立一个目录并进入该目录

2. 获取源码
``` bash
apt-get source <package>
```

3. 安装编译依赖
``` bash
sudo apt-get build-dep <package>
```

4. 修改源码
进入源码目录，例如下载cmake源码，下载完成后就有一个camke-<版本号>的目录，然后进入这个目录修改源码

5. 打包生成.deb文件
在源码目录执行以下命令，就会在上一层目录生成deb安装包
``` bash
dpkg-buildpackage -rfakeroot -uc -b
```