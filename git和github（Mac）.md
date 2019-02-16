# git和github（Mac）

## （前言）git命令和简介

#### 1.创建新仓库 : git init

新建一个仓库之后，当前目录就成为了工作区，工作区下有一个隐藏目录 .git，它属于 Git 的版本库。

Git 版本库有一个称为 stage 的暂存区，还有自动创建的 master 分支以及指向分支的 HEAD 指针。



#### 2.检出仓库

执行如下命令以创建一个本地仓库的克隆版本：
`  git clone /path/to/repository` 
如果是远端服务器上的仓库，你的命令会是这个样子：
` git clone username@host:/path/to/repository`

如：

```
lixiuxiudeMacBook-Pro:Desktop lixiuxiu$ git clone git@github.com:lixiuxiu/blockchain.git
Cloning into 'blockchain'...
remote: Enumerating objects: 17, done.
remote: Counting objects: 100% (17/17), done.
remote: Compressing objects: 100% (15/15), done.
remote: Total 17 (delta 1), reused 11 (delta 0), pack-reused 0
Receiving objects: 100% (17/17), 81.48 KiB | 92.00 KiB/s, done.
Resolving deltas: 100% (1/1), done.
```

把github上指定仓库下载到本地，使用的是仓库的ssh地址，我们可以在本地桌面上看到一个blockchain文件夹，里面是和github一样的下载下来的文件



#### 3，工作流

我的本地仓库由git维护的三棵树组成，第一个是我的工作目录，里面是我的实际文件，第二个是暂存区（Index），它像个缓存区，临时保存我的改动，最后一个是head，指向我最后一次提交的结果。

working dir    —add—》  Index（stage）—commit—》 Head



#### 4.提交和添加

你可以提出更改（把它们添加到暂存区），使用如下命令：
`git add <filename>`
`git add *`
这是 git 基本工作流程的第一步；使用如下命令以实际提交改动：
`git commit -m "代码提交信息"`
现在，你的改动已经提交到了 **HEAD**，但是还没到你的远端仓库。



#### 5.推动改动

你的改动现在已经在本地仓库的 **HEAD** 中了。执行如下命令以将这些改动提交到远端仓库：
`git push origin master`
可以把 *master* 换成你想要推送的任何分支。 

如果你还没有克隆现有仓库，并欲将你的仓库连接到某个远程服务器，你可以使用如下命令添加：
`git remote add origin <server>`
如此你就能够将你的改动推送到所添加的服务器上去了。

如：

```
git remote add origin "https://github.com/lixiuxiu/blockchain.git"
```



Git 版本号

```
lixiuxiudeMacBook-Pro:fabric lixiuxiu$ git --version
git version 2.17.2 (Apple Git-113)
```

git 目录

```
lixiuxiudeMacBook-Pro:fabric lixiuxiu$ which git
/usr/bin/git
```



## （一）生成ssh

1.命令行输入

```
 ssh-keygen -t rsa -C "1659518714@qq.com"  
```

邮箱是登录github的邮箱, 然后会在默认文件里 /Users/lixiuxiu/.ssh/id_rsa 保存ssh,也可以自己选择路径

2.

```
pbcopy < ~/.ssh/id-rsa.pub 
```

是将 ~/.ssh/id-rsa.pub 文件里的ssh保存到剪贴板上，这样可以直接粘贴到其他位置

过程如下：

```
lixiuxiudeMacBook-Pro:TyporaNotes lixiuxiu$ ssh-keygen -t rsa -C "1659518714@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/lixiuxiu/.ssh/id_rsa):
/Users/lixiuxiu/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/lixiuxiu/.ssh/id_rsa.
Your public key has been saved in /Users/lixiuxiu/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:YYNvldmxAN/HLnCJTIeAPXAmv/9HUjZ1Ps/TmQYTz3M 1659518714@qq.com
The key's randomart image is:
+---[RSA 2048]----+
|      o+=oo.o    |
|      .*o+.O *  o|
|      . =.O * *o.|
|       o = o =+=E|
|        S   .o+oO|
|       . .  ...=+|
|          .  o. .|
|           .  .  |
|            ..   |
+----[SHA256]-----+
lixiuxiudeMacBook-Pro:TyporaNotes lixiuxiu$ pbcopy < ~/.ssh/id-rsa.pub
```

