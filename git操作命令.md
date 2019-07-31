# git操作命令

#### 1.查看当前目录是否有文件需要使用git操作

git status 

#### 2.添加数据 （进暂存）

git add 文件完整名

eg：git add wzw.txt

eg2: git add Wzw.java

#### 2.移除数据  （从暂存移除）

git rm --cached 文件完整名

eg：git rm --cache wzw.txt

eg2: git rm --cache Wzw.java

#### 4.提交数据进git缓存

git commit -m "修改的信息" 文件完整名

eg: git commit -m "first commit"  Wzw.java

eg2: git commit -m "second commit , add something" Wzw.java

#### 5.查看提交的版本信息

git log 查看完整的提交信息

git log --pretty=oneline 提交的信息显示在一行，带有自定义提示修改信息

git log --oneline  提交信息显示在一行，是--pretty的精简版，（最前面的hash数字权当id）id是缩略的。

git reflog 带指针的显示提交信息  （目前觉得最优）

#### 6版本的回退

##### a.基于id的指定回退（目前最优）

git reset  --hard id

eg: git reset --hard f0fa61b

##### b.使用亦或符号回退（不好用）

git reset --hard HEAD ^

一个^退一个版本，几个^退几个版本

eg: git --hard HEAD ^^ (当前HEAD回退两个)

##### c.使用波浪线回退（亦或符号的优化版）

git reset --head HEAD ~5（波浪线后面的数字代表回退的版本数）

##### 7.文件删除

rm 完整文件名

eg:rm Wzw.java

##### 8.文件的恢复

git reset --hard id  参考6的版本的回退。

