---
title: 同一台电脑上配置两个 GitHub 账号
categories: 配置
tags:
 - 配置
 - git
 - mac
---

## 实现原理
要在同一台电脑上配置两个 GitHub 账号，（GitLab，码云同理）,需要生成各自配套的 SSH Key。这样，在 clone 和 push 代码的时候，就能区分用的是哪个账号。

## 取消全局设定
如果之前在 git config 中全局设置过用户名和邮箱，则需要先取消设置。这个用户名和邮箱是记录每一次 commit 的用户和关联的邮箱。公司和个人用的是不同的邮箱，所以应该分别设置。

```
git config --global --unset user.name

git config --global --unset user.email
```

## 生成 SSH Key

```
$ ssh-keygen -t rsa -C "youremail@xxx.com"
```

按一次回车后：

```
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/Shinancao/.ssh/id_rsa): id_rsa_company(取个名字)
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in 
id_rsa_company
Your public key has been saved in 
id_rsa_company.pub.
```

这里我首先设置的是公司的 SSH Key，使用的是公司邮箱。记得要给 SSH Key 取一个名字，不然会使用默认的名字，当后面再设置个人的 SSH Key 时，如果还使用的默认的名字，则会把前面的覆盖掉。好了，接着按上面的方式创建个人的 SSH Key，这次我取的名字是 id_rsa_personal。这样，就生成了两套 SSH Key。如果生成的 Key 不在 .ssh 文件夹中，可以拖进去。

## 在 GitHub 上添加 Key
为了避免新手读者不知道怎么添加，在这里我还是提一下吧。在本地通过 cat ~/.ssh/ssh_key_name.pub 获取公钥的内容，复制，然后登录 GitHub 账号，点击 Settings->SSH and GPG keys>New SSH Key 进行添加。

## 在本地 SSH Agent 中添加 Key
只有在本地的 SSH Agent 中添加了 Key，这样 SSH 服务器才能找到 id_rsa。
```
ssh-add -K ~/.ssh/id_rsa_personal

ssh-add -K ~/.ssh/id_rsa_company
```
## 修改 SSH config 文件
config 文件不是默认生产的，有些人可能会没有，所以可以先创建一个。先进入到 .ssh 文件中cd ~/.ssh，然后执行 touch config进行创建。

打开刚才创建好的 config 文件，然后在里面添加如下内容：
```
# personal
Host personal.github.com
HostName github.com
User git
IdentityFile ~/.ssh/id_rsa

# company
Host company.github.com  # 这个名字可任意设置
HostName github.com
User git
IdentityFile ~/.ssh/id_rsa_work
```

OK，现在让我们测试一下配置是否成功：
```
ssh -T git@personal.github.com

ssh -T git@company.github.com
```
如果输出以下内容：
```
Hi xxx! You've successfully authenticated
```
恭喜你，已经配置成功了！慢着慢着，刚才把全局设置给取消了，现在是不是要单独设置回来，没错，取消了全局设置的用户和邮箱后，我们要为每一个项目进行单独设置。我先给大家讲一下现在该如何对项目进行设置。

## 最后一步
由于我们设置了 Host，这就相当于是 HostName 的别名。比如现在对个人 GitHub 上的 repo 进行 clone 的时候，要改成 git clone git@personal.github.com:xxx/xxx.git。如果是已经克隆到本地的项目，则可以在项目中修改：
```
$ git remote rm origin

$ git remote add origin git@personal.github.com:xxx/xxx.git
```
可以执行 git remote -v 查看是否修改成功。

然后在每个项目中，执行：
```
git config user.email "xxx@xxx.com"

git config user.name "xxname"
```
对公司和个人项目单独设置用户名和邮箱。
可以查看一下：
```
git config user.name

git config user.email
```
push 的话还是照常使用：git push origin master

···转自Acan博客[https://github.com/hzsss/Mac-OS下配置多个GitHub账号/]