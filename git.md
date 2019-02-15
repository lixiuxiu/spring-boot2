# 1，git clone

远程仓库 git clone 下来

```
sh-3.2# git clone https://github.com/hyperledger/fabric.git
```

git branch 显示当前分支。这个命令以及以下命令一定要进入clone下来的目录下进行，先要 cd fabric.

```
sh-3.2# git branch
 * release-1.1
```

git tag 显示当前所有tag，切换分支或者版本用 git checkout  <tag>/<branch>

```
sh-3.2# git tag
baseimage-v0.0.11
v0.6.0-preview
v0.6.1-preview
v1.0.0
v1.0.0-alpha
v1.0.0-alpha2
v1.0.0-beta
v1.0.0-rc1
v1.0.1
v1.0.2
v1.0.3
v1.0.4
v1.0.5
v1.0.6
v1.1.0
v1.1.0-alpha
v1.1.0-preview
v1.1.0-rc1
v1.2.0
v1.2.0-rc1
```

```
sh-3.2# git checkout v1.0.0
Note: checking out 'v1.0.0'.
You are in 'detached HEAD' state.
```

Git branch -a 查看本地和远程分支，绿色的是本地分支。

git checkout 实际上是修改HEAD文件的内容，让其指向不同的branch。HEAD文件指向的branch就是当前branch. 一般来讲，HEAD的内容是指向staging（暂存区）的master文件的。

```
bogon:fabric lixiuxiu$ git branch -a
(HEAD detached at v1.0.0)
release-1.1
remotes/origin/HEAD -> origin/release-1.1
remotes/origin/feature/ca
remotes/origin/feature/convergence
remotes/origin/master
remotes/origin/release-1.0
remotes/origin/release-1.1
remotes/origin/release-1.2
remotes/origin/v0.6
remotes/origin/v1.0.0-preview
```



# 2,  git 加速

**Hosts是一个没有扩展名的系统文件,可以用系统自带的记事本等工具打开，作用就是将一些常用的网址域名与其对应的IP地址建立一个关联,当用户在浏览器输入一个需要登录的网址时,系统首先会自动从hosts文件中寻找对应的IP地址,一旦找到,系统会立即打开对应的网址,如果没有找到,则系统会再将网址提交到DNS域名解析服务器进行IP地址的解析**

计算机解析域名A和 B时，就解析到本机IP或错误的IP，达到了屏蔽网站A 和B的目的。约定 127.0.0.1 为本地计算机的IP地址, 0.0.0.0是错误的IP地址。如下：

127.0.0.1 # 要屏蔽的网站 A

0.0.0.0 # 要屏蔽的网站 B



1，打开 terminal。进入终端命令行模式sudo su, 输入密码，然后输入 vi /etc/hosts，打开hosts文档

2，然后打开hosts文件后，进入编辑模式，在英文字符下，按sift + i 进入插入模式

3 , 我们需要加入github的host地址，接下来我们需要知道github.com和github.global.ssl.fastly.net域名对应的ip地址。 用浏览器访问 IPAddress.com (需vpn)，输入github.com和github.global.ssl.fastly.net， 获得对应的ip。

4，在vi打开的hosts文件中添加如下格式：

192.168.xx.xx  github.com

185.31.17.xx   github.global.ssl.fastly.net

5， 然后esc退出编辑模式，在英文字符下，先输入：，然后输入wq，保存hosts文件，修改hosts结束。

6，最后，我们在命令行中输入sudo dscacheutil -flushcache，更新DNS缓存。此时，重新访问github就好了

但是网上其他人说速度提升了，我好像还是原来一样慢==，还没懂。