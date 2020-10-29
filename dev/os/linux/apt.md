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

