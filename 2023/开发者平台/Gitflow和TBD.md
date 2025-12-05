# Gitflow
[Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) 是出现时间较早的工作流，因使用了较多分支，流程颇为复杂。
## 简介
与基于主干的开发(Trunk-based development)相比，Gitflow会有很多长期存在的分支，这里有很多的提交(commits)。在这种模式下，开发人员创建一个功能分支，并延迟将其合并到主干分支，直到功能开发完成。这些长期存在的分支需要更多的协作才能合并，并且有很高的偏离主干分支的风险，还会引入冲突。  
## 原理
首先它有main和dev两条分支。main分支保存release历史记录，dev分支是feature分支的集成。main分支的所有提交也可以打上版本号的tag。  
dev分支包含了工程的所有历史，而main只包含简略版本。开发人员会建立dev的tracking branch。  
每开发新功能，都会创建自己的feature分支。feature分支以dev分支为父分支。开发完成后,feature分支合进dev分支。feature分支不直接和main分支交互。feature分支通常由最新的dev分支衍生。  
```
git checkout dev
git checkout -b feature_branch
```
开发完成后，merge到dev分支
```
git checkout dev
git merge feature_branch
```
当dev分支到了可以release的时候，先从dev分支fork一个release分支,新的feature不再允许加入release分支，只允许bug fix及doc等发布相关的东西。好了后就被merge到main分支，并打版本tag。同时release分支也merge回dev分支。
```
git checkout dev
git checkout -b release/0.1.0
```
当release分支被merge到main分支和dev分支后，就可以删除了。
```
git checkout main
git merge release/0.1.0
```
hotfix分支是一个维护分支，有点像feature分支或release分支，只是它是基于main分支。这也是能从main分支fork的唯一分支。当完成后，应被同时merge到main分支和dev分支。main分支更新版本号tag。
```
git checkout main
git checkout -b hotfix_branch
```
```
git checkout main
git merge hotfix_branch
git checkout dev
git merge hotfix_branch
git branch -D hotfix_branch
```

## 问题
### 合并冲突
合并冲突在使用Git Flow是非常常见的。原因很简单