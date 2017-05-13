---
title: git rebase 合并
type: "categories"
categories: 工具
---

## git rebase 合并

### 1.取消上一次的提交：
>   git revert HEAD

### 2.删除最近的两个提交：
>   git reset --hard HEAD~~ //可以是commit的序号
    回滚刚才误删掉的git reset --hard ORIG_HEAD

### 3.只将某一次分支的修改进行提交合并到master
>   1.先切换到最终合并后的分支
    2.git cherry-pick 99daed2 //如果要从某一个分支的所有提交合并到master：git merge --squash issue1（分支）

### 4.用rebase -i 汇合过去的提交
>   git rebase -i HAED~~
    后一个pick改为squash，再wq
    
### 5.用 rebase -i 修改提交
>   git rebase -i HEAD~~ //可以是commit的序号
    将pick改为edit
    打开编辑器进行修改
    修改完了之后 git add .
    git commit --amend
    git rebase --continue //如果发生冲突，解决冲突，然后再循环add....continue操作

### 6.把所有test分支的所有提交合并成一个提交，导入master分支，解决冲突后，在：
>   git add .
    git commit

### 7.和上次的提交一并提交
>	git commit --amend
