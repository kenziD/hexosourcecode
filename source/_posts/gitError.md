title: gitError
date: 2016-05-07 22:10:38
tags: git
---
今天遇到一个问题，在电脑a上修改了一些文件，并且没有add。突然断电了。我就打开电脑b，git fetch
git merge。修改好了。上传上去。电脑a开起来后。我直接git fetch／git merge。提示错误：
```
Updating 1463929..0e12104
error: Your local changes to the following files would be overwritten by merge:
	changeBgColor/popup.html
	changeBgColor/popup.js
Please, commit your changes or stash them before you can merge.
Aborting
```
所以如果你在本地修改了乱七八糟一堆没用的，就可以用
```
git reset --hard
git fetch
git merge
```
就可以了。```git reset --hard```的作用是把所有文件恢复到上一次commit时的状态。

如果还没```git add```，想单独恢复一个文件：```git checkout filename```就可以了。