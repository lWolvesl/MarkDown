# 个人博客



## 技术栈

- Spring+Vue

## 后端开发

- springboot项目
- 整合mybatis plus
- 统一结果封装
  - ShrioConfig
  - AccountRealm
  - JwtToken
  - AccountProfile
- 异常处理
- 实体校验
- 跨域问题
- 登陆接口开发
- 博客接口开发
- 总结

## 数据库

```mysql
create table `m_user`
(
    `id`         bigint(20) not null AUTO_INCREMENT,
    `username`   varchar(64)  default null,
    `avatar`     varchar(255) default null,
    `email`      varchar(64)  default null,
    `password`   varchar(64)  default null,
    `status`     int(5)     not null,
    `created`    datetime     default null,
    `last_login` datetime     default null,
    PRIMARY KEY (`id`),
    key `UK_USERNAME` (`username`) using btree
) ENGINE = InnoDB
  AUTO_INCREMENT = 2
  default charset = utf8mb4;
  
create table `m_blog` (
    `id` bigint(20) not null auto_increment,
    `user_id` bigint(20) not null ,
    `title` varchar(255) not null ,
    `description` varchar(255) not null ,
    `content` longtext,
    `created` datetime not null on update current_timestamp,
    `status` tinyint(4) default null,
    primary key (`id`)
)ENGINE =InnoDB AUTO_INCREMENT=11 default charset = utf8mb4;
```

