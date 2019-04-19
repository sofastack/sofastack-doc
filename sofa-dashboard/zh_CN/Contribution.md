# 参与贡献

> 可以先去 [RoadMap](./RoadMap) 内了解下开发任务及未来规划。

## 准备工作

贡献代码前需要先了解 git 工具的使用和 GitHub 网站的使用。

* git 工具用法可以查看 [git官方书籍](http://git-scm.com/book/zh/v1)，需要阅读前几章来熟悉。
* git 协作流程可以查看这篇文章 [Git协作流程](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)

## GitHub 贡献代码流程

### 提交issue

不论您是修复 SOFADashboard 的 bug 还是新增 SOFADashboard 的功能，在您提交代码之前，在 SOFADashboard 的 GitHub 上提交一个 issue，描述您要修复的问题或者要增加的功能。

这么做有几个好处:

* 不会与其它开发者或是他们对这个项目的计划发生冲突，产生重复工作.
* SOFADashboard 的维护人员会对您提的bug或者新增功能进行相关讨论，确定该修改是不是必要，有没有提升的空间或更好的办法。
* 在达成一致后再开发,并提交代码，减少双方沟通成本，也减少 pull request 被拒绝的情况。

### 获取源码

要修改或新增功能，在提 issue 后，点击左上角的 `fork` 按钮，复制一份 SOFADashboard 主干代码到您的代码仓库。

### 拉分支

SOFADashboard 所有修改都在分支上进行，修改完后提交 `pull request`， 在 Code Review 后由项目维护人员 Merge 到主干。
因此，在获取源码步骤介绍后，您需要：

* 下载代码到本地,这一步您可以选择 git/https 方式.
    ```bash
    git clone https://github.com/您的账号名/sofa-dashboard.git
    ```
* 拉分支准备修改代码
    ```bash
    git branch add_xxx_feature
    ```
* 执行完上述命令后，您的代码仓库就切换到相应分支了。执行如下命令可以看到您当前分支：
    ```bash
    git branch -a
    ```
* 如果您想切换回主干，执行下面命令:
    ```bash
    git checkout -b master
    ```
* 如果您想切换回分支，执行下面命令：
    ```bash
    git checkout -b "branchName"
    ```

### 修改代码提交到本地

拉完分支后，就可以修改代码了。

#### 修改代码注意事项

* 代码风格保持一致

    SOFADashboard 通过 Maven插件来保持代码格式一致。在提交代码前，务必本地执行
    ```bash
    mvn clean compile
    ```
* 补充单元测试代码
* 新有修改应该通过已有的单元测试.
* 应该提供新的单元测试来证明以前的代码存在 bug，而新的代码已经解决了这些 bug<br />您可以用如下命令运行所有测试
    ```bash
    mvn clean test
    ```
* 也可以通过 IDE 来辅助运行。

#### 其它注意事项

* 请保持您编辑的代码的原有风格，尤其是空格换行等.
* 对于无用的注释，请直接删除
* 对逻辑和功能不容易被理解的地方添加注释。
* 及时更新文档

修改完代码后，执行如下命令提交所有修改到本地：

```bash
git commit -am '添加xx功能'
```

### 提交代码到远程仓库

在代码提交到本地后，就是与远程仓库同步代码了。执行如下命令提交本地修改到 github 上：

```bash
git push origin "branchname"
```

如果前面您是通过 fork 来做的,那么这里的 origin 是 push 到您的代码仓库，而不是 SOFADashboard 的代码仓库。

### 提交合并代码到主干的请求

在的代码提交到 GitHub 后，您就可以发送请求来把您改好的代码合入 SOFADashboard 主干代码了。此时您需要进入您的 GitHub 上的对应仓库，按右上角的 `pull request`按钮。选择目标分支,一般就是 `master`，系统会通知 SOFADashboard 的人员， SOFADashboard 人员会 Review 您的代码，符合要求后就会合入主干，成为 SOFADashboard 的一部分。

#### 代码 Review

在您提交代码后，您的代码会被指派给维护人员 Review，请耐心等待。如果在数天后，仍然没有人对您的提交给予任何回复，可以在 PR 下面留言，并 @ 对应的人员。
对于代码 Review 的意见会直接备注到到对应 PR 或者 Issue。如果觉得建议是合理的，也请您把这些建议更新到您的补丁中。

#### 合并代码到主干

在代码 Review 通过后，就由 SOFADashboard 维护人员操作合入主干了。这一步不用参与，代码合并之后，您会收到合并成功的提示。
