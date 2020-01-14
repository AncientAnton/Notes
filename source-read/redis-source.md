# Redis

## 从配置文件开始

make/安装完毕之后，使用以下命令可以启动Redis

```
./redis-server /path/to/redis.conf
```

配置名|默认值|含义

## server.h

从服务器开始

```
struct redisServer {

}
```
## 内存分配 jemalloc