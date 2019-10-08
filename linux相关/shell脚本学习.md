#### shell脚本学习

#!/bin/bash
#file is exist or not
#by authors wzw 2019 10 8
echo -e "\033[32mplease speak Chinese\033[1m"
FILE=/wzw/abc.txt
CASEFILE=caseFile.sh
DIRECTORY=/wzw/shellDirectory



#判断是否存在着一个文件
if [ ! -f "$FILE" ];then
  touch /wzw/abc.txt
 echo -e "\033[32m创建了个新的文件$FILE\033[1m"
else
 echo -e "\033[32m已经存在$FILE该文件\033[1m"
fi
#判断上一条命令是否执行成功
if [[ $? == 0 ]]
 then
 echo -e "\033[32m执行成功了\033[1m"
else
 echo -e "\033[32m执行失败了\033[1m"
fi
ggsimida
#判断上一条命令是否执行了
if [[ $? == 0 ]]
 then
 echo -e "\033[32m执行成功了\033[1m"
else
 echo -e "\033[32m执行失败了\033[1m"
fi
#判断是否是存在一个文件夹
if [[ ! -d $DIRECTORY ]];then
 mkdir -p $DIRECTORY
 echo -e "\033[32m创建了一个$DIRECTORY\033[1m"
                                                             

