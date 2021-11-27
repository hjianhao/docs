# FAQ

## 如何指定WSL实例的目录

[参见LxRunOffline](#LxRunOffline)

``` bash
LxRunOffline install -n <WSL名称> -d <安装系统的路径> -f <镜像文件路径>\xxx.tar.gz -s
```

其中：

-n是安装的系统名称，可自定义；

-d 是安装系统的目录；

-f是之前下载的镜像文件路径；

另外加入-s参数可在桌面创建快捷方式，可以不加。

## 如何用文件管理器查看WSL文件

在文件管理器中输入:\\wsl$

或者在wsl终端窗口输入： ``` explorer.exe .```

## 在WSL中使用GPU

需要Windows版本： 

参见： https://docs.microsoft.com/en-us/windows/win32/direct3d12/gpu-accelerated-training


# LxRunOffline

## 参考

使用LxRunOffline自定义配置WSL，在Windows下拥有你的完美Linux子系统

https://www.niconya.com/bv/555d/

# 参考文档

## 官方文档

https://docs.microsoft.com/zh-cn/windows/wsl/install-manual
