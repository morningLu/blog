---
title: linux常用命令
date: 2018-12-26 14:15:04
tags:
---

### linux
0. history  # 偷窥别人常用命令神器
1. time
```
date                                    # 查看时间
date -s "2018-12-24 10:09:12"           # 修改时间
hwclock --hctosys                       # 恢复硬件时间
```
2. 开机执行
```
vim ~/.bash_profile
vim ~/.bashrc
```
3. 防火墙 root
```
iptable -L                   # 查看防火墙状态
iptable -F                   # 关闭防火墙
```
4. 远程拷贝
```
scp root@192.168.100.25:/data/release/file_name ./
```
5. 查看滚动日志
```
tail * -f | grep -E 'Traceback|traceback' -a50
```
6. 知道文件部分内容查找文件
```
grep '检索内容' ./ -r
```
7. 查看进程
```
ps -ef | grep desc
```

### 数据库的原则
1. mysql用redis做缓存
2. 运营的数据的需求不要写在业务逻辑里面，而是从源数据里面去导出

### mysql
1. 用户
```
mysql -uroot  # 登录
select user();  # 查看当前用户
```
2. 库
```
CREATE DATABASE database_name;  # 创库
DROP DATABASE database_name;  # 删库
show database;  # 查看所有库
grant all privileges on database_name.* to 'username'@'%' identified by 'password';  # 添加权限
flush privileges;  # 刷新权限
```
3. 表
```
DROP TABLE table_name;  #删除表
show tables;  # 查看所有表
desc table_name;  # 查看单表
ALTER TABLE `table_name` ADD `field_name` INT NOT NULL DEFAULT COMMENT 'desc' AFTER `field_name`;  # 添加字段
ALTER TABLE `table_name` ADD PRIMARY KEY `field_name`;  # 给表添加主键
ALTER TABLE `table_name` DROP `field_name`;  # 删除字段
show create table `table_name`;  # 查看表创建语句
SELECT * FROM `table_name01` WHERE  gname LIKE '%xxx%' UNION ALL SELECT * FROM `table_name02` WHERE  gname LIKE '%xxx%';  # 连表查询
```


### redis
1. 批量删除key
```
redis-cli -h 127.0.0.1 -p 6379 -n 0 keys "VPOKR_uifo*" | xargs redis-cli -h 127.0.0.1 -p 6379 -n 0 del
```

### mongodb
1. 用户
```
mongo -u username -p password 127.0.0.1:27017/database  # 登录
```
2. 库
```
use database_name  # 切换或创建数据库
db.dropDatabase()  # 删除真正使用的数据库	 
show dbs  # 显示所有数据的列表
db  # 显示当前数据库对象或集合
# 给数据创建用户
db.createUser(
     {
       user:"username",
       pwd:"password",
       roles:[{role:"readWrite",db:"database_name"}]
     }
  )
```
3. 表
  ```
  db.createCollection("table_name", {capped:true, size:1024})  # 创建一个capped表
  db.table_name.drop()  # 删表
  # 查
  db.table_name.find({
    "fieldname1": {$gt: min},
    "fieldname2": v
    }).limit(5).sort({fieldname1: 1, fieldname2: -1}).pretty()
  # 同一字段and查询
  db.table_name.find({
    $and: [{"fieldname":{$gt:min}}, {"fieldname":{$lt:max}} ]
    })
  db.table_name.find().limit(2).skip(0)  # 从第0个位置开始查2条数据
  db.table_name.insert({x: 1,y: 2,z: 3})  # 增
  ```

4. 性能
