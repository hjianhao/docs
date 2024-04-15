# PG基本知识

## 目录


<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [PG基本知识](#pg基本知识)
  - [目录](#目录)
  - [安装数据库服务和创建数据](#安装数据库服务和创建数据)
    - [安装数据库服务](#安装数据库服务)
    - [创建数据库](#创建数据库)
  - [用户创建和授权](#用户创建和授权)
    - [创建新用户](#创建新用户)
    - [将数据库权限赋予用户](#将数据库权限赋予用户)

<!-- /code_chunk_output -->



## 安装数据库服务和创建数据

### 安装数据库服务

1. 解压PostgreSQL的zip文件

2. 把PostgreSQL 的 bin 目录路径添加到PATH环境变量

3. 初始化数据库服务
 
创建数据库服务的data目录"path/to/data"

``` bash
initdb -D "path/to/data"
```

4. 启动数据库服务
``` bash
pg_ctl -D "path/to/data" start
```

### 创建数据库

1. 连接到 PostgreSQL 数据库服务器

``` bash
psql -d postgres
```

Windows操作系统在初始化PostgreSQL服务时默认会创建当前操作系统的用户名相同的超级用户并采用信任认证，psql不使用-U参数就使用这个默认超级用户

2. 创建数据库

``` sql
CREATE DATABASE mydatabase;
```

3. 查看数据库
``` sql
\l
```

## 用户创建和授权

### 创建新用户

``` sql
CREATE USER newuser WITH PASSWORD 'password';
```

### 将数据库权限赋予用户

1. 将用户设置为数据库的所有者

``` sql
ALTER DATABASE newdatabase OWNER TO newuser;
```


2. 如果你不想改变所有者，但想给予足够的权限进行常见数据库操作，可以分别授予权限：
```sql
GRANT ALL PRIVILEGES ON DATABASE newdatabase TO newuser;
```

3.  赋予用户表权限
如果你想让这个用户能够管理数据库中的所有现有表和将来创建的表，你还需要单独赋予这些权限：
```sql
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO newuser;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO newuser;
GRANT ALL PRIVILEGES ON ALL FUNCTIONS IN SCHEMA public TO newuser;
```
