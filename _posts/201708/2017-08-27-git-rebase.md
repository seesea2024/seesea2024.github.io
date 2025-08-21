## git rebase / merge 区别

### git rebase
rebase操作的具体过程为：

![merge和rebase](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/git-rebase1.jpg?raw=true)

1. 确保工作区位于dev分支上：git checkout dev
2. 执行衍合操作：git rebase master
3. 如果有冲突需要先解决冲突，解决完冲突之后执行：git rebase --continue
4. 如果想放弃这次操作可以执行：git rebase --abort
5. 如果是想直接使用master分支取代此分支，可以执行：git rebase --skip

这些命令会把你的"dev"分支里的每个提交(commit)取消掉，并且把它们临时保存为补丁(patch)(这些补丁放到".git/rebase"目录中),然后把"dev"分支更新为最新的"master"分支，最后把保存的这些补丁应用到"dev"分支上。

### git merge
使用merge时在dev分支中的提交不会发生变化，这对于有其他人使用次分支时很重要，但是这个操作会产生一个合并提交，因此在将特性分支合并到主干master上时一般不使用merge
