---
title: git stash最佳实践整理
categories:
  - card
---

## 1. 在切换分支前
当你在一个分支上工作，并且需要切换到另一个分支时，如果当前分支有未提交的更改，可以使用 git stash 暂时保存这些更改，以便顺利切换分支。

```bash
git stash
git checkout other-branch
```
## 2. 在拉取远程仓库前
在从远程仓库拉取更改之前，可以使用 git stash 保存当前的工作，以避免合并冲突。

```bash
git stash
git checkout other-branch
```
## 3. 在执行重大的重构或实验性更改之前
如果你计划对代码库进行重大的重构或实验性更改，可以先使用 git stash 保存当前的工作状态，这样可以随时恢复到之前的状态。

```bash
git stash
# 进行重构或实验性更改
git stash pop
```
## 4. 在修复紧急问题时
如果你正在处理一些工作，而突然需要修复一个紧急问题，可以使用 git stash 保存当前的更改，修复问题后再恢复工作。

```bash
git stash
# 修复紧急BUG
git stash pop
```

## 高级选项

* ```git stash save "message"```：添加描述性信息，以便以后更容易识别。
```bash
git stash save "WIP: Refactoring authentication module"
```
* ```git stash list```：查看所有 stash 条目。
```bash
git stash list
```
* ```git stash apply```：应用保存的 stash，但不删除它（相比 git stash pop）。
```bash
git stash apply stash@{0}
```
* ```git stash drop```：删除一个指定的 stash 条目。
```bash
git stash drop stash@{0}
```
* ```git stash branch <branchname>```：为 stash 创建并切换到一个新分支。
```bash
git stash branch new-branch stash@{0}
```
* ```git stash clear```：清除所有 stash 条目。
```bash
git stash clear
```