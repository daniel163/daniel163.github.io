---
layout: post
title: git 命令简单总结
---

git 命令简单总结
Git常用操作命令收集：
1) 远程仓库相关命令

```
检出仓库：$ git clone git://github.com/jquery/jquery.git
查看远程仓库：$ git remote -v
添加远程仓库：$ git remote add [name] [url]
删除远程仓库：$ git remote rm [name]
修改远程仓库：$ git remote set-url --push[name][newUrl]
拉取远程仓库：$ git pull [remoteName] [localBranchName]
关联远程仓库： git remote add origin git@github.com:Y-lenny/git-partial-revert.git
推送远程仓库：$ git push [remoteName] [localBranchName]

新增 -- 本地clone后关联到远程仓库
git branch -a，列出所有分支名称如下：
remotes/origin/dev
remotes/origin/release
git checkout -b dev origin/dev，作用是checkout远程的dev分支，在本地起名为dev分支，并切换到本地的dev分支
git checkout -b release origin/release，作用参见上一步解释
git checkout dev，切换回dev分支，并开始开发。

直接把分支导入开发环境中
```

 
2）分支(branch)操作相关命令

```
查看本地分支：$ git branch
查看远程分支：$ git branch -r
创建本地分支：$ git branch [name] ----注意新分支创建后不会自动切换为当前分支
切换分支：$ git checkout [name]
创建新分支并立即切换到新分支：$ git checkout -b [name]
删除分支：$ git branch -d [name] ---- -d选项只能删除已经参与了合并的分支，对于未有合并的分支是无法删除的。如果想强制删除一个分支，可以使用-D选项
合并分支：$ git merge [name] ----将名称为[name]的分支与当前分支合并
创建远程分支(本地分支push到远程)：$ git push origin [name]
删除远程分支：$ git push origin :heads/[name]
删除远程分支：$ git push origin --delete dev 官方文档中
```


我从master分支创建了一个issue5560分支，做了一些修改后，使用git push origin master提交，但是显示的结果却是'Everything up-to-date'，发生问题的原因是git push origin master 在没有track远程分支的本地分支中默认提交的master分支，因为master分支默认指向了origin master 分支，这里要使用git push origin issue5560：master 就可以把issue5560推送到远程的master分支了。
    如果想把本地的某个分支test提交到远程仓库，并作为远程仓库的master分支，或者作为另外一个名叫test的分支，那么可以这么做。

// 提交本地test分支作为远程的master分支

```
$ git push origin test:master
```
//好像只写这一句，远程的github就会自动创建一个test分支
// 提交本地test分支作为远程的test分支

```
$ git push origin test:test
```
              

如果想删除远程的分支呢？类似于上面，如果:左边的分支为空，那么将删除:右边的远程的分支。


```
$ git push origin :test
```

刚提交到远程的test将被删除，但是本地还会保存的，不用担心

3）版本(tag)操作相关命令

```
查看版本：$ git tag
创建版本：$ git tag [name]
删除版本：$ git tag -d [name]
查看远程版本：$ git tag -r
创建远程版本(本地版本push到远程)：$ git push origin [name]
删除远程版本：$ git push origin :refs/tags/[name]
```

 
4) 子模块(submodule)相关操作命令

```
添加子模块：$ git submodule add [url] [path]
如：$ git submodule add git://github.com/soberh/ui-libs.git src/main/webapp/ui-libs
初始化子模块：$ git submodule init ----只在首次检出仓库时运行一次就行
更新子模块：$ git submodule update ----每次更新或切换分支后都需要运行一下
删除子模块：（分4步走哦）
1)$ git rm --cached [path]
2) 编辑“.gitmodules”文件，将子模块的相关配置节点删除掉
3) 编辑“.git/config”文件，将子模块的相关配置节点删除掉
4) 手动删除子模块残留的目录
```

 
5）忽略一些文件、文件夹不提交

```
在仓库根目录下创建名称为“.gitignore”的文件，写入不需要的文件夹名或文件，每个元素占一行即可，如
target
bin
*.db
```


6）版本回退和撤销修改


```
版本回退：git reset [--mixed | --soft | --hard] HEAD^100 | HEAD^^^ | commit id 进行版本回退 
版本回退：git revert HEAD^100 | HEAD^^^ | commit id 进行版本回退
```


注意： 
     1. git revert是用一次新的commit来回滚之前的commit，git reset是直接删除指定的commit。
     2. 在回滚这一操作上看，效果差不多。但是在日后继续merge以前的老版本时有区别。因为git revert是用一次逆向的commit“中和”之前的提交，因此日后合并老的branch时，导致这部分改变不会再次出现，
        但是git reset是之前把某些commit在某个branch上删除，因而和老的branch再次merge时，这些被回滚的commit应该还会被引入。
     3. git reset 是把HEAD向后移动了一下，而git revert是HEAD继续前进，只是新的commit的内容和要revert的内容正好相反，能够抵消要被revert的内容。

文件修改回退：$ git checkout -- readme.txt 
把readme.txt文件在工作区的修改全部撤销，这里有两种情况：
    1. 是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
    2. 是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
总之，就是让这个文件回到最近一次git commit或git add时的状态。
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。

如何使用git回退部分修改：http://alpha-blog.wanglianghome.org/2010/07/30/git-partial-rollback/








