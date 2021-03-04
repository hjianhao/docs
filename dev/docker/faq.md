# 构建执行

## 如何不使用缓存构建
使用命令行参数 --no-cache

## 在构建时使用代理
使用命令行参数 --build-arg https_proxy=http://user:password@proxy-server-ip:port

# Dockerfile

## 从构建目录向镜像拷贝文件

使用COPY指令：
COPY [--chown=<user>:<group>] <源路径> <目标路径>

## 从网上下载文件放到镜像
使用ADD指令，类似COPY

## 在镜像内拷贝和移动文件
使用RUN指令，执行cp, mv等Linux指令


# 运行

## 运行镜像中的一个文件

