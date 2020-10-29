# curl

## 代理
方法一： 设置http_
proxy和https_proxy环境变量
方法二： 
``` bash
curl -x http://user:password@domain:port <url>
```

## 不校验证书
``` bash
curl <url> -k
```


# wget

## 不校验证书
```bash
 wget <url> --no-check-certificate
 ```


