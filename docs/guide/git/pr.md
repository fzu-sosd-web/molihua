# 添加你的名片！

任务要求：在 `https://github.com/Fzu-SOSD-Lab/git-lab/` 的lab1文件夹下添加你的电子名片

名片格式：可以参考[我的名片](https://github.com/Fzu-SOSD-Lab/git-lab/blob/main/lab1/luke.md)


!!! question "那么如何把你的名片添加到我们的仓库呢？"

# 如何在 GitHub 上提交 Pull Request

在 GitHub 上提交 Pull Request（PR）是协作开发项目中的重要步骤之一。通过提交 PR，你可以向项目贡献代码、修复错误或改进项目。以下是提交 PR 的简单步骤。

## 步骤 1：Fork 项目

首先，在你的 GitHub 帐户中 Fork 项目的仓库。Fork 意味着创建项目的一个副本，该副本将在你的 GitHub 帐户中。

1. 打开你要贡献的项目的 GitHub 仓库页面。
2. 点击右上角的 "Fork" 按钮。

## 步骤 2：克隆仓库

将你 Fork 的项目克隆到本地计算机：

记住，这边clone的仓库是你自己的github仓库，千万记住哦，下面引号，你懂的。

```bash
git clone https://github.com/"xxxxxxxxx"/git-lab.git
```

## 步骤 3：创建分支

为了使你的更改有组织，创建一个新的分支：

这个引号，我建议可以是 'add-self-intro'，描述你的行为。

```bash
git checkout -b 'new branch name'
```

## 步骤 4：进行更改

在新分支上进行你的更改，可以编辑文件、添加新功能或修复错误。

在更改前，请确认你的本地开发分支是最新的状态！

## 步骤 5：提交更改

将你的更改添加到暂存区并提交：

```shell
git add .
git commit -m "描述你的更改"
```

## 步骤 6：推送分支

将你的新分支推送到你的 GitHub 仓库：

```shell
git push origin 'new branch name'
```

## 步骤 7：创建 Pull Request

1. 打开你 Fork 的项目的 GitHub 仓库页面。
2. 在仓库页面上，点击 "New Pull Request" 按钮。
3. 在比较页面中，选择你刚刚创建的分支。
4. 输入一个描述你的 PR 的标题和说明。
5. 点击 "Create Pull Request" 按钮。

## 步骤 8：等待审查

项目维护者也就是我将会审查你的 PR。我可能会要求你进行更改，所以请保持耐心并按照他们的建议进行操作。

## 步骤 9：PR 合并

一旦你的 PR 被审查并通过，项目维护者将合并你的更改到主分支。

## 结语

恭喜你，你已经成功提交了一个 Pull Request！通过这种方式，你可以为更多的开源项目做出贡献，改进代码库，提高你的编程技能。