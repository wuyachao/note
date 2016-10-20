#shell基础
##启动和退出
shell是区分大小写的。用户键入命令或shell脚本名，来执行指定的命令或shell脚本，中止按Ctrl+C。Ctrl+D退出系统。
spell 拼写检查   cal 日历
在/etc/passwd下可以看到用户使用的shell。
##shell语法
###注释
- #是shell的注释，如果在第一行出现且其后没有！表明是注释，或者在其他的任何地方，表示从#开始到行末（即下一个换行符）之间的所有内容。
###通配符
?：表示任意的单个字符
*：任意长度的任意
[]:表示匹配方在[]中的任意一个字符
{}：将大括号中的字符以及前导字符串和后即字符串滋味匹配条件
例如：ls 1[1,2].txt 或者1[12].txt 表示10或者11开头的文件, ll 1[1-9].txt 表明1-9之间的任意一个数字。-表明连续的一个数字或者字幕范围。
!:表示取反
###指定哪个shell
脚本的前两个字符是#!，表命令解释器的绝对路径名。
###IO重定向
linux中数据流可分为3类，数据输入，数据输出和标准错误输出。文件描述指针分别为0，1，2。结果会被重定向到显示器。

