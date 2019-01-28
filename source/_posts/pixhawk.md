title: 编译APM源码基于pixhawk硬件
date: 2015-11-27 11:04:26
tags: pixhawk
---
参考:[福利来了，关于在win7下编译pixhawk源码的教程](http://bbs.loveuav.com/thread-499-2-1.html)
根据这个教程做下来，执行make px4-v2的时候总是提示错误:
```
error:cannot determine sketchbook location - please specify on the commandline with SKETCHBOOK=<path>
```
出现这个错误的原因是文件路径里含有空格，把所有空格去掉了就好了。
MINGWIN 在win中遇到空格会有误解。

<!--more-->
比如
```
cd E:/Program\ Files
```
就是进入Program Files文件夹

如果
```
cd E:/Program Files
```
他会理解成进入Program文件夹


From the [MinGW "Getting Started" guide](http://www.mingw.org/wiki/getting_started):
> MinGW may have problems with paths containing spaces, and if not, usually other programs used with MinGW will experience problems with such paths. Thus, we strongly recommend that you do not install MinGW in any location with spaces in the path name reference; i.e. you should avoid installing into any subdirectory of "Program Files" or "My Documents", or the like.

