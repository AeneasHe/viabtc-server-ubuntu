要先升级 openssl

【依赖文件】

redis

zookeeper

apt install zookeeper

kafka

apt install kafka

无法安装时用源码安装

librdkafka

github 源码安装

https://github.com/edenhill/librdkafka.git

要注释掉 configure.self 里面的

# mkl_check "libsasl2" disable

# mkl_check "zstd" disable

libev

源码安装

libmpdec

源码安装

jansson

源码安装

libmysqlclient-dev (apt-get install libmysqlclient-dev)

apt-get install libmysqlclient-dev

http_parser

github 源码安装

https://github.com/nodejs/http-parser.git

libcurl

源码安装

注意 configure 时关闭一些功能

./configure --disable-ldap --disable-ldaps

utils

openssl/bio.h: No such file or directory

安装 libssl-dev

sudo apt install libssl-dev

accessws

curl 出错

makefile 里面-lcurl 移到末尾改成动态链接

cannot find -llz4

apt-get install -y liblz4-dev
