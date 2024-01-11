---
comments: true
---

# Git 和 GitHub 基本操作指南


## 1. 介绍

Git 是一个分布式版本控制系统，用于跟踪和管理项目中的代码更改。GitHub 是一个托管 Git 仓库的在线平台，它使多人协作变得更容易。

本指南将教你如何开始使用 Git 和 GitHub，以及如何解决可能出现的问题。

## 2. 安装 Git

首先，你需要在计算机上安装 Git。你可以从 [Git 官方网站](https://git-scm.com/downloads) 下载适用于你的操作系统的安装程序。

**具体安装教程不多赘述，请参考博客[安装Git](https://www.liaoxuefeng.com/wiki/896043488029600/896067074338496)。**

安装完成后，打开终端（在 Windows 上使用 Git Bash 或命令提示符，Mac 和 Linux 上使用终端），运行以下命令以确认安装成功：

```
git --version
```

## 3. 配置 Git

在开始使用 Git 之前，你需要配置一些基本信息，如你的用户名和电子邮件地址。运行以下命令来配置：

**切记切记，这边的your name指的就是你的帐号名字，引号里面的才需要你自己修改成你自己的用户名和邮箱**

```
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

注意`git config`命令的`--global`参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

## 4. 创建一个 GitHub 帐户

如果你还没有 GitHub 帐户，前往 [GitHub](https://github.com/) 注册一个帐户。

## 5. 创建一个新的仓库

1. 登录到 GitHub 帐户。
2. 点击右上角的加号（+）按钮，选择 "New repository"。
3. 输入仓库名称，选择**公开或私有**，然后点击 "Create repository"。

## 6. 克隆仓库

因为我们打算使用ssh的形式来完成和github的通信，所以需要跟着这篇博客来完成ssh的认证

[远程仓库](https://www.liaoxuefeng.com/wiki/896043488029600/896954117292416)， 后续我会自己写一篇更好理解的（画饼）。

要在本地计算机上使用仓库，你需要将其克隆到本地：

```shell
git clone git@github.com:your-username/your-repo.git
```

**替换 `your-username` 和 `your-repo` 为你的 GitHub 用户名和仓库名称。**

## 7. 添加和提交更改

1. 在本地编辑文件。
2. 使用以下命令将更改添加到暂存区：

```
git add 'filename'
可以使用 git add . 来把当前目录下所有文件都加进去
RockRockWhite:"git add .中.是相对当前工作路径的所有文件"
```

**记得我说的吗？引号里面的需要改成你自己的东西！**

3. 使用以下命令提交更改：

```
git commit -m 'Description of the changes'
```

## 8. 拉取和合并更改

在你开始工作之前，确保你的本地仓库是最新的。使用以下命令拉取远程更改并将它们合并到你的分支：

**记得我说的吗？引号里面的需要改成你自己的东西！确认你的远程仓库的主分支名称是main还是master！**

```
git pull origin 'your origin branchname'
```

## 9. 发布更改

将本地更改发布到 GitHub 仓库：

```
git push origin 'your origin branchname'
```

## 10. 常见错误和解决方案

### 1. 错误 : `fatal: remote origin already exists`

**解决方案**: 这表示你已经添加了一个名为 "origin" 的远程仓库。你可以使用以下命令来更改远程仓库的 URL：

```
git remote set-url origin new-url
```

### 2. 错误: `Permission denied (publickey).`

**解决方案**: 这表示你没有正确的 SSH 密钥配置。请参考 [GitHub SSH 密钥文档](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) 进行设置。

### 3. 错误：`fatal: not a git repository (or any of the parent directories): .git`

**解决方案**：这个错误通常表示你不在一个包含Git仓库的目录中。确保你在正确的目录中运行Git命令，或者在目标目录中初始化一个新的Git仓库：

```
git init
```

### 4. 错误：`error: Your local changes to the following files would be overwritten by merge`

**解决方案**：这个错误表示你有未提交的更改，而Git无法自动合并。你可以选择放弃本地更改或提交本地更改后再进行合并。如果要放弃更改，可以使用以下命令：

```
git stash
git pull origin branch-name
git stash pop
```

### 5. 错误：`remote: Support for password authentication was removed`

**解决方案**：GitHub 不再支持使用密码进行认证。你应该配置SSH密钥并使用它们进行认证。请查看 [GitHub的SSH密钥文档](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) 以设置SSH密钥。

### 6. 错误：`fatal: refusing to merge unrelated histories`

**解决方案**：这个错误通常发生在合并两个不相关的Git历史时。你可以使用以下命令来合并它们：

```
git pull origin branch-name --allow-unrelated-histories
```

### 7. 错误：`fatal: unable to access 'https://github.com/your-username/your-repo.git/': SSL certificate problem`

**解决方案**：这个错误可能是由于SSL证书问题引起的。你可以尝试切换到使用SSH协议来解决此问题，或者验证你的系统上的SSL证书。

### 8. 错误：`error: pathspec 'filename' did not match any file(s) known to git`

**解决方案**：这个错误通常表示Git无法找到指定的文件。确保文件名拼写正确，并且文件在Git仓库中。

### 9. 错误：`error: failed to push some refs to 'https://github.com/your-username/your-repo.git'`

**解决方案**：这个错误表示你试图将更改推送到GitHub仓库时发生问题。可能是因为你没有足够的权限或远程仓库已经有了一些不同的更改。确保你有权限并尝试使用`git pull`来合并远程更改，然后再次尝试`git push`。

## 12. 结语

这个指南涵盖了 Git 和 GitHub 的基本操作，以及一些常见的错误