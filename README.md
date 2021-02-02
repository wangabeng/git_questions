# git问题汇总
# git的编译安装最新版本方法
	我购买的阿里云yum安装的git 竟然是很低的版本1.8.1的
	卸载重新安装步骤 参见https://www.cnblogs.com/oufeng/p/6614042.html

		--安装gcc
		sudo yum install gcc

		--安装g++
		sudo yum install gcc-c++

		--安装编译所需的包
		sudo yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel
		sudo yum install gcc perl-ExtUtils-MakeMaker
		复制代码
		下载源码(*.tar.gz)到指定的目录

		--下载文件到/usr/src/git-2.12.2目录
		wget -P ~/src/git-2.12.2 https://www.kernel.org/pub/software/scm/git/git-2.12.2.tar.gz
		切换到刚刚保存下载文件的目录并解压文件

		--切换到指定目录
		cd /usr/src/git-2.12.2/

		--解压源码包
		tar zxvf git-2.12.2.tar.gz
		进入解压目录

		cd git-2.12.2/
		配置安装目录并编译和安装
		./configure 是待编译文件的路径
		--prefix=/usr/local/git-2.12.2意思是设置这个程序编译后的安装的路径 你编译后的程序会放在/usr/local/git-2.12.2目录下
		sudo ./configure --prefix=/usr/local/git-2.12.2 && sudo make install

		编译安装完成后 新的版本会自动添加到环境变量中


# git push的权限问题 虽然我更新了git的最新版本 但是每到git push的时候 	还是会出现权限的问题 
	提示信息如下：
	Permission denied (publickey).
	fatal: Could not read from remote repository.

	解决方法：
	https://www.phpsong.com/1860.html

	git config --global push.default simple // 成功 即把git push的默认模式改为simple模式 何为simple模式：

	在中央仓库工作流程模式下，拒绝推送到上游与本地分支名字不同的分支。也就是只有本地分支名和上游分支名字一致才可以推送，就算是推送到不是拉取数据的远程仓库，只要名字相同也是可以的。在GIT 2.0中，simple将会是push.default的默认值。simple只会推送本地当前分支。
	修改以后 ~/.gitconfig文件的配置如下： 
	[user]
	        name = wangabeng
	        email = wangabeng@163.com
	[push]
	        default = simple


	备注：如果不设置 在第一次推送的时候 设置好推送的分支 应该也可以
	git push -u origin master 

	在git push的时候不需要sudo权限

	above all
	如果总是出现权限的问题 一般都是文件夹的权限问题 就把项目文件夹权限设置最高
	chmod -R 777 /www/itbulu.com/wp-content（参照 http://www.jb51.net/article/110277.htm）

# 遇到的问题 在本地删除了几个文件后 
	git add . // 出现错误提示
	* 'git add --all <pathspec>' will let you also record the removals.

	Run 'git status' to check the paths you removed from your working tree.
	
	// 于是执行
	git add --all  // 成功解决


# 需求1 (仅供参考 不是最佳实践)
	本地建了一个仓库 有文件a 需要上传到github上
 第一步 在本地文件夹根目录 初始化git仓库 git init

 第二步 本地仓库添加git源 git remote add origin git@github.com:wangabeng/aliyundata.git  此时 查看git源 git remote -v origin git@github.com:wangabeng/aliyundata.git (fetch) origin git@github.com:wangabeng/aliyundata.git (push)

 git修改源镜像
 git remote set-url origin newurl

 第三步 把本地代码提交到暂存区 工作区  git add . git commit -m 'add'

 第四步 合并当前分支到远程master分支 sudo git fetch origin master  此时会报错 因为本地没有工作流记录 fatal: refusing to merge unrelated histories  所以要添加参数 --allow-unrelated-histories 
 git fetch origin master --allow-unrelated-histories

 第五步 git push -u origin master  提交到远程仓库的master分支 -u设置默认提交的主机名（origin） 分支名（master）

 成功

# git需求 [最佳实践] 
	远程仓库已经创建好 本地只有一个空目录 如何把远程代码下载到本地
	https://www.douban.com/note/622560328/
	1 进入本地目录
	git init

	2 获取remote
	git remote add origin git@github.com:wangabeng/aliyundata.git

	3 从远程获取最新版本到本地
	使用如下命令可以在本地新建一个temp分支，并将远程origin仓库的master分支代码下载到本地temp分支(没有temp分支 就创建这个分支)
	$  git fetch origin master:temp  // 此时只有一个分支 temp
	
	4比较本地仓库与下载的temp分支
	使用如下命令来比较本地代码与刚刚从远程下载下来的代码的区别：

	$  git diff temp  

	合并temp分支到本地的master分支对比区别之后，如果觉得没有问题，可以使用如下命令进行代码合并：

	 $  git merge temp // 合并下载的temp分支到本地的master
	 合并以后 git branch查看 就有两个分支了
	   dev
		* master // 当前分支


	5删除temp分支
	如果temp分支不想要保留，可以使用如下命令删除该分支：

	$ git branch -d temp  // 删除分支     

	如果该分支的代码之前没有merge到本地，那么删除该分支会报错，可以使用git branch -D temp强制删除该分支。

	6 git add .
		git commit -m 'add'
		git push -u origin master // 第一次提交 设置提交当前分支到远程主机origin的master分支 并设置以后默认提交到origin master分支
		// 或者这样提交也可以
		git push --set-upstream origin master

# git遇到的问题之“Please make sure you have the correct access rights and the repository exists.”
```
1、首先我得重新在git设置一下身份的名字和邮箱（因为当初都忘了设置啥了，因为遇到坑了）进入到需要提交的文件夹底下（因为直接打开git Bash，在没有路径的情况下，根本没！法！改！刚使用git时遇到的坑。。。）

git config --global user.name "yourname"

git config --global user.email“your@email.com"

注：yourname是你要设置的名字，your@email是你要设置的邮箱。



2、删除.ssh文件夹（直接搜索该文件夹）下的known_hosts(手动删除即可，不需要git）



3、git输入命令

$ ssh-keygen -t rsa -C "your@email.com"（请填你设置的邮箱地址）



接着出现：

Generating public/private rsa key pair.

Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):



请直接按下回车



然后系统会自动在.ssh文件夹下生成两个文件，id_rsa和id_rsa.pub，用记事本打开id_rsa.pub



将全部的内容复制



4、打开https://github.com/，登陆你的账户，进入设置



进入ssh设置







在key中将刚刚复制的粘贴进去



点击add ssh key，



ok！



5、在git中输入命令：



ssh -T git@github.com



然后会跳出一堆话。。



输入命令：yes



回车



然后就会提示你成功了~~
```
# git 放弃本地更改 强制拉取
未提交同步----操作---打开命令提示符(C)  
```
git fetch --all
git reset --hard origin/master
git pull
```
 git fetch 指令是下载远程仓库最新内容，不做合并  
 git reset 指令把HEAD指向master最新版本  

# git打标签
```
Git Tag

    git tag -a 2.27 -m "release version 201606"
    git push origin --tags
    详解：git tag 是命令
        -a 0.1.3是增加 名为0.1.3的标签
        -m 后面跟着的是标签的注释    

     打标签的操作发生在我们commit修改到本地仓库之后。完整的例子
        git add .
        git commit -m “fixed some bugs”
        git tag -a 0.1.3 -m “Release version 0.1.3″

    分享提交标签到远程服务器上
       git push origin master
       git push origin --tags
       –tags参数表示提交所有tag至服务器端，普通的git push origin master操作不会推送标签到服务器端。

    删除标签的命令
        git push origin local_branch:remote_branch

```
# test添加信息
Test 添加信息