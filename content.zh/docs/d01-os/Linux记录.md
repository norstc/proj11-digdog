---
weight: 2
title: "Linux"
---

以下操作都是在BC linux的操作， 属于Redhat， centOS系列， 其他linux版本不一定都能适用。

## 查看版本

查看cpu

```shell
cat /proc/cpuinfo
```

查看linux的版本

发行版本

```shell
cat /etc/issue

\S
Kernel \r on an \m
```

内核版本

```shell
cat /proc/version

Linux version 4.19.90-2107.6.0.0192.8.oe1.bclinux.x86_64 (mockbuild@compute1) (gcc version 7.3.0 (GCC)) #1 SMP Fri Mar 24 10:04:47 CST 2023
```  

发行版

```shell
lsb_release –a
-bash: lsb_release：未找到命令
```

如果没有lsb_release

可以看

```shell
cat /etc/redhat-release

BigCloud Enterprise Linux For Euler release 21.10 (LTS-SP2)
```

## 用户管理

创建新用户admin，并建立一个初始目录 /home/admin

```shell
useradd -m admin
```

查看添加情况

```shell
less /etc/passwd

#其中会新增一行
admin:x:1001:1001::/home/admin:/bin/bash
```

设置一个密码

```shell
passwd admin
```

修改用户所属的用户组

```shell
#查看用户
id iop_dq

#查看用户主配置
cat /etc/passwd | grep iop_dq

#修改用户iop_dq 的用户组为 superuser
usermod -g superuser iop_dq
```

## 更改密码 passwd

修改当前用户密码

```shell
passwd 用户名
```

## 查看当期路径下所有目录的大小du

```shell
du -h --max-depth=1
```

说明：

-h : --human-readable

## 查看进程 ps

看java的进程号

```shell
ps -ef|grep java
```

查看tomcat的进程号

```shell
ps –ef | grep tomcat
```

查看 用户test1的进程

```shell
ps -u test1
```

查看所有用户的所有进程

```shell
ps -aux
```

## 查看端口netstat

当前java进程使用的端口

```shell
netstat -nap | grep java
```

或者

```shell
netstat -nlp | grep :81
```

```shell
netstat -tunlp | grep 12001
```

说明：

```shell
-t:  TCP
-u: UDP
-n: no alias
-l: listen状态
-p: program 名, 需要root权限
```

## 查看端口使用情况

查看哪个进程使用了80端口

```shell
sudo ss -lptn 'sport = :80'
```

或者

```shell
sudo lsof -n -i :80 | grep LISTEN
```

## 查找命令find

在当前用户的home目录下查找名称为”.txt”的文件

```shell
find  ~  -name  "*.txt"
```

找到超过30天的文件，然后删除

```shell
find . -mtime +30 -delete
```

执行命令的方式

找到 超过 29天的文件， 然后 执行 ls 命令

```shell
find . -mtime +29 -exec ls -l {} \;
```

参数说明

```shell

-exec: find命令的 选项， 默认是 -print(将找到的结果打印出来)， 使用-exec 表示 找到结果后 执行某个命令

ls -l: 执行命令的名称 和 该命令的选项

{} : 找到结果的占位标识，即执行命令时 使用找到的结果替代进来

\; : 执行命令的分割标识，这个表示find命令在找到结果后，将结果传递给后续命令时，采用何种分割方式 有两种 , 一个是 ;, 一个是 +
; 表示 找到一个 传一个
\+ 表示 找到所有结果后 一起传
由于;都在shell命令中有特别意义，所以需要 一个转义标识 \, 告诉shell不要解释
```

## 查看日志文件

### tail

查看tomcat的日志

```shell
cd path/to/tomcat/logs
#适用滚动模式， 自动刷新日志
tail -f catalina.out
```

带行数

```shell
#展示倒数1000行后， 然后进入滚动模式
tail -1000f catalina.out
```

### less

```shell
less catalina.out
```

移动

d : forward 下一页

u : backword 上一页

r : repaint

查找

/pattern 向后找

?pattern 向前找

n 重复上一次

N 重复上一次查找，但是反方向

跳转

g 文件第一行

G 文件最后一行

## 权限修改chmod

增加~/x.log的读取权限

```shell
chmod +r ~/x.log
```

## 编辑器VI

查找替换->全局查找替换  2023 为 2024：
:%s/2023/2024/g

## 周期执行crontab

