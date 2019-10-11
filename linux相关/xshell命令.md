## xshell命令合集

#### 1.windows 和centos之间的文件互传

pscp root@193.112.94.98:/wzw/application/a.txt D:\文件夹   将远程地址中的a.txt文件传送到D盘当中来。

#### 2.文件的赋值粘贴

cp a.txt  /wzw/application  将a.txt文件复制到目录 /wzw/application中去

#### 3.文件的重命名和移动

mv a.txt b.txt 将文件a.txt更改为b.txt

mv a.txt  /wzw/application 将a.txt文件移动到/wzw/application中去

#### 4.锁定当前目录位置

pwd

#### 5.查询文件所在的位置

whereis a.txt 

#### 6压缩和解压缩

tar zxvf a.tar 解压a.tar这个文件

tar -zcvf  /wzw/testWorkspace/gitTest/ a.tar.gz  将文件夹gitTest压缩成a.tar.gz这个文件

#### 7查看端口的使用情况

netstat -ntlp 这个命令会列举所有启动的端口

#### 8撤销与取消上次撤销

u 撤销  ctrl + r 取消上次撤销

#### 9用户相关操作

useradd zhangsan  添加张三这个用户

userdel zhangsan 删除张三这个用户

user -r zhangsan 删除张三和张三所拥有的目录

id zhangsan  判断张三这个用户是否存在

who am i 判断当前的用户是谁

usermod -g wudang zhangsan  将用户张三放于武当这个组

#### 10用户组的相关操作

groupadd wudang 添加一个叫做武当的组

goroupdel wudang 删除叫做武当的组

#### 11获取更高权限

su root 切换至root用户，需要输入密码

exit 退出切换的用户，返回本目录

#### 12运行级别7个

0关机模式

1单用户（找回密码模式）

2多用户无网络模式

3多用户有网络

4保留模式

5图形界面模式1

6 重启模式

init 3 切换至多用户有网路模式

init 6 重启

#### 13忘记密码

在开机的时候快速按entry键，然后输入命令e，这个命令是用来编辑引导的命令，直至有然后会出现三个选项 选择中间的kernel项，即内核选项，再输入一个e，会出现一个选择运行级别的界面，在这个界面输入1，即单用户（忘记密码）模式



#### 14.安装jdk

##### 1.解压安装包

tar zxvf jdk-8u181-linux-x64.tar.gz

##### 2.修改环境变量

vim /etc/profile

java环境变量配置 第一行是jdk的安装位置

export JAVA_HOME=/usr/local/jdk/jdk1.8.0_181
export CLASSPATH=$:CLASSPATH:​$JAVA_HOME/lib/ 
export PATH=$PATH:​$JAVA_HOME/bin

<https://blog.csdn.net/qq_42815754/article/details/82968464>

#### 15虚拟机安装centos

<https://blog.csdn.net/qq_23033339/article/details/80867195>

#### 16安装git

<https://www.cnblogs.com/imyalost/p/8715688.html>

#### 17安装maven

<https://www.cnblogs.com/zwcry/p/10043224.html>