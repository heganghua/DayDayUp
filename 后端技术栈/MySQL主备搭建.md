``` shell
# 1、准备两台安装好了mysql的服务器
  128.196.110.107(主)
  128.196.111.128(备)
# 2、登录主数据库服务器
 
# 3、登录主数据库
mysql --login-path=sulogin
mysql -h localhost -u haha -p
# 4、创建用于主备复制数据库操作的用户
create user 'repl'@'%' idenfitied with mysql_native_password by 'Ha_12345';
 
# 5、用户授权
grant replication slave on *.* to 'repl'@'%';
 
# 6、查看授权信息是否正确修改
show grants for 'repl'@'%';
 
# 7、刷新授权表信息
flush privileges;
 
# 8、重启主数据库服务
service mysqld restart
 
# 9、获取主节点当前binary log 文件名和位置(position)
show master status\G;
 
# 10、切换至从节点主机，登录MySQL
 
# 11、在从节点上设置主节点参数
# master_host为从那台服务复制，即master的ip
# master_log_file和master_log_pos为告诉slave服务从哪个binlog文件的哪个日志点开始复制
change master to
master_host = '128.196.110.107',
master_user = 'repl',
master_password = '密码',
master_log_file = 'binlog.000004',
master_log_pos = 154;

# 12、启动链路复制
start slave;

# 13、查看链路复制是否启动成功
show slave status\G;

# Io_Running以及SQL_Running 都为Yes则启动成功
# 14、测试 主节点运行下面语句，查看从节点是否同步该据库
create database `secge` character set 'utf8mb4' collate 'utf8mb4_bin';

# 常见问题、以及额外命令
show databases;
use databaseName;
show tabases;
# 删除授权信息
revoke all on *.* from 'repl'@'%';
# 获取用户信息
select host, user, authentication_string, plugin from user;
# 查看密码策略
show variables like 'validate_password';
# 删除用户
drop 'repl'@'%';
# 关闭主从同步
stop slave;
ps: 解决不了问题， 先重启
```

