# 避坑_Hutool

## FileUtil

FileUtil的各种方法中避免传字符串路径，如果是相对路径，他会使用classpath下的相对路径，而不是应用当前的相对目录。