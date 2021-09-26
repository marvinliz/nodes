通常Mysql的查询语句不是大小写敏感的，如果查询需要大小写敏感，需要用到mysql的`COLLLATE`，他规定了字符集的比较规则。

像末尾是ci,cs是大小写不敏感，binary是大小写敏感，比如utf8_bin。

```sql
SELECT * FROM users WHERE name like 'cRaZy' COLLATE utf8_bin;

ALTER TABLE users CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;

create table `users` (
   `id` bigint(20) NOT NULL AUTO_INCREMENT,
   `name` varchar(255) NOT NULL COMMENT '用户名',
   PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE utf8mb4_bin COMMENT='用户';
```

## 参考资料

- [Mysql collate, searching case sensitive](https://makandracards.com/makandra/19495-mysql-collate-searching-case-sensitive)
- [Case Sensitivity in String Searches](https://dev.mysql.com/doc/refman/8.0/en/case-sensitivity.html)