```shell
crontab --help

usage:  crontab [-u user] file
        crontab [-u user] [ -e | -l | -r ]
                (default operation is replace, per 1003.2)
        -e      (edit user's crontab)
        -l      (list user's crontab)
        -r      (delete user's crontab)
        -i      (prompt before deleting user's crontab)
        -s      (selinux context)
```

```shell
*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 6) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12) 
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```

查看当前的周期任务

```shell
crontab -l
```

删除该周期任务(当前用户的)

```shell
crontab -r
```

修改当前用户的周期任务

```shell
crontab –e
```

示例:

```shell
[dev@core_ifapp01 zjh-18]$ crontab -l
0 1 * * * /work/logs/job-proxy/resume/rm_resume_one_day_ago.sh >> /work/logs/job-proxy/resume/resume.log 2>&1
3-58/5 * * * * /work/euop/shell/changemod.sh > /dev/null 2>&1
#3,33 * 17 5 * /work/euop/0517/0517.sh >> /work/euop/0517/0517.log 2>&1
0 8,20 * * * /home/dev/reduce.sh
0 10 * * * /var/log/checklog/checklog.sh >> /var/log/checklog/error.log  2>&1
[dev@core_ifapp01 zjh-18]$ 
[dev@core_ifapp01 zjh-18]$ 
[dev@core_ifapp01 zjh-18]$ crontab -e
crontab: installing new crontab
[dev@core_ifapp01 zjh-18]$ crontab -l
0 1 * * * /work/logs/job-proxy/resume/rm_resume_one_day_ago.sh >> /work/logs/job-proxy/resume/resume.log 2>&1
3-58/5 * * * * /work/euop/shell/changemod.sh > /dev/null 2>&1
#3,33 * 17 5 * /work/euop/0517/0517.sh >> /work/euop/0517/0517.log 2>&1
0 8,20 * * * /home/dev/reduce.sh
0 10 * * * /var/log/checklog/checklog.sh >> /var/log/checklog/error.log  2>&1

#修改18权限
11 0 * * * chmod 644 /work/euop/logs/*.log 2>&1
```

每天11点11分，修改183 日志权限

```shell
11 11 * * * chmod 644 /work/euop/logs/*.log 2>&1
```

```shell
crontab -e
## app 每天0点11分，修改99.132的app日志权限
11 0 * * * chmod 644 /appdata/euopmanagement-app/work/euop/logs/*.log 2>&1
## api每 天0点11分，修改99.132 api日志权限
11 0 * * * chmod 644 /appdata/euopmanagement-api/work/euop/logs/*.log 2>&1
```

使用文件方式增加定时任务示例：

admin登录，在/home/admin 下新建一个admincron 文件，内容如下

```shell
#每天7点7分 修改161上日志文件的权限
7 7 * * * chmod 644 /home/dev/euop_logs/*.log >> /home/dev/zjh/log_chmod644.log 2>&1
```

将该文件加入cron（即使能周期任务）

```shell
crontab admincron
```

使用周期任务示例

```shell
crontab -l

*/1* ** * /etc/rsync/rsync-mcms.sh >> /etc/rsync/rsync-mcms.log 2>&1
```

## 创建软链接ln

```shell
ls -s 目标名 软链接名
```

create a soft link for previewed images

```shell
ln -s ${the_pomp_home}/uploadBaseDir ${the_app_home}/webapps/pomp/uploadBaseDir
```

create a soft link for old previewed images

```shell
ln -s ${the_pomp_home}/img_template/template ${the_app_home}/webapps/pomp/img/template

cd ${the_build_home}

echo have created a soft link for previewed images successfully
```

删除软连接

```shell
rm -rf symbolic_name
```

**** 注意不是rm -rf symbolic_name/

实际应用：

2018-3-16解决208上/home空间过小，放不下pomp的应用数据问题，将pomp搬到/opt目录下

在当前路径下创建一个同名的指向目标目录(/opt/pomp)的短链接(pomp)

```shell
ln -s /opt/pomp
```

## 防火墙

/etc/sysconfig

关闭所有防火墙

sudo iptables -F

查看防火墙状态

sudo iptables --list

## 网络应用

## wget

```shell
wget http://path/to/some/web/location/index.html
```

该命令可以直接获取该网页，并保存到本地，在验证SSO测试服务器联通性时，使用该命令可以明确的看到网络是通的，但是http请求被上网监控转向

