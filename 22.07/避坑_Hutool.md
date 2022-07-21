# 避坑_Hutool

## FileUtil

FileUtil的各种方法中避免传字符串路径，如果是相对路径，他会使用classpath下的相对路径，而不是应用当前的相对目录。

## Wrapper

不会包装包含空格的字符串，有些数据库表格和字段允许创建带空格的，导致wrapper方法失效。