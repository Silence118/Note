## 1.卸载之前的MySQL

```shell
# 查看安装过的mysql
rpm -qa | grep mysql
mysql-libs-5.1.73-8.el6_8.ns6.01.x86_64
mysql-5.1.73-8.el6_8.ns6.01.x86_64
mysql-devel-5.1.73-8.el6_8.ns6.01.x86_64

# 卸载安装过的mysql
rpm -e --nodeps mysql-libs-5.1.73-8.el6_8.ns6.01.x86_64
rpm -e --nodeps mysql-5.1.73-8.el6_8.ns6.01.x86_64
rpm -e --nodeps mysql-devel-5.1.73-8.el6_8.ns6.01.x86_64
# 查找并删除mysql安装路径
find / -name mysql
```

## 2.解压MySQL安装包

```shell
tar -zxvf mysql-5.7.31-linux-glibc2.12-x86_64.tar.gz
# 创建/data文件夹
mkdir /data
# 移动并重命名mysql
mv mysql-5.7.31-linux-glibc2.12-x86_64 /data/mysql
cd /data/mysql
```

## 3.创建用户和数据存放目录

```shell
# 创建mysql数据和日志目录
mkdir -p /data/mysql/{data,log}
# 创建错误信息日志
touch /data/mysql/log/err.log
# 修改/data/mysql/data文件夹所属用户和所属用户组
chown -R mysql:mysql /data/mysql/data
# 修改/data/mysql/log文件夹所属用户和所属用户组
chown -R mysql:mysql /data/mysql/log
# 修改/data/mysql/data文件夹权限
chmod -R 755 /data/mysql/data

# 添加用户组
groupadd mysql
# 添加用户mysql并指定用户组和家目录
useradd -g mysql -d /data/mysql mysql
```

## 4.初始化MySQL并配置/etc/my.cnf

```shell
# 初始化mysql 手动记录mysql登录密码
./bin/mysqld --user=mysql --basedir=/data/mysql --datadir=/data/mysql/data --initialize
cd support-files/
# 配置/data/mysql/support-files/mysql.server base目录和数据目录
sed -i s#^basedir=#basedir=/data/mysql#g mysql.server
sed -i s#^datadir=#datadir=/data/mysql/data#g mysql.server
# 配置mysql配置文件 /etc/my.cnf
vim /etc/my.cnf

[client]
default-character-set=utf8

[mysqld]
basedir = /data/mysql
datadir = /data/mysql/data
log-error=/data/mysql/log/err.log
port = 3306
#最大连接数
max_connections = 5000
#最大错误连接数
max_connect_errors = 1000
#log-bin=mysql-bin     #启用二进制日志-主从使用
#server-id=MySQL3306           #主从使用

#批量插入sql语句大小限制-默认4M
max_allowed_packet=128M

character_set_server=utf8
init_connect='SET NAMES utf8'
```

## 5.配置MySQL服务自启动并启动MySQL服务

```shell
# 将mysql服务启动文件复制到/etc/init.d目录
cp /data/mysql/support-files/mysql.server /etc/init.d/mysqld
# 修改/etc/init.d/mysqld文件权限
chmod 755 /etc/init.d/mysqld
# 注册mysql服务
chkconfig --add mysqld
# 设定mysqld在各等级为on 
# 等级0表示：表示关机 
# 等级1表示：单用户模式 
# 等级2表示：无网络连接的多用户命令行模式
# 等级3表示：有网络连接的多用户命令行模式
# 等级4表示：不可用
# 等级5表示：带图形界面的多用户模式
# 等级6表示：重新启动
chkconfig mysqld on
# 创建mysql软连接可以在任意目录下使用mysql命令
ln -s /data/mysql/bin/mysql /usr/bin/mysql

# 启动mysql服务
./mysql.server start
```

## 6. 修改MySQL登录密码和远程登录权限

```shell
# 登录mysql
mysql -u root -p
#系统生成的初始密码
# 修改密码
set password=password('新密码');
use mysql;
# 设置远程登录用户ip为任意ip
update user set host='%' where user='root';
# 授权
GRANT ALL PRIVILEGES ON *.* TO '用户名'@'%' IDENTIFIED BY '设置的密码' WITH GRANT OPTION;
# 刷新权限
FLUSH PRIVILEGES;
quit;
```

## 7. 重启MySQL服务

```shell
systemctl restart mysqld
```