## telent

```shell
telnet ip port
```

比如

```shell
telnet 218.206.191.179 80
```

## ping

```shell
ping http://www.bing.com
```

ping通并不代表网络协议数据可用，只能单纯看网络是否联通

## curl

curl是非常强大的网页测试工具，可以直接显示出当前网址的内容

```shell
#查看curl 基本信息
curl --version

#查看帮助信息
curl --help

## euop 
# 使用euop提供的接口模拟一级iop推送精准活动
# 活动文件： activity-file.txt
curl -i  http://10.14.15.18/pomp/services/G2EActUpService?wsdl   -H "Content-Type: text/xml"   --data-binary "@activity-file.txt"


# 连续curl 20次，并记录时间
for i in {1..20};do time curl http://10.255.98.8:8076/pc/recommandService/tagquery/01705161003/017051616457/18351305080;done

for i in {1..20};do echo `curl http://192.168.1.181:8080/euop-proxy/iopproxy/downloadMaterials.do`;done

for i in {1..20};do echo `curl http://10.14.15.18/pomp/iopproxy/downloadMaterials.do`;done

#在18上，手动更新30003文件
curl  -k "sftp://127.0.0.1:22/data/common/bidata/20210603/day/" --user "superuser" -T "/home/zhjh/bidata/20210603-r/a_10000_20210603_IOP-F30003_01_001.dat" --ftp-create-dirs

#上传多个文件的写法
curl -k "sftp://127.0.0.1:22/data/common/bidata/20220327/day/" --user "superuser" -T  "{/home/zhjh/bidata/20220327-r/day/a_10000_20220327_IOP-F30003_01_001.dat,/home/zhjh/bidata/20220327-r/day/a_10000_20220327_IOP-F30003_01.verf}" --ftp-create-dirs

#参考:  https://curl.se/docs/manpage.html#-T

#在18上 , 手动模拟2230重庆省iop活动

curl  -k "sftp://127.0.0.1:22/data/2230/activity/20211126/day/" --user "iopcq2230" -T "/home/zhjh/2230-simactivity/20211126/a_2230_20211126_IOP-F20001_2230112601_00_001.dat" --ftp-create-dirs
 
#在郑州高新区api上手动取 一级iop的素材文件 10.255.98.216:3021 

