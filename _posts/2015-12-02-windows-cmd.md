---
layout: post
title: 使用windows中CMD命令实现定时提醒功能(转载收藏)
---

有很多第三方软件具备闹钟定时提醒功能，但如果不想下载和安装第三方软件，则可通过windows中的CMD命令实现定时提醒功能。这里涉及到两个cmd命令：at和mshta。

基础知识：


1、at命令

at命令是Windows中的计划任务命令行，可指定时间和日期运行命令和程序。at命令必须在计划服务(Task Scheduler)开启的前提下使用。

常见的功能如：


	at 22:30 shutdown -s -t 30


//当天晚上十点半关机，延时30秒，该命令只执行一次。如命令运行时系统时间超过22：30，则会自动推迟在第二天执行。

定时提醒功能使用到at命令的两个参数：

/interactive —— 允许作业在运行时与当时登录的用户桌面进行交互。

/every —— 每个月或每个星期在指定的日期运行命令。


	示例命令：at 22:30 /interactive /every:M,T,W,Th,F,S,Su shutdown -s -t 30


//每天晚上十点半关机，延时30秒，可执行多次，直到人为取消该计划。此处/interactive参数可不加，因为shutdown命令无须与用户桌面进行交互，而在定时提醒功能实现命令中必须包含此参数。


2、mshta命令

	mshta.exe是hta(HTML Application HOST)文件解释器程序，同时利用mshta命令也可运行vbscript和javascript脚本。

下面给出一些示例：

	mshta C:\abcd9.hta  //打开本地C盘根目录下的abcd9.hta文件

	mshta http://www.abcd9.com/  //以hta方式打开www.abcd9.com页面

	mshta vbscript:CreateObject("Shell.Application").MinimizeAll()(close)  //执行vbs脚本最小化功能

	mshta vbscript:msgbox("是否确定？",36,"确认")(window.close)  //弹出vbs脚本对话框


mshta javascript:alert('welcome to www.abcd9.com');window.close();  //弹出js脚本的alert窗口
注意：执行vbs脚本后加(close)或(window.close)，以及执行js脚本后加windows.close()，是为避免弹出hta窗口。如不加close，则会在执行vbs/js脚本时弹出hta窗口影响使用效果，但不影响功能实现。


定时提醒功能实现：

结合at命令和mshta命令的功能，定时提醒功能也很容易实现，示例命令如下：

	at 22:30 /interactive /every:M,T,W,Th,F,S,Su mshta vbscript:msgbox(\"吃药时间到了\",64,\"定时提醒\")(window.close)

//每日晚上十点半准时弹出信息框提醒“吃药时间到了”。注：此处需要加上反斜杠“\”对双引号进行转义。

该命令可多次运行添加多个提醒计划。取消所设置的计划命令也很简单：通过 at 命令查看计划作业ID，通过 at ID /del  命令进行删除。