# cloudgo-data


#1.安装数据库

###1.1下载镜像

在windows下安装docker命令后使用：

![](https://github.com/JXONEV/cloudgo-data/raw/master/image/1.png)

![](https://github.com/JXONEV/cloudgo-data/raw/master/image/2.png)

###1.2启动 mysql 作为主机服务

![](https://github.com/JXONEV/cloudgo-data/raw/master/image/3.png)

###1.3启动 mysql client 访问服务器

得到网页和命令行的结果如下：

![](https://github.com/JXONEV/cloudgo-data/raw/master/image/4.png)

#2.mysql 基本数据库操作

创建数据库及用户表：

![](https://github.com/JXONEV/cloudgo-data/raw/master/image/5.png)

![](https://github.com/JXONEV/cloudgo-data/raw/master/image/6.png)

![](https://github.com/JXONEV/cloudgo-data/raw/master/image/7.png)

显示数据库及用户表：

![](https://github.com/JXONEV/cloudgo-data/raw/master/image/8.png)

![](https://github.com/JXONEV/cloudgo-data/raw/master/image/9.png)

![](https://github.com/JXONEV/cloudgo-data/raw/master/image/10.png)

#3.测试web服务

使用命令：

```shell
curl -d "username=ooo&departname=1" http://localhost:8080/service/userinfo
```

```
{
  "UID": 5,
  "UserName": "ooo",
  "DepartName": "1",
  "CreateAt": "2017-11-29T16:13:42.150631709+08:00"
}
```

查询上传的数据：
```shell
curl http://localhost:8080/service/userinfo?userid=
```

```
{
  "UID": 5,
  "UserName": "ooo",
  "DepartName": "1",
  "CreateAt": "2017-11-29T00:00:00Z"
}
```

#4.对比database/sql 与 orm的性能

分别使用两种方式进行ab测试：

// database/sql
```shell
ab -n 1000 -c 100 http://localhost:8080/service/userinfo?userid=
```

```shell
This is ApacheBench, Version 2.3 <$Revision: 1757674 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking localhost (be patient).....done


Server Software:        
Server Hostname:        localhost
Server Port:            8080

Document Path:          /service/userinfo?userid=
Document Length:        559 bytes

Concurrency Level:      100
Time taken for tests:   0.438 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      683000 bytes
HTML transferred:       559000 bytes
Requests per second:    2715.05 [#/sec] (mean)
Time per request:       37.133 [ms] (mean)
Time per request:       0.438 [ms] (mean, across all concurrent requests)
Transfer rate:          1798.80 [Kbytes/sec] received


Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    1   0.9      0       5
Processing:     1   34  29.5     27     136
Waiting:        1   33  29.5     27     137
Total:          1   34  29.7     27     138


Percentage of the requests served within a certain time (ms)
  50%     27
  66%     32
  75%     34
  80%     36
  90%     83
  95%    121
  98%    129
  99%    133
 100%    138 (longest request)
```

// xorm
```shell
ab -n 1000 -c 100 http://localhost:8080/service/userinfo?userid=
```

```shell
This is ApacheBench, Version 2.3 <$Revision: 1757674 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking localhost (be patient).....done


Server Software:        
Server Hostname:        localhost
Server Port:            8080

Document Path:          /service/userinfo?userid=
Document Length:        499 bytes

Concurrency Level:      100
Time taken for tests:   0.306 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      623000 bytes
HTML transferred:       499000 bytes
Requests per second:    3091.50 [#/sec] (mean)
Time per request:       27.813 [ms] (mean)
Time per request:       0.306 [ms] (mean, across all concurrent requests)
Transfer rate:          2036.25 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    1   0.2      0       8
Processing:     1   27  12.0     27      78
Waiting:        1   27  12.0     26      75
Total:          1   27  12.0     28      78


Percentage of the requests served within a certain time (ms)
  50%     28
  66%     32
  75%     34
  80%     37
  90%     42
  95%     51
  98%     56
  99%     62
 100%     76 (longest request)
```

通过对比可以看出xorm的吞吐量比较大，而且用户等待时间更短，所以与database/sql相比它的性能会更好。
