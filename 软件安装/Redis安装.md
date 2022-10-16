# Redis安装

## 1.解压redis

```shell
tar -zxvf redis-7.0.5.tar.gz
# 重命名为/data/redis
mv redis-7.0.5 /data/redis
```

## 2.安装依赖,编译redis

```shell
cd /data/redis
yum -y install gcc g++
# 编译
make
make install
```

## 3.修改redis.conf

```shell
cp redis.conf redis.conf.bak
vim redis.conf

# 设置后台启动
daemonize yes
# 任意ip都可连接 注释原来的
# bind 127.0.0.1 -::1
# 关闭保护模式
protected-mode no
# 需要密码连接
# requirepass 密码
```

## 4.启动redis

```shell
# 指定配置文件启动redis
redis-server /data/redis/redis.conf
```