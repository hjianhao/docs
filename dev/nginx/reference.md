# 教程

[agentzh 的 Nginx 教程](https://openresty.org/download/agentzh-nginx-tutorials-zhcn.html)

# 网站

[OpenResty官网](https://openresty.org/cn/getting-started.html)

# 安装

可能出现的问题： 
./configure: error: ngx_postgres addon was unable to detect version of the libpq library.
　　ERROR: failed to run command: sh ./configure –prefix=/opt/openresty/nginx …

解决：
sudo apt-get install libpq-dev
