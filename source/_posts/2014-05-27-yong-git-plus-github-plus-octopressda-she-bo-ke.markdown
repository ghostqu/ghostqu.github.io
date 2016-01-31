---
layout: post
title: " 用git+github+octopress搭设博客"
date: 2014-05-27 23:25:29 +0800
comments: true
categories: octopress github 
published: true
---

既然我答应大家要给大家一个[github][1]+[octopress][2]的教程，那么我就要说话算数，嗯，好吧那么下面给大家上步骤：
<!--more-->
##1.申请github账号，并建好respository

+ 要用github做博客，那么我们需要一个[github][1]的账号，那么这个注册还是很简单的，只要照着这个图填好即可。
<a href="" target="_blank"><img src="/images/github.png"/></a>
+ 然后回到[github][1]页面，点击图中第2个图标respository。
<a href="" target="_blank"><img src="/images/respositority.png"/></a>
+ 这时候会弹出这样一个页面，我们只需要按照** username.github.io **来填写进项目名称，这样github会给生成一个静态的托管页面，供我们搭建博客。
<a href="" target="_blank"><img src="/images/new.png"/></a>
**这里需要注意一点，上面的username一定要是你自己的用户名，而且要求是全部是小写[^1]。**用户名里有大写的童鞋可以注销账号重新申请了。

---

##2.安装ssh和git，并进行github的配置
在这里我先提一下我用的是ubuntu 14.04 LTS这是一个linux内核的系统，所以我们使用git就能方便的在github上进行pull或push的操作，使用**温妻**的同学可以自己在网上找一下教程[^2]。

* 安装过程很简单，只需要打开终端，输入
```bash
$sudo apt-get install git
$sudo apt-get install ssh
```
* github需要我们提供ssh密匙，首先要确定系统中是否已存密匙，在终端输入以下命令。
```bash
$cd ~/.ssh
$ls -al
```
如果，你的终端中显示有`id_rsa.pub`或者`id_dsa.pub`那么说明你已经有密匙了。
* 如果没有密匙，那么需要生成，终端输入
```bash
$ssh-kengen -t ras -C "your_email@nidongde.com"
```
确保自己没有输错的话，就一路enter，直到终端上显示类似
```
The key fingerprint is:
11:11:11:.................................
```
此时，向终端输入
```
$ssh-add ~/.ssh/id_rsa
```
这样我们就有了一个ssh密匙，接下来要把密匙加进github中。首先，无所谓用`gedit`还是`gvim`或者是`open office`打开`～/.ssh/id_rsa.pub`，将里面的内容`Ctrl+C`下来，接着点击github页面右上角的Account settings，即图中第二个图标。
<a href="" target="_blank"><img src="/images/set.png"/></a>
选择SSH keys，点击Add SSH key。将你刚才复制下来的东西统统`Ctrl+V`进**Key**里面。
<a href="" target="_blank"><img src="/images/key.png"/></a>
至于那个titile随便填个什么就好，甚至不填都没有问题。

+ 在git上配置好用户名和邮箱以便登陆，简简单单的在终端输入一下命令就好
```
$git config --global user.name "Your Name Here"
$git config --global user.email "your_email@example.com"
```
注意上面就是你**注册github时用的用户名和邮箱**千万不要填错了
+ 最后，测试一下好不好用，终端输入
```
$ssh -T git@github.com
```
第一次连的时候可能还要问你是不是想访问之类的问题，不管它，直接yes，如果输出类似

```
Hi ghostqu! You've successfully authenticated, but GitHub does not provide shell access.
```
就说明你访问成功了。



---
##3.安装Octopress

如果大家对自己英文水平有自信可以直接访问[Octopress][2]的主页，因为接下来的东西基本我就是做了个翻译。

*首先利用git命令把octopress克隆到主机上
```
git clone git://github.com/imathis/octopress.git octopress
cd octopress    # If you use RVM, You'll be asked if you trust the .rvmrc file (say yes).
```
+ 接着安装依赖项
```
gem install bundler
rbenv rehash    # If you use rbenv, rehash to be able to run the bundle command
bundle install
```
+ 最后安装默认主题
```
rake install
```
**注意我这里系统自带了ruby，如果没有这个的同学要自己安装，具体请参考[Installing Ruby With Rbenv][3]**

---

##4.配置Octopress
其实在这里Octopress的作者真的很贴心，我们需要改的东西真心不多。打开octopress文件夹下的`_config.yml`
+ 该文件中的`url`是必须要填写的，这里的`url`是在[github][1]上的页面地址，就是那个`username.github.io`
+ 另外再修改一下title、subtitle和author，就是你博客的名字，博客的副标题[^3]，还有你的名字啦
+ 觉得改这些还是不够，想多改改的同学参见[官网教程][4][^4]

---
##5.将博客上传到github上
我们需要利用`octopress`的一个配置`rake`任务来自动配置上面创建的仓库：可以让我们方便的部署GitHub page。在终端输入如下命令：
```
$ rake setup_github_pages
```
上面的命令会做一些事情。其中最主要的就是创建一个_deploy目录，目录用来存放部署到master分支的内容。期间会要求你输入仓库的url，根据提示，进行输入即可。
完成上面的命令之后，我们就可以生成博客并真正的部署到仓库中了。执行如下命令：
```
rake generate
rake deploy
```
上面的命令首先生成博客文件，并将生成的博客文件拷贝到_deploy/目录下，然后将这些内容添加到git中，并commit和push到仓库的master分支。
现在可以访问`http://username.github.com`了。
注意：有时候可能会有延时，要等几分钟才能打开。
至此，我们的博客已经完成基本的部署，不过博客的source需要单独提交，执行如下命令就可以将source提交到仓库的source分支下。
```
$git add .
$ git commit -m 'Initial source commit'
$ git push origin source
```
如果在部署到仓库之前，需要先预览一下博客，可以在终端输入`rake preview`命令，然后就能在浏览器中进行本地预览访问了`http://127.0.0.1:4000/`或`http://localhost:4000/`，效果跟上传到`github`中的一样。

---
##6.发表新博文
我们需要调用octopress给我们的命令
```
rake new_post["title"]
```
会让我们输入博客的题目什么的，生成的博文在`source/_post`下，以`日期-题目.markdown`命名，我们需要一个markdown编辑器即可，目前我用的是`retext`，推荐一个网页版[`cmdMarkdown`][5]
然后上传文件
```
$ rake generate
$ git add .
$ git commit -am "Some comment here." 
$ git push origin source
$ rake deploy
```
当然，在上传之前[^5]你依旧可以使用`rake preview`预览页面。

第二次写博客，依旧猴累猴累的，不过还是能坚持。
[1]:http://github.com/
[2]:http://octopress.org/
[3]:http://octopress.org/docs/setup/rbenv/
[4]:http://octopress.org/docs/configuring/
[5]:https://www.zybuluo.com/mdeditor
[^1]:我就是这么被坑了，一开始用户名是ghostQu，结果死活不生成页面。
[^2]:Google is your good friend.
[^3]:就是我博客名字下面那个Ever learning，never end啦。
[^4]:只不过是全英文的。
[^5]:即rake generate之后