---
title: 代码被revert,你pull了master,代码没了,咋找回来?
date: 2021-11-21 21:36:54
---
## 问题场景

实际工作中，在上线时，你的新功能代码都是在gitlab上提交merge to master的请求，拥有merge权限的领导通过后，你的代码才合到master。好，QA开始上线了你的代码，这时 啪，啪，报警了，代码有问题。领导revert了你的代码以保证master分支的正确。这时，你的分支pull了master后，你发现你分支上的新功能代码都没有了！！咋找回来

本文从实战角度来解决这个问题

## 条件

1.  假设你已经有了一个`git项目: test-git`，并且有两个分支: `master，test`

## 实战

在`test`分支，你创建了一个文件`welcome`，文本:`hi git`。并`git commit -m '测试git revert'`提交

当前，`master，test`分支的内容分别如下

```
~/tt/test-git>>master $ ll
drwxr-xr-x  3 tt  staff    96B  5  8 20:54 test
-rw-r--r--  1 tt  staff    16B  8  1 19:55 test3.log
复制代码
```

```
~/tt/test-git>>test $ ll
drwxr-xr-x  3 tt  staff    96B  5  8 20:54 test
-rw-r--r--  1 tt  staff    16B  8  1 19:55 test3.log
-rw-r--r--  1 tt  staff     7B  8  1 20:19 welcome

~/tt/test-git>>test $ cat welcome
hi git
复制代码
```

现在，向`master`提交`merge to master`请求，如下图 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dcd9a425d2e44d16801af56e13020286~tplv-k3u1fbpfcp-zoom-1.image)

领导`merge`后开始上线，假设线上出现问题，领导`revert`了这次提交的代码。如下图 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9d8bc0999b46479497bc5ffd7d224676~tplv-k3u1fbpfcp-zoom-1.image)

接着你要查看问题，所以你会本地操作，回到你的代码分支，执行`git merge origin/master`

```
~/tt/test-git>>test $ git merge origin/master
Updating 670adc2..d3961f7
Fast-forward
 welcome | 1 -
 1 file changed, 1 deletion(-)
 delete mode 100644 welcome
~/tt/test-git>>test $ ll
total 8
drwxr-xr-x  3 tt  staff    96B  5  8 20:54 test
-rw-r--r--  1 tt  staff    16B  8  1 19:55 test3.log
复制代码
```

此时，你发现你提交的代码没有了。`welcome`文件不见了，咋办

咋找回来这些代码呢？下面开始找回操作

1.  `git log`找到领导`revert`你代码的那个`commit id`，这里`commit id`是`50a06845da879ab76e6fdd55dce923826742dcb2`。如下图三个`commit`的说明 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c3aefc8590da49dbbbdd5e17a41abdb8~tplv-k3u1fbpfcp-zoom-1.image)
1.  `git revert --no-commit 50a06845da879ab76e6fdd55dce923826742dcb2`

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0c7b978f306c444f824bbf0dd7f91100~tplv-k3u1fbpfcp-zoom-1.image) 你的代码已经回来了，然后就没有然后了，简单吧

## 总结

找到领导`revert`你代码的`commit id`，然后`git revert --no-commit commit id`

一句话总结：把之前`revert`的那条`commit`再`revert`一下
