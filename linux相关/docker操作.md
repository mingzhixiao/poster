### docker操作

##### 1.检查内核版本，需要3.10以及以上

uname -r

##### 2.安装docker

yum install docker

##### 3.启动docker

systemctl start docker

##### 4.开机启动docker

systemctl enable docker

##### 5.停止docker

systemctl stop docker

##### 6.docker检索

docker search redis

docker search mysql

docker searhc tomcat

##### 7.docker检索的地址

http://hub.docker.com

##### 8.docker拉取镜像

docker pull tomcat  默认拉取的版本号是last

docker pull mysql

docker pull mydql:5.5 其中5.5是版本号

##### 9.查看本地的镜像

docker images

##### 10.启动docker中的镜像

docker start 镜像id -d -p 8080:8080 tomcat

##### 11.删除docker中的镜像

docker start 镜像id

##### 12.查看启动的镜像

docker ps 

##### 13.查看启动与关闭的镜像

docker ps -a

