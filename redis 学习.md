# redis 学习

#### 1.启动redis服务器

在redis的相应目录打开cmd控制台输入 redis-server.cmd     redis-windows.config 这个命令，这样便启动了redis服务器。

#### 2.启动redis客户端

在redis的相应目录打开cmd控制台输入 redis-server.cmd  -h 127.0.0.1 -p 6379 这个命令是启动redis的客户端，主机是本地主机 端口是6379

#### 3.redis中存储的数据类型（五种）

 字符串String  列表 list   哈希 hash   集合  set    有序集合   zset

#### 4.在redis中去存储数据

##### a.存储字符串 String

set first one   其中 first是键名 one是存储的数据类型 且为字符串类型

##### b.存储列表 list

lpush second two 其中second是键名 two是存储的数据类型，类型为list

##### c.存储哈希 hash

##### d.存储集合 set

##### e.存储有序集合zset

#### 5.查看数据内同

get first 反馈是one 

gei second 反馈是two

#### 6.在redis中查看数据类型

##### a.查看指定的键名所存储的数据类型

type fist  输入这个指令会得到一个反馈 String

type second 输入这个指令会得到反馈 list

#### 7.查看所有的键名

keys *

#### 8.在redis中删除数据

##### a.删除制定键名的数据

del first 这个指令便删除了存储数据为one且键名为first的数据

#### 

#### 