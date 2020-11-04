
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [网络配置](#网络配置)
  - [不校验证书](#不校验证书)
- [路径配置](#路径配置)
  - [文件存放路径](#文件存放路径)
    - [配置npm的全局模块的存放路径](#配置npm的全局模块的存放路径)
    - [配置npm的cache路径](#配置npm的cache路径)

<!-- /code_chunk_output -->


# 网络配置

## 不校验证书
执行以下命令：
``` bash
npm config set strict-ssl false
```

# 路径配置

## 文件存放路径

### 配置npm的全局模块的存放路径
``` bash
npm config set prefix <path to global>
```
同时要将 ```<path to global>``` 加入到PATH环境变量中，以便能执行安装模块的可执行命令

另外需要要将环境变量NODE_PATH设置为```<path to global>\node_modules```以便全局模块被require搜索




### 配置npm的cache路径
``` bash
npm config set cache <path to cache>
```

# 模块安装

## 安装配置

npm install moduleName 命令
1. 安装模块到项目node_modules目录下。
2. 不会将模块依赖写入devDependencies或dependencies 节点。
3. 运行 npm install 初始化项目时不会下载模块。

npm install -g moduleName 命令
1. 安装模块到全局，不会在项目node_modules目录中保存模块包。
2. 不会将模块依赖写入devDependencies或dependencies 节点。
3. 运行 npm install 初始化项目时不会下载模块。

npm install --save moduleName 命令
1. 安装模块到项目node_modules目录下。
2. 会将模块依赖写入dependencies 节点。
3. 运行 npm install 初始化项目时，会将模块下载到项目目录下。
4. 运行npm install --production或者注明NODE_ENV变量值为production时，会自动下载模块到node_modules目录中。

npm install --save-dev moduleName 命令
1. 安装模块到项目node_modules目录下。
2. 会将模块依赖写入devDependencies 节点。
3. 运行 npm install 初始化项目时，会将模块下载到项目目录下。
4. 运行npm install --production或者注明NODE_ENV变量值为production时，不会自动下载模块到node_modules目录中。

总结
devDependencies 节点下的模块是我们在开发时需要用的，比如项目中使用的 gulp ，压缩css、js的模块。这些模块在我们的项目部署后是不需要的，所以我们可以使用 -save-dev 的形式安装。像 express 这些模块是项目运行必备的，应该安装在 dependencies 节点下，所以我们应该使用 -save 的形式安装。

