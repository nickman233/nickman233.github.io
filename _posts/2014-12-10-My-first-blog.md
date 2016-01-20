---
layout: default_post
title: My first blog!
category: git
---

这是我的第一篇博客。jekyll使用比较简单，只要把要发的博文用makedown或texttitle写好，放到`_posts`文件夹内即可。
下面放张图片试试：  

[jekyll-now]: https://github.com/barryclark/jekyll-now

![pic](http://7sbplh.com1.z0.glb.clouddn.com/pic.jpg)

#git的一些命令   

##linux创建SSH密钥  
```bash
$ ssh-keygen -t rsa -C "your_email@youremail.com"
```

后面的 your_email@youremail.com 改为你的邮箱，之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。成功的话会在~/下生成.ssh文件夹，进去，打开id_rsa.pub，复制里面的key。  

回到github，进入Account Settings，左边选择SSH Keys，Add SSH Key,title随便填，粘贴key。
为了验证是否成功，输入：  

```
$ ssh -T git@github.com
```

如果是第一次的会提示是否continue，输入yes就会看到：You've successfully authenticated, but GitHub does not provide shell access 。这就表示已成功连上github。  

##github 设置   

```
$ git config --global user.name "your name"
$ git config --global user.email "your_email@youremail.com"
```

##clone  
```
$ git clone https://github.com/nickman233/nickman233.github.io
```

##push  
```
$ git add images/pic.jpg
$ git commit -m "pic"
$ git remote add origin https://github.com/nickman233/nickman233.github.io
$ git push -u origin master
```

##创建项目并上传  
```
$ mkdir testdir & cd testdir  
$ touch README.md  
$ git init #初始化一个本地库  
$ git add README.md #添加文件到本地仓库  
$ git rm README.md  #本地仓库内删除  
$ git commit -m "commit" #提交到本地仓库并备注，此时更改在本地  
$ git remote add origin https://github.com/nickman233/nickman233.github.io #增加一个远程服务器的别名  
$ git remote rm origin #删除远程版本库的别名  
$ git push -u origin master #将本地文件提交到github  
```
