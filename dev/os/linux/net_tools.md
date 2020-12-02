# curl

## 代理
方法一： 设置http_proxy和https_proxy环境变量
方法二： 
``` bash
curl -x http://user:password@domain:port <url>
```

## 不校验证书
``` bash
curl <url> -k
```

## 配置文件
基本不变的配置可以放在~/.curlrc文件中
配置文件例子：
``` bash
proxy = <domain:port> # 代理服务器地址和端口
proxy-user = <user:passwd> # 代理的用户名密码
cacert = <path/to/cert.pem> # 设置认证proxy的证书
noproxy = ... # 设置不代理的地址
```

# wget

## 不校验证书
```bash
 wget <url> --no-check-certificate
 ```
 
 ## 配置文件
 
 配置文件在用户目录下的.wgetrc文件中。例如Linux操作系统下是'$HOME/.wgetrc'
 
 配置文件的参数详细说明：[https://www.gnu.org/software/wget/manual/html_node/Wgetrc-Commands.html](https://www.gnu.org/software/wget/manual/html_node/Wgetrc-Commands.html)
 
 ## 设置代理
 修改配置文件：'$HOME/.wgetrc'
 ```bash
 use_proxy = on # 打开代理
 http_proxy = <user>:<password>@domain:port # 设置代理服务器
 https_proxy = <user>:<password>@domain:port # 设置代理服务器
 ca_certificate = <证书路径> #设置证书路径
 # check_certificate = off # 或者不校验服务端证书
 
 
 


