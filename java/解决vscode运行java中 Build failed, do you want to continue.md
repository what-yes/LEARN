# 解决vscode运行java中 Build failed, do you want to continue?

代码没有问题的情况下，虽然可以通过点proceed来运行代码但确实不太方便

![20211129210555](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/20211129210555.png)

终于功夫不负有心人，在stackoverflow上找到了解决办法：

在settings.json文件中添加如下代码：

`"java.debug.settings.forceBuildBeforeLaunch": false`

当然，这只是一种办法，有些人还说可以清除什么缓存等等。