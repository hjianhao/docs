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
