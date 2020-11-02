
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

