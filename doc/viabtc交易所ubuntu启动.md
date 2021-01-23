【参考】

https://github.com/lealife/viabtc_exchange_server/issues/1



适用于 Centos 6.0+/7.0+. (为什么要写到issue中, 因为可以讨论)

1. 安装依赖

1.1 yum安装依赖

yum install -y libev-devel
yum install mpdecimal # 可能不需要了
yum install mpdecimal-devel
yum install mariadb-devel -y # libmysqlclient-dev没有, 只能用这个了
yum install zlib-static
yum install libcurl-devel


1.2. 安装 jansson

wget http://www.digip.org/jansson/releases/jansson-2.7.tar.gz
tar -xzvf jansson-2.7.tar.gz
cd  jansson-2.7
./configure --prefix=/usr
make && make install


查看是否安装成功:

> whereis libjansson
libjansson: /usr/lib/libjansson.la /usr/lib/libjansson.a /usr/lib/libjansson.so


1.3. 安装 kafka lib

$ wget https://github.com/edenhill/librdkafka/archive/v0.11.3.tar.gz -O librdkafka-0.11.3.tar.gz
$ tar -xzxf librdkafka-0.11.3.tar.gz
$ cd librdkafka-0.11.3
$ ./configure
$ make
$ make install


2. 编译viabtc_exchange_server

本项目与官方的viabtc_exchange_server不同在于修改编译命令以适配于Centos, 其它源码都一样.

下载链接: https://github.com/lealife/viabtc_exchange_server/archive/master.zip

依次进入各个文件夹编译

cd depends/hiredis
make
 
cd network
make
 
cd utils
make

cd accesshttp
make

cd accessws
make

cd alertcenter
make

cd marketprice
make

cd matchengine
make

cd readhistory
make


如果编译都成功, 恭喜你, 已经成功了99%.

如果编译出现失败, 请看本文的"问题"部分.

3. Mysql配置

3.1 创建数据库

sql/中的3个文件请依次执行, init_trade_history.sh 里需要修改相应的数据库配置

- create_trade_history.sql

- create_trade_log.sql

- init_trade_history.sh

Tips:

 可以在mysql客户端执行sql文件, 

mysql -u root -p

source  create_trade_history.sql完整路径



也可以使用mysql命令, 如: 

mysql -h localhost -u root -p xxx < create_trade_history.sql



3.2 修改数据库配置

- 修改 matchengine/config.json中的 db_log, db_history数据库配置

- 修改 readhistory/config.json中的db_history数据库配置

4. 启动

4.1 启动 Redis

请自行安装Redis.

启动它(必须要使用sentinel):

cd  redis目录
redis-server ./redis.conf &
redis-sentinel ./sentinel.conf &


4.1 创建日志文件夹

mkdir -p /var/log/trade/


4.2 启动

cd matchengine
./restart.sh

cd ../alertcenter
./restart.sh

cd ../readhistory
./restart.sh

cd ../accesshttp
./restart.sh

cd ../accessws
./restart.sh

cd ../marketprice
./restart.sh


5. 测试

curl http://localhost:8080 -d '{"method": "market.list", "params": [], "id": 1516681174}'


更多接口测试可以安装 https://github.com/djpnewton/viaxchtest 非常方便

6. kafka安装与运行

kafka还没运行呢! 但暂时不影响步骤5的接口测试. 接下来要安装kafka和运行它.

大家可以搜索下, 很多资料!! 在Centos中安装kafka, 如 https://blog.csdn.net/wisgood/article/details/51497329

联系方式

成品展示: http://smallcoin.teakki.top/

需要二次开发或问题讨论请加我微信 lealife, 备注 via btc,

问题

问题1 libmysqlclient找不到

解决: 可以尝试安装新版 mariadb: https://blog.csdn.net/default7/article/details/74795766

问题2 marketprice build出错

/usr/local/lib/librdkafka.a(rdkafka_sasl_cyrus.o): In function `rd_kafka_sasl_cyrus_close':
/home/paymatch/down/librdkafka-0.11.3/src/rdkafka_sasl_cyrus.c:409: undefined reference to `sasl_dispose'
/usr/local/lib/librdkafka.a(rdkafka_sasl_cyrus.o): In function `rd_kafka_sasl_cyrus_recv':
/home/paymatch/down/librdkafka-0.11.3/src/rdkafka_sasl_cyrus.c:74: undefined reference to `sasl_client_step'
/home/paymatch/down/librdkafka-0.11.3/src/rdkafka_sasl_cyrus.c:100: undefined reference to `sasl_errdetail'
/home/paymatch/down/librdkafka-0.11.3/src/rdkafka_sasl_cyrus.c:112: undefined reference to `sasl_getprop'
/home/paymatch/down/librdkafka-0.11.3/src/rdkafka_sasl_cyrus.c:116: undefined reference to `sasl_getprop'
/home/paymatch/down/librdkafka-0.11.3/src/rdkafka_sasl_cyrus.c:120: undefined reference to `sasl_getprop'


解决:

在makefile后加 -lsasl2

参考: https://stackoverflow.com/questions/49457353/undefined-reference-to-sasl-x-when-build

