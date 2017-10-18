#### git操作--master分支reset后，dev分支更新新内容后，如果将新内容merge到master分支reset节点

#### 大体的解决思路
1. 前提是： master分支已经reset到节点A；
2. 首先将本地master，此时处于节点B（B节点晚于A节点），也reset到节点A；(也就是将远程master全覆盖本地代码，且原程master不存在的commit也消失)
```
git fetch --all
git reset --hard origin/master
git fetch 只是下载远程的库的内容，不做任何的合并git reset 把HEAD指向刚刚下载的最新的版本
```
3. 定义本地分支dev（dev分支是在B节点的基础上pull下来的，然后再添加了新commit，比如该节点为C）得B节点之后的第一个节点为B1，我们将B1--C节点之间的commit，merge到节点A。这样就删除掉了节点A到节点B之间的commit；
#### 具体解决办法：
1. 将本地master，reset到远程master的节点A
```
git fetch --all
git reset --hard origin/master
git fetch 只是下载远程的库的内容，不做任何的合并git reset 把HEAD指向刚刚下载的最新的版本
```
2. 基于分支dev创建一个新的分支newbranch，并指明新分支的最后一个commit,也就是节点C：
`git checkout -bnewbranch 62ecb3 `
3. 然后，rebase这个新分支的commit到master（--onto master）。76cada^ 指明你想从哪个特定的commit开始，也就是节点B1。
`git rebase --onto master 76cada^ `
4. 得到的结果就是feature分支的commit 76cada（B1节点） ~62ecb3（C节点） 都被合并到了master分支；
5. 此时你可以能发现远程master分支并没有变化，且远程没有newbranch分支。那这个时候，可以将newbranch分支push到远程，确认远程newbranch分支的commit记录是否正确；
6. 如果正确，则切换到master分支，进行git merge --no-ff newbranch就可以了；
**注意：可能还需要在github上对提交的pull reqest，通过手动点击的方式进行合并**

#### 其他注意事项：
1. 中间操作，可能会遇到因为冲突，无法切换分支的提示：git add解决一下；
2. 中间操作出现问题，如果想继续rebase，则使用git rebase --continue；不清楚时，使用git rebase --abort；
3. 常用git log打印一下记录；


