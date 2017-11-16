1,git/svn 使用过程中遇到的问题讨论

 a,遇到的问题
	删除文件找回麻烦
	别人的代码环境影响自己的代码运行
	
	1,所有的项目就只有一个git 地址 每个项目一个git
	
	2,我想上周五我提交了哪些代码修改了哪些bug 怎么看？ gitlab
	
	3,领导总是想做代码审查，review gitlab 的直接原因
	
	4，缺少git 后台关系，导致现在代码关系混乱。

dev  master  online

 b，正确使用git
 	git 冲突了如何解决
 	Git 代码回滚到上一个(上某个)版本怎么办
 	git 分支 
 	git 分支管理权限
 	git 分支合并
 	git tag 



2,git 常用命令介绍

git add 
git commit -m 'xxxxx'
git pull
git push

查看远程仓库

git branch -a 查看远程分支

git branch -av 查看远程分支并显示最后一次提交的信息

git remote show origin 查看远程分支与本地的对比
——————————————————————————————————————————————————————————————
创建分支：
git branch 分支名称

切换分支
git checkout 分支名称

把分支提交到线上分支
git push origin 本地分支名称:远程分支名称


如果本地分支和线上分支名称不一致的话如何提交代码

git push origin test:onlinetest

合并分支
要想把test 分支代码合并到master 上面

1，先切换到master分支
 git checkout master

2, git meger test

当在一个分支里面开发没有提交代码 就想切换到另外一个分支中去修改bug
git stash   暂存

再次进入分支的话
git stash pop


修改分支
 test 现在的分支名称
 onlinetest 修改后的名称
git branch -m test onlinetest



删除分支
 1,本地分支 
 git branch -D onlinetest
 2，远程分支
 git push origin :onlinetest
 git push origin --delete origin/onlinetest



-----------------------------------------------------------------
查看tag
git tab

创建tag
git tag -a tag名称 -m '备注'
git tag -a v1.5.1_kszzy -m '测试打了一个tag'

	sha  commit_id  id
f0d7b0a08d27fbfbab5f75f27f6a10bfead929ee = v1.5.1_kszzy

把创建的tag 推动到服务器上
 1,git push origin  v1.5.1_kszzy
 2, git push origin --tags


查看tag id
git show v1.5.1_kszzy
commit f0d7b0a08d27fbfbab5f75f27f6a10bfead929ee



如果过想看tag的代码
1，git stash save '暂存名称'  //先暂存当前分支
2， git reset --hard f0d7b(commit_id) //得到 tag里面的代码 ，可以浏览
3，git stash pop


删除tag
1.本地分支
 git tag --delete v1.5.1_kszzy
2,删除远程分支
git push origin --delete  v1.5.1_kszzy



-----------------------------------------------------------------

合并冲突
/**
 * dev
    *
     *
 * 关于我们
    *
    <<<<<<< HEAD
 * master
    *
 * master
  =======
   *
 * dev
    *
 * dev
    *
>>>>>>> dev
>>>>>>>  */
>>>>>>>  1，HEAD 永远指向的是自己的分支，也就是你当前开发所在的分支，相当于一个指针。

 2，dev 是指的另外一个分支。

 3，=======  是分界线   

 4， HEAD 到 =======  之间的代码是当前分支的代码
 	 ======= 到dev 是另外一个分支的代码。	

-----------------------------------------------------------------

代码回滚

1,用git reflog 找到 commit_id

git reset -- hard commit_id //回退到指定的版本

-----------------------------------------------------------------

gitlab 使用

1,管理员添加用户


​	

![主面板](/Users/lihao/Desktop/gitlab用图/主面板.png)



2，设置用户邮箱登陆名称系统会自动发邮件



3，用户收到邮箱后重置密码

![设置密码](/Users/lihao/Desktop/gitlab用图/添加key.png)

4，添加sshkey



5，添加用户到组



6，创建组

7，把用户添加到组中



![添加用户到组](/Users/lihao/Desktop/gitlab用图/添加用户到组.png)



8，用户就可以改组的所有项目



9，在组中创建项目






3,git 分支介绍

4,git tag 使用

5,gitlab 使用

下午给大家讲解一下git 使用