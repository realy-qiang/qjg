Git 命令回顾：

init : 					  在本地创建⼀个新的库
clone : 				  从服务器克隆代码到本地 (将所有代码下载)
status : 				 查看当前代码库的状态
add : 					  将本地⽂件添加到暂存区
commit : 			  将代码提交到本地仓库(与远程通信)
push : 					将本地代码推送到远程仓库(与远程通信)
pull :					   将远程仓库的代码拉取到本地 (只更新与本地不⼀样的代码)(与远程通信)
branch : 				分⽀管理
checkout : 			切换分⽀ / 代码回滚 / 代码还原
merge : 				  合并分⽀
log : 					     查看提交历史
diff : 					    差异对⽐
remote : 			    远程库管理

fetch:						将远程仓库的代码拉取到本地 (与远程通信)

.gitignore : 		   ⼀个特殊⽂件, ⽤来记录需要忽略哪些⽂件



Git Flow

分支：

+ master:主干分支，最稳定的代码，经过了严格的测试，可以随时上线
+ develop:开发分支，汇总了每个开发者完成的最新的功能，经过了初步的测试，稳定性不如master分支
+ feature:功能分支，开发者为了完成功能开发而创建的分支，每个功能创建一个分支

比如：

master

develop

feat-follow

feat-user

feat-social

fix-like等