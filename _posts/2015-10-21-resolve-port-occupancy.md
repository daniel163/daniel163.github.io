---
layout: post
title: Windows利用命令行解决端口占用
---

查看端口占用情况

```
netstat -aon|findstr 8080
```

结果显示PID为`8788`的进程占用了该端口

```
TCP    0.0.0.0:8080           0.0.0.0:0              LISTENING       8788
TCP    [::]:8080              [::]:0                 LISTENING       8788
```

根据PID查询进程信息

```
tasklist|findstr 8788
```

PID为`8788`的进程为`javaw.exe`

```
javaw.exe                     8788 Console                    1    181,076 K
```

终止该进程

```
taskkill -f -pid 8788
```