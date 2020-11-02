# 网络配置

## 设置proxy后如何解决unable to verify the first certificate问题

使用公司的proxy后，访问某个网站的证书的根证书变成了公司的根证书。所以在校验证书链时，node默认带的可信证书里面没有包含公司的根证书

1. 从浏览器导出公司的根证书

   * 用浏览器访问目标网站，然后点击网址左边的锁图标(chrome浏览器），然后点击“证书”
   
   * 点击“证书路径”标签页，然后选择当前访问网站域名的上一级证书（一般就是公司的根证书，看名字就清楚）
   
   * 点击“查看证书”按钮，就打开了公司的证书
   
   * 点击“详细信息”标签页
   
   * 点击“复制到文件"按钮
   
   * 选择"DER 编码二进制 X.509"，指定证书导出文件例如"hello.cer"
  
2. 用openssl将证书转换为pem格式
``` bash
 openssl x509 -inform DER -in hello.cer -out hello.pem -text   
```

3. 设置环境变量NODE_EXTRA_CA_CERTS为证书的完整路径，如d:\hello.pem

这样就可以解决这个证书错误问题了。