#判断是否能连接sftp  iopdqIOPDQ1234!@#$iopdq
curl  -k "sftp://10.255.98.216:3021/" --user "iop_dq" -v
curl  -k "sftp://10.255.98.216:3021/interface01/iop/picture/" --user "iop_dq" -v
#从18 连郑州高新区sftp 入口  StAPp_Pomp_2018#euoP
curl -k "sftp://file-euop-portal.10086.cn:8022/"  --user "stapp" -v 
Enter host password for user 'stapp':
* About to connect() to file-euop-portal.10086.cn port 8022 (#0)
*   Trying 10.250.63.228... connected
* Connected to file-euop-portal.10086.cn (10.250.63.228) port 8022 (#0)
* Failure establishing ssh session
* Closing connection #0
curl: (2) Failure establishing ssh session


#下载文件 <campaignId>20230403778520</campaignId>  20230403778520_3c0a5997-1e63-48af-88a0-3e4374e7a2e2.png
curl  -k "sftp://10.255.98.216:3021/interface01/iop/picture/20230403778520/20230403778520_3c0a5997-1e63-48af-88a0-3e4374e7a2e2.png" -o "./20230403778520/20230403778520_3c0a5997-1e63-48af-88a0-3e4374e7a2e2.png" --user "iop_dq"  --ftp-create-dirs

#一级iop换目录了 /interface01/iop_dq/picture
curl  -k "sftp://10.255.98.216:3021/interface01/iop_dq/picture/20230403778520/20230403778520_3c0a5997-1e63-48af-88a0-3e4374e7a2e2.png" -o "./20230403778520/20230403778520_3c0a5997-1e63-48af-88a0-3e4374e7a2e2.png" --user "iop_dq"  --ftp-create-dirs

```

## sftp

```shell
sftp -o Port=18022 iop_dq@180.169.129.52
sftp -o Port=3021 iop_dq@10.255.98.216
```

OKfmyA1!qaz 密码

直接使用sftp来测试ftp服务器是否可以联通

## nc/ncat

判断网络是否通

```shell
ncat ip port
```

## FTP服务器

### sftp

链接远端服务器

sftp [iop_dq@10.255.234.113](mailto:iop_dq@10.255.234.113)

bash-4.1$ sftp

usage: sftp [-1Cv] [-B buffer_size] [-b batchfile] [-F ssh_config]

​      [-o ssh_option] [-P sftp_server_path] [-R num_requests]

​      [-S program] [-s subsystem | sftp_server] host

​    sftp [user@]host[:file ...]

​    sftp [user@]host[:dir[/]]

​    sftp -b batchfile [user@]host

带不同的端口

sftp -oPort=22 [iop_dq@10.255.234.113](mailto:iop_dq@10.255.234.113)

### 常见命令

bye                Quit sftp

cd path              Change remote directory to 'path'

chgrp grp path           Change group of file 'path' to 'grp'

chmod mode path          Change permissions of file 'path' to 'mode'

chown own path           Change owner of file 'path' to 'own'

df [-hi] [path]          Display statistics for current directory or

​                  filesystem containing 'path'

exit                Quit sftp

get [-P] remote-path [local-path] Download file

help                Display this help text

lcd path              Change local directory to 'path'

lls [ls-options [path]]      Display local directory listing

lmkdir path            Create local directory

ln oldpath newpath         Symlink remote file

lpwd                Print local working directory

ls [-1aflnrSt] [path]       Display remote directory listing

lumask umask            Set local umask to 'umask'

mkdir path             Create remote directory

progress              Toggle display of progress meter

put [-P] local-path [remote-path] Upload file

pwd                Display remote working directory

quit                Quit sftp

rename oldpath newpath       Rename remote file

rm path              Delete remote file

rmdir path             Remove remote directory

symlink oldpath newpath      Symlink remote file

version              Show SFTP version

!command              Execute 'command' in local shell

!                 Escape to local shell

?                 Synonym for help

### 配置一个ftp服务器（redhad/bc linux）

https://guide.freecodecamp.org/linux/install-and-configure-ftp-server-in-linux-redhat/

Install and configure FTP server in Redhat/Centos Linux

FTP stands for File Transfer Protocol. It was written by Abhay Bhushan and published as RFC 114 on 16 April 1971. It is supported by all operating system and browsers. It is built on a client-server architecture.

Install and configure FTP server in Redhat/Centos Linux

Step 1: We will use localhost for our machine to setup ftp server.

Step 2: Install vsftpd (very secure FTP daemon) package.

yum install -y vsftpd

Step 3: Start FTP Server when system on.

systemctl enable vsftpd.service

Step 4: Checking the status of ftp server

systemctl status vsftpd.service

Step 5: Configure vsftpd package. We will edit

/etc/vsftpd/vsftpd.conf

Change the line which contain anonymous_enable=NO to anonymous_enable=YES

This will give permit any one to access FTP server with authentication.

Change the following to YES

local_enable=YES

write_enable=YES

Step 6: Start FTP Server

systemctl start vsftpd.service

Step 7: Install FTP Client

yum install -y lftpd

Step 8: Connect ftp to localhost

lftp localhost

### 启用ftp的21端口

​     sudo vsftpd /etc/vsftpd/vsftpd.conf &

​     如果sftp能连，ftp不能连

1、 iptables --list , 确认防火墙是否关闭

2、 service vsftpd status , 确认ftp服务是否正常

不正常

[root@wap-tomcat02 vsftpd]# service vsftpd status

Redirecting to /bin/systemctl status vsftpd.service

● vsftpd.service - Vsftpd ftp daemon

  Loaded: loaded (/usr/lib/systemd/system/vsftpd.service; disabled; vendor preset: disabled)

  Active: inactive (dead)

需要重启

service vsftpd restart

再次看状态，正常了

[root@wap-tomcat02 vsftpd]# service vsftpd status

Redirecting to /bin/systemctl status vsftpd.service

● vsftpd.service - Vsftpd ftp daemon

  Loaded: loaded (/usr/lib/systemd/system/vsftpd.service; disabled; vendor preset: disabled)

  Active: active (running) since 四 2018-09-13 16:56:37 CST; 4s ago

 Process: 19922 ExecStart=/usr/sbin/vsftpd /etc/vsftpd/vsftpd.conf (code=exited, status=0/SUCCESS)

 Main PID: 19923 (vsftpd)

  CGroup: /system.slice/vsftpd.service

​      └─19923 /usr/sbin/vsftpd /etc/vsftpd/vsftpd.conf

9月 13 16:56:37 wap-tomcat02 systemd[1]: Starting Vsftpd ftp daemon...

9月 13 16:56:37 wap-tomcat02 systemd[1]: Started Vsftpd ftp daemon.

### 添加一个新用户

比如账号：mgvideo

Root账号登录

直接编辑群文件

vi /etc/group

mgvideo:x:531:superuser

然后，再编辑密码文件

vi /etc/passwd

mgvideo:x:531:531::/home/common/3001001:/sbin/nologin

然后再设置下密码

passwd mgvideo

### 使用脚本改权限lftp

```shell

lftp -u iopha2371,iopha2371Pomp_2018#EuoP sftp://10.14.15.18:22 <<EOF

chmod -R 775 /data/2371/activity/$(date +%Y%m%d)

bye

EOF
```

### ftp

连接ftp

```shell
ftp 127.0.0.1
name:
password:
ftp 127.0.0.1
Connected to 127.0.0.1 (127.0.0.1).
220 (vsFTPd 3.0.3)
Name (127.0.0.1:root): iopsh2210
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> pwd
257 "/" is the current directory
ftp> ls
227 Entering Passive Mode (127,0,0,1,255,253).
150 Here comes the directory listing.
drwxr-xr-x  3 537   538      4096 May 13 2019 20190513
-rw-r--r--  1 537   538      39 May 13 2019 20190513164900.txt
drwxrwx---  40 537   512     4096 Sep 30 04:26 activity
drwxrwx--- 899 537   512     20480 Feb 01 20:53 data
drwxr-xr-x  2 537   538     4096 May 13 2019 day
drwxr-xr-x  3 537   538     4096 Aug 13 11:14 home
drwxrwx---  46 537   512     4096 Sep 30 06:49 picture
drwxr-xr-x  7 512   512     4096 Sep 17 15:57 report
drwxr-xr-x  4 537   538      4096 May 13 2019 sms
drwxr-xr-x  2 537   538     4096 May 13 2019 sms\20190513\day
226 Directory send OK.
ftp> pwd
257 "/" is the current directory
ftp>
```

使用不同端口连接

```shell
ftp 

ftp> open 117.136.129.219 10090
Connected to 117.136.129.219 (117.136.129.219).
220 (vsFTPd 2.2.2)
Name (117.136.129.219:zhjh): cxftp
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
```

## 文件同步rsync

### 源地址：使用周期任务来执行同步脚本

rsync配置目录： 192.168.1.210

周期任务root的

```shell
[admin@test-pomp-apache01 cron]$ sudo less root 

*/2 * * * * /etc/rsync/rsync_labs.sh 2>1&

*/2 * * * * /etc/rsync/rsync_wap.sh 2>1&

[admin@test-pomp-apache01 cron]$ pwd

/var/spool/cron
```

### 目标地址：rsync以后台服务的方式执行，保持监听状态

配置目录：192.168.1.171

/etc/rsync

查看rsync是否运营：ps -ef | grep rsync

 杀掉已经运行的rsync： sudo kill [PID]

 修改配置文件：/etc/rsync/rsync.conf

 启动后台服务

rsync --daemon --config=/etc/rsync/rsync.conf

使用 rsync 示例

```shell
less /etc/rsync/rsync-mcms.sh

!/bin/bash

src1=/work/www/

src2=/work/wap/

src3=/work/wwwhd/new10086/

des1=www

des2=wap

des3=wwwhd

host1=192.168.1.138

host2=192.168.1.65

user=root

/usr/bin/rsync -vzrtlug --progress $src1 $user@$host1::$des1 --password-file=/etc/rsync/www.passwd

/usr/bin/rsync -vzrtlug --progress $src2 $user@$host2::$des2 --password-file=/etc/rsync/wap.passwd

/usr/bin/rsync -vzrtlug --progress $src3 $user@$host1::$des3 --password-file=/etc/rsync/www.passwd
```

目标地址：192.168.1.138

监听服务

```shell
[root@portal-labs-138 rsync]# ps -ef | grep rsync

root   6841   1 0 Jan24 ?    00:00:00 rsync --daemon --config=/etc/rsync/rsync.conf

[root@portal-labs-138 rsync]# less /etc/rsync/rsync.conf

uid = 0

gid = 0

port = 873

use chroot = no

strict modes = false

log file = rsyncd.log

hosts allow = *

hosts deny = *

[www]

path = /portal/cm_platform_4

ignore errors = yes

read only = no

write only = no

hosts allow = 192.168.1.117

list = yes

uid = root

gid = root

auth users = root

secrets file = /etc/rsync/www.passwd

[wwwhd]

path = /portal/cm_platform_5

ignore errors = yes

read only = no

write only = no

hosts allow = 192.168.1.117

list = yes

uid = root

gid = root

auth users = root

secrets file = /etc/rsync/www.passwd
```

## 挂载mount

将远地目录192.168.1.116:/work/MCMSrec/tomcat6/webapps/mcms/samples/new10086 挂载到 /home/view/preview

```shell
mount -t nfs -o rw 192.168.1.116:/work/MCMSrec/tomcat6/webapps/mcms/samples/new10086 /home/view/preview
```

## 压缩 tar

```shell
cd /data

tar –cvf filename.tar foldername
```

## 文件传输 sz/rz

需要额外安装

下载文件到本地 sz filename

上传文件 rz

## Yum安装

配置repo的路径/etc/yum.repos.d

新增一个sj.repo

内容如下

[sj.repo]

name=sj.repo

baseurl=http://ftp.sjtu.edu.cn/centos/7/os/x86_64/

enabled=1

gpgcheck=0

查看repo的状态 yum repolist all

[root@wap-tomcat02 yum.repos.d]# yum repolist all

已加载插件：fastestmirror, langpacks

Loading mirror speeds from cached hostfile

源标识                              源名称                               状态

local.repo                            local.repo                             禁用

sj.repo                              sj.repo                              启用: 9,911

repolist: 9,911

[root@wap-tomcat02 yum.repos.d]#

这样就可以通过这个sj.repo安装东西了

比如安装gcc 和g++

```shell
yum intall gcc gcc-c++
```

### yum 安装提示非RHN(未完)

```shell
yum update
Loaded plugins: rhnplugin, security
This system is not registered with RHN.
RHN support will be disabled.
Setting up Update Process
No Packages marked for Update

```

```shell
cd /etc/yum.repos.d/
```

删除yum

```shell
rpm -qa | grep yum
yum-updatesd-0.9-2.el5
yum-metadata-parser-1.1.2-3.el5
yum-rhn-plugin-0.5.4-13.el5
yum-3.2.22-20.el5
yum-security-1.1.16-13.el5
```

查找当前yum

```shell
rpm -qa | grep yum | xargs rpm -e --nodeps
[root@tst-mcms-tomcat3 yum.repos.d]# rpm -qa | grep yum
```

centos5 历史版本仓库： https://vault.centos.org/5.11/os/i386/CentOS/

yum-fastestmirror-1.1.16-21.el5.centos.noarch.rpm
yum-fastestmirror-1.1.16-21.el5.centos.noarch.rpm
yum-metadata-parser-1.1.2-4.el5.i386.rpm
yum-3.2.22-40.el5.centos.noarch.rpm

同时安装多个rpm包， 避免出现循环依赖

```shell
rpm -ivh ./yum-3.2.22-40.el5.centos.noarch.rpm ./yum-fastestmirror-1.1.16-21.el5.centos.noarch.rpm ./yum-metadata-parser-1.1.2-4.el5.i386.rpm 
```

```shell
[root@tst-mcms-tomcat3 yum-install]# ll
总计 1068
-rw-r--r-- 1 root root 1034960 03-21 14:49 yum-3.2.22-40.el5.centos.noarch.rpm
-rw-r--r-- 1 root root   21033 03-21 14:49 yum-fastestmirror-1.1.16-21.el5.centos.noarch.rpm
-rw-r--r-- 1 root root   26164 03-21 14:49 yum-metadata-parser-1.1.2-4.el5.i386.rpm
[root@tst-mcms-tomcat3 yum-install]# rpm -ivh ./yum-3.2.22-40.el5.centos.noarch.rpm ./yum-fastestmirror-1.1.16-21.el5.centos.noarch.rpm ./yum-metadata-parser-1.1.2-4.el5.i386.rpm 
warning: ./yum-3.2.22-40.el5.centos.noarch.rpm: Header V3 DSA signature: NOKEY, key ID e8562897
Preparing...                ########################################### [100%]
   1:yum-metadata-parser    ########################################### [ 33%]
   2:yum-fastestmirror      ########################################### [ 67%]
   3:yum                    ########################################### [100%]
[root@tst-mcms-tomcat3 yum-install]# 
[root@tst-mcms-tomcat3 yum.repos.d]# rpm -qa | grep yum
yum-fastestmirror-1.1.16-21.el5.centos
yum-metadata-parser-1.1.2-4.el5
yum-3.2.22-40.el5.centos
```

然后更新  （未完）

升级老版的linux很浪费时间， 不如重做一个新版的linux

## 远程拷贝scp

scp 源 目标

```shell
scp /home/daisy/full.tar.gz root@172.19.2.75:/home/root
```

## 设置别名

```shell
alias ll='ls -l'
```

针对监控要求禁止使用rm，可以将rm命令设置别名，加到所有关键账号的配置文件中，这样就可以避免误操作了

比如zhjh，修改

```shell
vi ~/.bashrc

#在文件末尾，增加下列

alias rm='echo "禁止使用"; false'
```

## 设置中文字体

```shell
export LANG=zh_CN.UTF-8
```

## 查找grep

在一个文件中查找多个字符串

```shell
grep -e “string1” -e “string2” target_file_name
```

排除查找到的结果

```shell
grep -v "string1" target_file_name
```

在grep中使用正则表达式

- ^ : 行开头
- $ : 行结尾
- [0-9] : 匹配任意一个数字

```shell
grep -v "string1" target_file_name

grep '^2|202310301000' $(ls -rt | grep iopLog_ | grep  2023-10-30)  | wc -l

grep '^[0-9]|202310301000' euop_channel_iopLog_196.113.74.55.2023-10-30.log  | grep -v '2|202310301000' | grep -v '5|202310301000'
```

## 设置时钟

命令名：

ntpdate

例子：

```shell
ntpdate ntp1.aliyun.com
```

## supervisor

【已解决】supervisor的supervisorctl start出错：unix:///var/run/supervisor/supervisor.sock no such file

```shell
supervisord -c /etc/supervisord.conf
```

配置文件 /etc/supervisord.conf

```shell
supervisorctl status

redis-nodes-6379         RUNNING  pid 24937, uptime 0:21:59

redis-nodes-6380         RUNNING  pid 24938, uptime 0:21:59

tomcat              RUNNING  pid 24939, uptime 0:21:59
```

## 取消自动登出TMOUT

查看当前设置

```shell
set | grep TMOUT

TMOUT=0
```

修改 ./.bashrc

增加

```shell
export TMOUT=0
```

或者

```shell
unset TMOUT
```

## UMASK默认权限

umask

0022

第一个0 表示 8进制

后面3位数 作为被减数，用最大权限（文件666 rw-rw-rw-， 文件夹 777 rwxrwxrwx）作为减数

666 – 022 = 644 (rw-r--r--)

777 – 022 = 755 （rwx-r-xr-x）

umask

0022

```shell
umask -S
```

u=rwx,g=rx,o=rx

u:所有者

g:群组

o:其他

## shell脚本参数

```shell
./shell_name.sh one two three
```

- $1 is one
- $2 is two
- $3 is three
- $0 is ./shell_name.sh
- $# is 3 , parameters count
- $$ is pid of this script
- $* is one two three
- $? Last return code

## top

top 可以查看当前系统的cpu，内存使用情况

## awk

awk是一个脚本语言工具， 复杂度远远超过一般的命令行命令， 应该把它当作一个脚本语言来学， 而不是单纯的背几个命令。

awk核心作用是通过脚本的方式来处理文本文件，典型应用是按照指定的匹配模式来扫描指定的文件， 当发现匹配时， 针对匹配的内容进行操作。

参考文档： https://www.gnu.org/software/gawk/manual/

语法：

```shell
awk options 'selection _criteria {action}' input-file > output-file
```

例子：

```shell
#将filename中每一行都打印出来
awk '{print}' filename

#将一行内容按 空格 分割后， 打印出 第二个 元素
awk '{print $2}' filename

#更改分割符为 : 后，打印出 每行第一个元素
awk 'BEGIN {FS = ":" }; /20230329140800401490/ {print $1} ' euop/logs/euop.log
```

## 管道

linux的管道十分强大

比如： 杀掉用户test1 启动的进程

```shell
kill -9 $(ps -ef|grep test1|awk '{print $2}')
```

其中使用了kill 命令
ps命令
grep命令
awk命令
