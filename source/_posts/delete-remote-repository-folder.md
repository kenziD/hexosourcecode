title: 删除远程仓库的文件
date: 2015-11-16 10:10:37
tags: git
---
经常遇到本地文件已经删了,然后执行git add .

再git commit ,git push 。远程仓库上那些删掉的文件还是在。

因为git add不能识别删除的文件。git rm才可以。

<!-- more-->

正确的做法是：
```
git rm folder_name
git commit -m "xxx"
git push 
```
# rm 和 git rm 的区别

rm是在本地删除它，git rm 是在暂存区删除它

如果只执行了rm，本地是删除掉了，用git status 查看会发现它提示你

![](http://7xk7fp.com1.z0.glb.clouddn.com/git.png)
用git rm 来更新等一下上传的东西。也就是说如果只执行了rm，并没有执行git rm然后就直接commit ，工作区还是会有你删掉的那个文件。

如果直接一上来就用git rm删除了文件，本地文件会被删除，暂存区也会删除，所以就可以直接提交了。

# git rm 和 git rm --cached

这个 --cached选项允许你把文件保留在你的硬盘上.常见情况是有一些配置文件或者ide生成的工程文件你不想传到远程仓库，但又很重要不能在本地删除掉。
使用git rm --cached file_name 就可以使这个文件不被git版本控制所追踪。

或者像上一个例子中使用了rm 就直接提交了，本地已经没有了这个文件，但是远程仓库上还存在，只要使用it rm --cached file_name(或者git rm file_name都可以,这时候两者结果一样)就可以把在暂存区中的file删掉，commit 后再push,远程仓库上应该就删掉了。

# 总结：

* ```rm```只能删除本地文件
* ```git rm```不仅删了本地文件，也删了暂存区文件
* ```git rm --cached``` 只删除了暂存区文件

# 实验过程

![](http://7xk7fp.com1.z0.glb.clouddn.com/rma.png)
![](http://7xk7fp.com1.z0.glb.clouddn.com/gitrma.png)
![](http://7xk7fp.com1.z0.glb.clouddn.com/rmb.png)
![](http://7xk7fp.com1.z0.glb.clouddn.com/gitrmcacheb.png)