# 介绍

我一直想开展做代码历史的分析，也就是一个项目是如何构建到今天的。

幸运的是 Git 这个工具提交了便捷的工具，它可以方便的导出提交历史，也就是Git diff文件。

因此也有了个小研究的开始。

我的工作流目前是这样： 

case1: 能否找到最早的提交，并且导出git diff文件

git log --reverse --oneline | head -1

得到的输出是：
dd013f6 Initial commit: Superpowers plugin v1.0.0

用 git show 来导出内容。

然后我们让AI帮我们分析提交内容，目前没有格式要求。