3.然后登陆github，在setting的ssh and GPG keys 里，创建新的ssh key，输入名字和ssh。成功后如下图第二个![image-20190215153503391](https://ws4.sinaimg.cn/large/006tKfTcgy1g075azo41xj316u0lqdjl.jpg)

4.测试github连接

```
lixiuxiudeMacBook-Pro:TyporaNotes lixiuxiu$ ssh -T git@github.com
Warning: Permanently added the RSA host key for IP address '192.30.253.112' to the list of known hosts.
Hi lixiuxiu! You've successfully authenticated, but GitHub does not provide shell access.
```

在使用Git提交前，必须配置用户名和邮箱，这些信息会永久保存到历史记录中。

```
lixiuxiudeMacBook-Pro:fabric lixiuxiu$ git config --global user.name "lixiuxiu"
lixiuxiudeMacBook-Pro:fabric lixiuxiu$ git config --global user.email 1659518714@qq.com
```



## （二） 本地文件上传到github上

切换到本地需要上传的项目目录里，只需如下几行命令，就可以上传代码到 github 上

如果有两个本地目录需上传到github的两个仓库中，以下过程需执行两次

```
git init
git add .   
git commit -m "first commit"
git remote add origin git@github.com:saysky/SpringBlog-.git
git push -u origin master
```



过程如下：

1..git init 初始化当前目录，即创建Git仓库。之后在管理员权限下可看到待上传到文件夹目录里多了.git 和 .DS_Store文件

```
lixiuxiudeMacBook-Pro:fabric lixiuxiu$ git init
Initialized empty Git repository in /Users/lixiuxiu/Desktop/TyporaNotes/fabric/.git/
lixiuxiudeMacBook-Pro:fabric lixiuxiu$ sudo ls
Password:
.DS_Store		fabric架构图.md		golang.md
.git			fabric网络操作.md	java concepts.md
Blockcerts.md		fabric运行步骤.md
fabric 1.1镜像.md	fabric网络操作.pdf
```

2. git add . 把当前目录下的所有文件或者他们的改动加入到缓冲区
3. git commit -m "first commit of fabric notes" 

```
lixiuxiudeMacBook-Pro:fabric lixiuxiu$ git commit -m "first commit of fabric notes"
[master (root-commit) 2b35352] first commit of fabric notes
 9 files changed, 436 insertions(+)
 create mode 100644 .DS_Store
 create mode 100644 Blockcerts.md
 create mode 100644 "fabric 1.1\351\225\234\345\203\217.md"
 create mode 100644 "fabric\346\236\266\346\236\204\345\233\276.md"
 create mode 100644 "fabric\347\275\221\347\273\234\346\223\215\344\275\234.md"
 create mode 100644 "fabric\347\275\221\347\273\234\346\223\215\344\275\234.pdf"
 create mode 100644 "fabric\350\277\220\350\241\214\346\255\245\351\252\244.md"
 create mode 100644 golang.md
 create mode 100644 java concepts.md
```

4."https://github.com/lixiuxiu/blockchain.git"是本地文件要上传到github 仓库的地址。

```
$ git remote add origin "https://github.com/lixiuxiu/blockchain.git"
```

![image-20190215160746218](https://ws3.sinaimg.cn/large/006tKfTcgy1g0768zsa0vj31le0owjy3.jpg)

5. git push -u origin master 将本地缓冲区的文件上传到作者github的上面命令指定的仓库中。

```
lixiuxiudeMacBook-Pro:fabric lixiuxiu$ git push -u origin master
Username for 'https://github.com': lixiuxiu
Password for 'https://lixiuxiu@github.com':
To https://github.com/lixiuxiu/blockchain.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/lixiuxiu/blockchain.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

可以看到有报错，是因为：本地没有update到最新版本的项目（git上有README.md文件没下载下来），git pull origin master 没有成功，可以通过如下命令进行代码合并【注：pull=fetch+merge] ：git pull --rebase origin master成功了

然后使用 git push -u origin master，查看github，我们可以看到文件已经成功上传到github上了。 

 * ```
  lixiuxiudeMacBook-Pro:fabric lixiuxiu$ git pull origin master
  warning: no common commits
  remote: Enumerating objects: 6, done.
  remote: Counting objects: 100% (6/6), done.
  remote: Compressing objects: 100% (4/4), done.
  remote: Total 6 (delta 0), reused 0 (delta 0), pack-reused 0
  Unpacking objects: 100% (6/6), done.
  From https://github.com/lixiuxiu/blockchain
  
  - branch            master     -> FETCH_HEAD
  - [new branch]      master     -> origin/master
    fatal: refusing to merge unrelated histories
  ```

 * ```
  lixiuxiudeMacBook-Pro:fabric lixiuxiu$ git pull --rebase origin master
  From https://github.com/lixiuxiu/blockchain
  
  - branch            master     -> FETCH_HEAD
    First, rewinding head to replay your work on top of it...
    Applying: first commit of fabric notes
    lixiuxiudeMacBook-Pro:fabric lixiuxiu$ git push -u origin master
    Counting objects: 11, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (11/11), done.
    Writing objects: 100% (11/11), 79.97 KiB | 15.99 MiB/s, done.
    Total 11 (delta 0), reused 0 (delta 0)
    To https://github.com/lixiuxiu/blockchain.git
    a8947aa..0bd669b  master -> master
    Branch 'master' set up to track remote branch 'master' from 'origin'.
  ```

  

###  (三)新增文件上传到github上

经过（二）操作后的本地文件夹新增加了一个文件，进行如下操作将新增文件上传到github上.

注：已经经过init和remote add后的不同本地目录下都是如下操作，不需要再次init和remote add就会把新增的文件push到对应的仓库中

```
git add  <filename.后缀>
git commit -m "xx"
git push origin master
```

如

```
lixiuxiudeMacBook-Pro:java web lixiuxiu$ git add git和github（Mac）.md
lixiuxiudeMacBook-Pro:java web lixiuxiu$ git commit -m "git and github"
[master 9263374] git and github
 1 file changed, 264 insertions(+)
 create mode 100644 "git\345\222\214github\357\274\210Mac\357\274\211.md"
lixiuxiudeMacBook-Pro:java web lixiuxiu$ git push origin master
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 4.15 KiB | 4.15 MiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/lixiuxiu/spring-boot2.git
   4d15abd..9263374  master -> master
```

