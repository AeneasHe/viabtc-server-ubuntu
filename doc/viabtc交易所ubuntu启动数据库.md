mysql -u root -p

qq366370771



执行sql文件

mysql -h localhost -u root -p

 qq366370771 



create database 数据库名

use 数据库名

source  sql文件名





./marketprice.exe: init message fail: -356: Connection refused



redis集群配置



Hi Follow these step

https://seanmcgary.com/posts/how-to-build-a-fault-tolerant-redis-cluster-with-sentinel/

download all the config

https://github.com/seanmcgary/redis-cluster-example

start one by one

once it is started

redis-cli -p 16382 sentinel get-master-addr-by-name redis-cluster

will get these out put

1. "127.0.0.1"

1. "6382"



change marketprice config

add below line

"redis": {

"name": "redis-cluster",

"addr": [

"127.0.0.1:16382"

]

},

