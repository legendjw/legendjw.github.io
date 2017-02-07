---
layout: post
title:  "阿里云 RDS for MySQL 备份文件还原"
date:   2016-10-24 20:00:00
categories: "服务器"
permalink: /archivers/aliyun-rds-mysql-backup-restore
---

公司产品数据库一直在用阿里云的 mysql RDS，RDS可以设置数据自动备份，最近出现了一些操作失误
需要还原备份文件，官方有帮助文档但不够详尽，这里记录下自己还原备份的步骤和注意点。

## 运行环境以及必备软件

运行环境： **64位** 的 **Linux** 系统  

必备软件：

- 解压软件 [rds_backup_extract.sh](http://oss.aliyuncs.com/aliyunecs/rds_backup_extract.sh?spm=5176.7741817.2.12.yyNYYV&file=rds_backup_extract.sh) 
  阿里内部所写的解压脚本
- 还原软件 [Percona-XtraBackup](https://www.percona.com/downloads/XtraBackup/) 
  需要下载 **2.2.9** 或更新的版本使用  

rds_backup_extract使用说明：

```bash
bash rds_backup_extract -f backup.tar.gz/back.xb.gz  -C directory

# -f 指定要解压的备份集文件
# -C 指定解压到的目录；-C 参数是可选的，如果不指定就解压到当前目录，如 bash rds_backup_extract -f backup.tar.gz
```

XtraBackup使用说明：
```bash
innobackupex --defaults-file=/home/mysql/data/backup-my.cnf --apply-log /home/mysql/data

# --defaults-file 指定默认的mysql配置文件
# --apply-log 指定备份目录即包含日志文件 **xtrabackup_logfile** 的同一目录
# 更多配置参数参考(手册)[https://www.percona.com/doc/percona-xtrabackup/2.2/innobackupex/innobackupex_option_reference.html]
```

注意点：
- 由于使用解压还原软件的限制，还原环境只能为**64位**的**Linux**系统。所以需要还原的同学如果
只有Windows系统或者装了32位的Liunx系统都无法正常进行还原!

## 具体步骤

### 第一步：下载备份文件

在阿里云控制台 **云数据库RDS > 进入指定RDS实例 > 备份恢复** 页面下载你需要还原的备份文件，假设下载到本地
*/home/mysql/backup/hins575175_xtra_20150429091224.tar.gz*

### 第二步：解压备份文件

使用 rds_backup_extract 解压到 */home/mysql/data*目录下

```bash
bash rds_backup_extract -f /home/mysql/backup/hins575175_xtra_20150429091224.tar.gz -C /home/mysql/data

```

### 第三步：还原备份文件

使用 XtraBackup 所带的 innobackupex 脚本还原备份文件，如果提示 *innobackupex: completed OK!* 则表示还原成功
还原后所有文件还是在 */home/mysql/data* 下。

```bash
innobackupex --defaults-file=/home/mysql/data/backup-my.cnf --apply-log /home/mysql/data
```

还原后的目录如下所示

```bash
-rw-r----- 1 mysql mysql  412 11月 11 17:09 backup-my.cnf
-rw-r----- 1 mysql mysql 200M 11月 11 17:08 ibdata1
-rw-r----- 1 mysql mysql 500M 11月 11 17:09 ib_logfile0
-rw-r----- 1 mysql mysql 500M 11月 11 17:09 ib_logfile1
-rw-r----- 1 mysql mysql  12M 11月 11 17:09 ibtmp1
-rw-r----- 1 mysql mysql  31M 11月 11 17:09 log000000000004.tokulog27
drwxr-xr-x 2 mysql mysql 4.0K 11月 11 17:19 mysql
drwxr-xr-x 2 mysql mysql  36K 11月 11 17:09 nloa
drwxr-xr-x 2 mysql mysql 4.0K 11月 11 17:09 performance_schema
drwxr-xr-x 2 mysql mysql 4.0K 11月 11 17:09 test
-rw-r----- 1 mysql root  2.4K 11月 11 17:20 thinkpad.err
-rw-r----- 1 mysql mysql  16K 11月 11 17:09 tokudb.directory
-rw-r----- 1 mysql mysql  16K 11月 11 17:09 tokudb.environment
-rw-r----- 1 mysql mysql  16K 11月 11 17:09 tokudb.rollback
-rw-r----- 1 mysql mysql  117 11月 11 17:09 xtrabackup_binlog_info
-rw-r--r-- 1 mysql mysql   24 11月 11 17:09 xtrabackup_binlog_pos_innodb
-rw-r----- 1 mysql mysql  119 11月 11 17:09 xtrabackup_checkpoints
-rw-r----- 1 mysql mysql  731 11月 11 17:09 xtrabackup_info
-rw-r----- 1 mysql mysql 8.0M 11月 11 17:09 xtrabackup_logfile
-rw-r----- 1 mysql mysql   76 11月 11 17:09 xtrabackup_slave_filename_info
-rw-r----- 1 mysql mysql  159 11月 11 17:09 xtrabackup_slave_info
```

### 第四步：启动mysqld进程并且登录验证

查看还原后的 */home/mysql/data* 目录，你就会发现RDS进行的是全量备份，这时就可以直接使用 **mysql_safe** 来启动mysqld服务。

```bash
# 修改还原目录下所有文件的属主为mysql用户
chown -R mysql:mysql /home/mysql/data

# 根据还原目录启动mysqld服务, 也可以使用 --skip-grant-table 选项跳过用户验证
mysqld_safe --defaults-file=/home/mysql/data/backup-my.cnf --user=mysql --datadir=/home/mysql/data &

# 直接连接到备份数据库
mysql -h localhost -u root

```

还原后的mysql数据库user表中只有root用户，不包含你在RDS中建立的所有用户，而且默认root是没有密码的。如果你需要使用
navicate等软件连接还原后的数据库，需要在mysql命令窗口执行如下命令来调整root用户的密码和权限。当然你也可以直接
在运行 mysql_safe 命令时使用 **--skip-grant-table** 选项跳过用户验证，直接以任意用户名密码连接。

```mysql
use mysql;

update user set password=password('123456') where user='root';

update user set host='%' where user='root' && host='localhost';

flush privileges;

```

如果 mysql_safe 命令始终无法正常运行而又没找不到原因的话，还可以使用直接替换本地data目录的方式来查看还原数据，
可以把你本地mysql数据库的data目录替换为还原后的目录，直接以你本地的mysql用户来登录还原数据库，参考如下命令，
如果使用这种方法需要注意备份原来的数据库data目录以防止本地数据丢失!!!


```bash
# 假使本地 mysql data 目录为/opt/lampp/var/mysql
cd /opt/lampp/var/
# 备份本地 mysql data
sudo mv mysql mysql-bak
# 复制还原后的数据文件到本地 data 目录
sudo cp -R /home/mysql/data /opt/lampp/var/
sudo mv data mysql
# 删除还原后数据目录里的 mysql 文件夹以及 ib_logfile0 ib_logfile1 文件
sudo rm -rf mysql/mysql
sudo rm mysql/ib_logfile0 mysql/ib_logfile1
# 复制备份的本地mysql data目录下的mysql文件夹到还原目录下，由此获得本地的mysql用户
sudo cp -r mysql-bak/msyql mysql
# 调整文件属主
sudo chown -R mysql:mysql mysql
```

## 参考文献

- [阿里云官方文档 RDS for MySQL 备份文件恢复到自建数据库](https://help.aliyun.com/knowledge_detail/41817.html)
- [The innobackupex Script](https://www.percona.com/doc/percona-xtrabackup/2.2/innobackupex/innobackupex_script.html)
