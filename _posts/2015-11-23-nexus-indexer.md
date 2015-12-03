---
layout: post
title : Nexus手动更新索引
---

访问[http://repo.maven.apache.org/maven2/.index/](http://repo.maven.apache.org/maven2/.index/)下载索引文件。

我们需要下载如下两个文件

[nexus-maven-repository-index.properties](http://repo.maven.apache.org/maven2/.index/nexus-maven-repository-index.properties)
[nexus-maven-repository-index.gz](http://repo.maven.apache.org/maven2/.index/nexus-maven-repository-index.gz)

*注：下载完成之后最好是通过MD5或者SHA1校验一下文件是否一致。*

索引文件为gz格式，但并不能直接解压，我们需要下载专用索引解压工具。

[indexer-cli-5.1.1.jar](http://central.maven.org/maven2/org/apache/maven/indexer/indexer-cli/5.1.1/indexer-cli-5.1.1.jar)

*注：indexer-cli-5.1.1.jar是专门用来解析和发布索引的工具，关于它的详细信息[请见这里](https://maven.apache.org/maven-indexer/indexer-cli/index.html)。*

将上面三个文件放置到同一目录下，运行如下命令

```
java -jar indexer-cli-5.1.1.jar -u nexus-maven-repository-index.gz -d indexer
```

将index目录下的所有文件放置到{nexus-home}/sonatype-work/nexus/indexer/central-ctx目录下，重新启动nexus。