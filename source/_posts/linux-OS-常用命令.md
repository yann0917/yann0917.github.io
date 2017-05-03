---
title: linux OS 常用命令
date: 2017-05-03 10:30:27
tags: Linux
categories: Linux
---
本文汇总一些常用的Linux命令，分类如下：
 - 操作目录或文件的常用指令
 - 系统管理命令
 - 打包压缩命令
 - 关机重启命令
 - Linux 管道
 - Linux 软件安装(centos yum安装)
 - 用户及用户组
 - 文件权限管理
 - **crontab 执行定时任务**
 - **find 文件搜索**

具体每个命令的详细用法，可以查看[鸟哥的Linux私房菜](http://linux.vbird.org/linux_basic/)这本书，最基础最详细的Linux教程，可以作为入门教程。
### 常用指令
```
ls     显示文件或目录
   -l  列出文件详细信息l(list)
   -a  列出当前目录下所有文件及目录，包括隐藏的a(all)
mkdir  创建目录
   -p  创建目录，若无父目录，则创建p(parent)   
cd     切换目录
touch  创建空文件
echo   创建带有内容的文件。
cat    查看文件内容
cp     拷贝
mv     移动或重命名
rm     删除文件
   -r  递归删除，可删除子目录及文件
   -f  强制删除
find   在文件系统中搜索某文件
wc     统计文本中行数、字数、字符数
grep   在文本文件中查找某个字符串
rmdir  删除空目录
tree   树形结构显示目录，需要安装tree包
pwd    显示当前目录
ln     创建链接文件
more、less   分页显示文本文件内容
head、tail   显示文件头、尾内容
ctrl+alt+F1  命令行全屏模式
```

### 系统管理命令
```
stat     显示指定文件的详细信息，比ls更详细
who      显示在线登陆用户
whoami   显示当前操作用户
hostname 显示主机名
uname    显示系统信息
top      动态显示当前耗费资源最多进程信息
ps       显示瞬间进程状态 ps -aux
du       查看目录大小 du -h /home带有单位显示目录信息
df       查看磁盘大小 df -h 带有单位显示磁盘信息
ifconfig 查看网络情况
ping     测试网络连通
netstat  显示网络状态信息
man      帮助命令，命令不会用了 如：man ls
clear    清屏,或者 Ctrl+L
alias    对命令重命名 如：alias showmeit=”ps -aux” ，另外解除使用unaliax showmeit
kill     杀死进程，可以先用ps 或 top命令查看进程的id，然后再用kill命令杀死进程。
```
### 打包压缩命令
```
gzip：
bzip2：
tar:     打包压缩
   -c    归档文件
   -x    压缩文件
   -z    gzip压缩文件
   -j    bzip2压缩文件
   -v    显示压缩或解压缩过程 v(view)
   -f    使用档名

tar -cvf /home/abc.tar /home/abc        只打包，不压缩
tar -zcvf /home/abc.tar.gz /home/abc    打包，并用gzip压缩
tar -jcvf /home/abc.tar.bz2 /home/abc   打包，并用bzip2压缩
解压缩的话，把参数c换成x即可。
```
### 关机重启命令
```
shutdown
    -r    关机重启
    -h    关机不重启
    now   立刻关机
halt      关机
reboot    重启
```
### Linux 管道
将一个命令的标准输出作为另一个命令的标准输入。
```
ps -ef | grep mysql
netstat -tulpn | grep -i mysql
ps -aux |grep redis
```
### Linux 软件安装(centos yum安装)
更换yum源之前先备份原有的yum源，位于`/etc/yum.repos.d/`文件夹；
`yum clean all`清除所有缓存
`yum makecache`重新生成缓存
`yum update`更新系统软件

```
yum install package_name 下载并安装一个rpm包 
yum localinstall package_name.rpm 将安装一个rpm包，使用你自己的软件仓库为你解决所有依赖关系 
yum update package_name.rpm 更新当前系统中所有安装的rpm包 
yum update package_name 更新一个rpm包 
yum remove package_name 删除一个rpm包 
yum list 列出当前系统中安装的所有包 
yum search package_name 在rpm仓库中搜寻软件包 
yum clean packages 清理rpm缓存删除下载的包 
yum clean headers 删除所有头文件 
yum clean all 删除所有缓存的包和头文件 
```
### 用户及用户组
```
/etc/passwd    存储用户账号
/etc/group     存储组账号
/etc/shadow    存储用户账号的密码
/etc/gshadow   存储用户组账号的密码

useradd        添加用户名
userdel        删除用户名
usermod        修改用户
adduser        添加用户名
groupadd       添加组名
groupdel       删除组名
passwd root    给root设置密码
su root
su – root

/etc/profile   系统环境变量
bash_profile   用户环境变量
.bashrc        用户环境变量

su user        切换用户，加载配置文件.bashrc
su – user      切换用户，加载配置文件/etc/profile ，加载bash_profile
```
### 文件权限管理
文件的权限，文件的特殊权限，文件的特殊属性
```
ls -lh 显示权限 
ls /tmp | pr -T5 -W$COLUMNS 将终端划分成5栏显示 
chmod ugo+rwx directory1 设置目录的所有人(u)、群组(g)以及其他人(o)以读（r ）、写(w)和执行(x)的权限 
chmod go-rwx directory1 删除群组(g)与其他人(o)对目录的读写执行权限 
chown user1 file1 改变一个文件的所有人属性 
chown -R user1 directory1 改变一个目录的所有人属性并同时改变改目录下所有文件的属性 
chgrp group1 file1 改变文件的群组 
chown user1:group1 file1 改变一个文件的所有人和群组属性 

find / -perm -u+s 罗列一个系统中所有使用了SUID控制的文件 
chmod u+s /bin/file1 设置一个二进制文件的 SUID 位 - 运行该文件的用户也被赋予和所有者同样的权限 
chmod u-s /bin/file1 禁用一个二进制文件的 SUID位 
chmod g+s /home/public 设置一个目录的SGID 位 - 类似SUID ，不过这是针对目录的 
chmod g-s /home/public 禁用一个目录的 SGID 位 
chmod o+t /home/public 设置一个文件的 STIKY 位 - 只允许合法所有人删除文件 
chmod o-t /home/public 禁用一个目录的 STIKY 位 

chattr +a file1 只允许以追加方式读写文件 
chattr +c file1 允许这个文件能被内核自动压缩/解压 
chattr +d file1 在进行文件系统备份时，dump程序将忽略这个文件 
chattr +i file1 设置成不可变的文件，不能被删除、修改、重命名或者链接 
chattr +s file1 允许一个文件被安全地删除 
chattr +S file1 一旦应用程序对这个文件执行了写操作，使系统立刻把修改的结果写到磁盘 
chattr +u file1 若文件被删除，系统会允许你在以后恢复这个被删除的文件 
lsattr 显示特殊的属性 
```
### crontab 执行定时任务

```
crontab file [-u user] 用指定的文件替代目前的crontab。 
crontab - [-u user] 用标准输入替代目前的crontab. 
crontab -1 [user] 列出用户目前的crontab. 
crontab -e [user] 编辑用户目前的crontab. 
crontab -d [user] 删除用户目前的crontab. 
crontab -c dir  指定crontab的目录。 

基本格式 : 
*　　*　　*　　*　　*　　command 
分　时　日　月　周　命令 
第1列表示分钟1～59 每分钟用*或者 */1表示 
第2列表示小时1～23（0表示0点） 
第3列表示日期1～31 
第4列表示月份1～12 
第5列标识号星期0～6（0表示星期天） 

30 21 * * * /usr/local/etc/rc.d/lighttpd restart 每晚的21:30重启apache
0 */1 * * * /usr/local/etc/rc.d/lighttpd restart  每一小时重启apache 
0 23-7/1 * * * /usr/local/etc/rc.d/lighttpd restart 晚上11点到早上7点之间，每隔一小时重启apache 

```

### find 文件搜索
find的用法较多，可以根据文件名，修改时间，文件类型，权限，大小等条件进行搜索。
```
find / -name file1 从 '/' 开始进入根文件系统搜索文件和目录 
find / -user user1 搜索属于用户 'user1' 的文件和目录 
find /home/user1 -name \*.bin 在目录 '/ home/user1' 中搜索带有'.bin' 结尾的文件 
find /usr/bin -type f -atime +100 搜索在过去100天内未被使用过的执行文件 
find /usr/bin -type f -mtime -10 搜索在10天内被创建或者修改过的文件 
find / -name \*.rpm -exec chmod 755 '{}' \; 搜索以 '.rpm' 结尾的文件并定义其权限 
find / -xdev -name \*.rpm 搜索以 '.rpm' 结尾的文件，忽略光驱、捷盘等可移动设备 
locate \*.ps 寻找以 '.ps' 结尾的文件 - 先运行 'updatedb' 命令 
whereis halt 显示一个二进制文件、源码或man的位置 
which halt 显示一个二进制文件或可执行文件的完整路径 

```
### 友情链接
 - [Linux常用命令大全](http://www.cnblogs.com/fnlingnzb-learner/p/5831284.html)
 - [鸟哥的Linux私房菜](http://linux.vbird.org/linux_basic/)
 - [Linux find 用法示例](http://www.cnblogs.com/wanqieddy/archive/2011/06/09/2076785.html)