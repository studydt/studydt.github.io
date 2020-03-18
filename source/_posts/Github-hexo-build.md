---
title: Github_hexo_build
date: 2020-03-18 13:52:19
tags:
---
# Github Hexo 搭建博客
[TOC]

## 安装Node.js

下载[Node.js](https://nodejs.org/en/)，下载后安装时一路默认next(安装路径可以自选)
最后安装好之后，按 `Win+R` 打开命令提示符，输入 `node -v` 和 `npm -v` ，如果出现版本号，那么就安装成功了。

### 添加国内镜像源

如果没有梯子的话，可以使用阿里的国内镜像进行加速。
```
npm config set registry https://registry.npm.taobao.org
```

## 安装Git
为了把本地的网页文件上传到github，我们需要用到分布式版本控制工具——[Git](https://git-scm.com/download/win)

安装选项还是全部默认，只不过最后一步添加路径时选择`Use Git from the Windows Command Prompt`，这样我们就可以直接在命令提示符里打开git了。

安装完成后在命令提示符中输入`git --version`验证是否安装成功。

## GitHub账号绑定ssh私钥
### 注册
在[github官网](https://github.com/) 申请注册帐号，注册后要记住：你的用户名(username)，你的密码(password)，以及你的邮箱(email)
### 新建仓库
新建一个仓库（start a project  / new repository），命名 (Repository name) 是你的github的用户名(username)加上.github.io，并且需要勾选Initialize this repository with a README，然后点击create repository
建立仓库后，点击右边中间的(Cloud or download)再点击Use SSH，看到你的git@github.com:username/username.github.io.git 然后复制
### 绑定ssh
你可能会需要配置git的ssh(密钥)
步骤：
首先右键打开git bash，然后输入下面命令：
```
git config --global user.name "你的用户名"
git config --global user.email "你的邮箱"
```
用户名和邮箱根据你注册github的信息自行修改。然后生成密钥SSH 
```
key：ssh-keygen -t rsa -C "你的邮箱"
```
**注意检查是否是私密邮箱**
打开github，在头像下面点击`settings`，再点击`SSH and GPG keys`，新建一个SSH，名字随便。
git bash中输入
```
cat ~/.ssh/id_rsa.pub
```
将输出的内容复制到框中，点击确定保存。
输入`ssh -T git@github.com`，出现你的用户名，那就成功了。

>**问题**：假如ssh-key配置失败，那么只要以下步骤就能完全解决
首先，清除所有的key-pair
ssh-add -D
rm -r ~/.ssh
删除你在github中的public-key
重新生成ssh密钥对
ssh-keygen -t rsa -C "xxx@xxx.com"
接下来正常操作
在github上添加公钥public-key:
1、首先在你的终端运行 xclip -sel c ~/.ssh/id_rsa.pub将公钥内容复制到剪切板
2、在github上添加公钥时，直接复制即可
3、保存

## 本地初始化一个Hexo项目

新建一个空目录，作为你的博客目录。进入该目录，右击`Git bash here`，初始化一个Hexo项目：
```
hexo init
npm install
npm install hexo-deployer-git --save
```

然后用自己原来博客里的文件替换掉这里的`source\ `, `scaffolds\` ,   `themes\`,` _config.yml ` 替换成自己原来博客里的。注意，这里把`themes/jacman`中的`.git/`目录删除，否则下面推送到hexo分支后jacman为空。这也是众多坑中的一个，由于水平有限具体原因不是很清楚，只能瞎猫碰上死耗子啦。
### 配置hexo deploy参数
修改 根目录下的`_config.yml `文件
```yml
deploy:
    type: git  
    repo: https://github.com/你的用户名/你的用户名.github.io.git 
    branch: master
```
**【在_config.yml文档修改所有信息都要注意，冒号后面一定要有一个空格！】**


## 将整个目录推送到master
要推送到master分支，首先要将该目录初始化为本地Git仓库：
```
git init

//把博客目录下所有文件推送到master分支
git remote add origin git@github:你的用户名/你的用户名.github.io.git
git add .
git commit -m "first add hexo source code"
git push origin master
```

## 在github上新建分支hexo
新建一个分支hexo(名字可以自定义)，这时候hexo分支和master分支的内容一样，都是hexo的源文件。

并把hexo设为默认分支，这样的话在另外一台机器上克隆下来就直接进入hexo分支，并且以后所有操作都是在hexo分支下完成。

为什么需要这个额外的分支呢？

因为`hexo d`只把静态网页文件部署到master分支上，所以你换了另外一台电脑，就无法pull下来继续写博客了。有了hexo分支的话，就可以把hexo分支中的源文件(配置文件、主题样式等)pull下来，再`hexo g`的话就可以生成一模一样的静态文件了。



## 部署博客
然后使用部署命令
```
hexo g -d
```
就会自动渲染Markdown文件生成静态文件并部署到yourname.github.io仓库的master分支上，这时再访问http://yourname.github.io 就可以看到博客页面了。

此时博客页面是部署保存了，但hexo配置、md、主题等文件还没有保存，在heox目录下使用以下命令将文件推送到hexo分支上
```
git remote add origin https://github.com/yourname/yourname.github.io.git
git add .
git commit -m "提交描述"
git push origin hexo
```
这时候到github查看两个分支的内容，hexo分支里是源文件，master里是静态文件。
这样才能在另外的机器上pull下来，保持同步。

## 不同环境操作
### 已有环境
如果在电脑上已经写过博客，那么可以在已有的工作目录下同步之前写的博客。
在你的仓库目录下右键`git bash shell`，起来bash命令行，然后
```
git pull
```
这样你的状态就更新了，之后就是 hexo命令写文章啦。。。
写完`hexo g -d`部署好后，使用
```
git add .
git commit -m “change description”
git push origin hexo
```
推送上去.

### 新环境

到了新的电脑上时，我们需要将项目先下载到本地，然后再进行hexo初始化。
```
git clone https://github.com/yourname/yourname.github.io.git
```
但是由于.gitignore文件中过滤了`node_modules\ `，所以克隆下来的目录里没有`node_modules\ ` ，这是hexo所需要的组件，所以要在该目录中重新安装hexo，但不需要hexo init。
```
cd yourname.github.io
npm install hexo
npm install
npm install hexo-deployer-git –save
```
之后开始写博客，写好部署好之后，别忘记 git add , ….git push origin hexo…推上去

## 日常操作

### 写博客前
不管你本地的仓库是否是最新的，都先pull一下，以防万一：
```
git pull origin hexo
```
把最新的pull下来，再开始撰写新的博客。
### 写博客
```
hexo new "title"
```
然后打开`source/_posts/title.md`，撰写博文。
### 提交博客
先推送到hexo分支上：
```
git add .
git commit -m "add article xxx"
git push origin hexo
```
最后部署到master分支上：
```
hexo g -d
```
## 常见问题
### hexo命令不能执行，返回报usage
>尝试了所有的命令hexo clean
生成静态页面
hexo g
本地服务器预览
hexo s
发布到git
hexo d
生成新文章#在`source/_post`文件夹里面,编辑生成的markdown文件
hexo new 'post-name'

**都返回同样的结果**
```
➜ ~ cd blog
➜ blog hexo g
Usage: hexo

Commands:
help Get help on a command.
init Create a new Hexo folder.
version Display version information.

Global Options:
--config Specify config file instead of using _config.yml
--cwd Specify the CWD
--debug Display all verbose messages in the terminal
--draft Display draft posts
--safe Disable all plugins and scripts
--silent Hide output on console

For more help, you can use 'hexo help [command]' for the detailed information
or you can check the docs: http://hexo.io/docs/
```
#### 解决方案：
```
你在~根目录运行 hexo g... 当然没反应... 相当于你走在大街上,大吼一声: 老婆,饭煮好了没? 你老婆在家是听不见的...
淘宝... 好歹是解决了... 用钱解决也是诚意... 但每天都用的东西最好还是自己了解一下

    网上搜了好多也没有找到合适的方案。

    详细一些就是自怼搜索姿势的机会呐
```
