
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [设置源](#设置源)

<!-- /code_chunk_output -->


# 设置源

将源设置为阿里云的Maven镜像
在 project 的 build.gradle中修改如下：
```

buildscript {
    ext.kotlin_version = '1.3.72'
    repositories {
        maven {url 'https://maven.aliyun.com/repository/google'}
        maven {url 'https://maven.aliyun.com/repository/jcenter'}
        maven {url 'https://maven.aliyun.com/repository/public'}
    }
}

allprojects {
    repositories {
        maven {url 'https://maven.aliyun.com/repository/google'}
        maven {url 'https://maven.aliyun.com/repository/jcenter'}
        maven {url 'https://maven.aliyun.com/repository/public'}
    }
}
```

