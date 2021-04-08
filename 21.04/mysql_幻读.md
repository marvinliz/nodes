# Innodb中RR隔离级别是否能防止幻读

在快照读情况下，mysql通过mvcc来避免幻读。

在当前读情况下，mysql通过next-key来避免幻读。



资料

- [讨论](https://github.com/Yhzhtk/note/issues/42)
- [美团技术团队](https://tech.meituan.com/2014/08/20/innodb-lock.html)



