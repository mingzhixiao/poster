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