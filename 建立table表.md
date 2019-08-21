create table `t_user`(
`id` int(10) not null auto_increment COMMENT = '自增用户id',
`name` varchar(255) not null DEFAULT '' COMMENT = '用户名称',
`password` varchar(255) not null DEFAULT 123456 COMMENT = '密码',
primary key(`id`),
)
ENGINE = InnoDB AUTO_INCREMENT = 1234 CHARSET = UTF8 COMMENT = '用户表'


create table `t_role`(
`id` int(10) NOT NULL 	AUTO_INCREMENT COMMENT = '角色自增id',
`name` varchar(255) NOT NULL  DEFAULT `老师` COMMENT  = `角色名称`,
primary key(`id`)
)
ENGINE = InnoDB AUTO_INCREMENT = 1234 CHARSET = utf8 COMMENT = 角色表'


create table `t_user_role`(
`id` int(10) not null auto_increment comment = `用户角色自增id`,
`user_id` int(10) not null auto comment = `用户id`,
`role_id` int(10) not null comment = `角色id` ,
primary key(`id`),
unique key `myuniqueKey`('user_id',`role_id`)
) 
ENGINE = InnoDB auto_increment = 214 charset = utf8 comment = `用户角色表`