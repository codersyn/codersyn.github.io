---
title: 利用Hexo和Github Pages搭建个人博客
date: 2022-01-16 17:20:00
tags:
---

1. ## 搭建教程
   
   + ### 准备工作
     
     + #### 新建一个Github仓库
       
       仓库命名格式为`你的用户名.github.io`，如我的github用户名为websyn，那仓库名就为`websyn.github.io`。
     
     + #### 安装[Node.js](https://nodejs.org/zh-cn/)
     
     + #### 安装[Git](https://git-scm.com/)
     
     + #### 配置SSH keys
       
       鼠标右键 -> **Git Bash Here**
       
       ```bash
       git config --global user.email "you@example.com"
       git config --global user.name "Your Name"
       ssh-keygen -t rsa -C "you@example.com"
       ```
       
       注意把`you@example.com`替换为你的邮箱，`Your Name`替换为你的用户名，然后一路回车，然后在**C:\Users\你的用户名\\.ssh**目录下找到`id_rsa.pub`文件，用记事本打开复制里面的内容。
       
       回到Github，依次点击**你的头像 -> Settings -> SSH and GPG keys -> SSH keys -> New SSH key**，Title随便写，Key的输入框里粘贴刚才复制的密钥，保存。
   
   + ### 安装Hexo
     
     + #### 安装
       
       ```bash
       npm install hexo-cli -g
       hexo init blog
       cd blog
       npm install
       npm install hexo-deployer-git --save
       ```
       
       *note: 因为天朝国情可能需要挂代理或换成国内镜像源，文末有*。
     
     + #### 配置`_config.yml`
       
       ```bash
       deploy:
         type: git
         repo: git@github.com:yourname/yourname.github.io.git
       branch: master
       ```
       
       repo后面的地址请复制你的github仓库地址替换，注意复制**SSH**地址，不要复制https地址。
     
     + #### 部署到Github
       
       ```bash
       hexo clean
       hexo g
       hexo d
       ```
       
       如果前面你的配置都正确的话，直接hexo三板斧就可以将你的博客上传到github上面了。
     
     + #### 开启**Github Pages**
       
       进入你新建的仓库，这时候仓库下应该有一堆你刚才推送上来的网页文件，依次点击上方的Settings -> Pages，进去后选择master分支保存就开启了Pages，点击上方的域名即可访问你的网站。
       
       如果你有自己的域名也可以在**Custom domain**下面的输入框里填上你的域名，记得在你的域名商处添加CNAME记录为`yourname.github.io`。

2. ## 使用教程
   
   + ### 新建文章
     
     `hexo new '文章名称'`，新建的文章为Markdown文件，在目录**blog\source\\_posts**下，打开即可编辑。
   
   + ### 发布文章
     
     `hexo clean`为清除缓存文件和已生成的静态文件，`hexo g`为生成静态文件，`hexo d`为部署静态文件到你的仓库。
     
     使用顺序不能错，可合并为`hexo clean & hexo g & hexo d`。
   
   + ### 更换主题
     
     hexo默认加载的主题是`landscape`，如果不喜欢可去[Themes | Hexo](https://hexo.io/themes/)挑选自己喜欢的主题。
     
     找到喜欢的主题下载下来后把主题解压放到目录**blog\themes**下，然后在`_config.yml`里找到`theme:`项更改后面的值为你下载的主题名字，然后hexo三板斧后就能看到你更换后的主题了。
     
     某些主题带有自己的config配置，请自行研究。

3. ## 备份设置
   
   + ### 新建备份分支并推送到github
     
     ```bash
     git init
     git remote add origin git@github.com:yourname/yourname.github.io.git
     git checkout -b blog
     git add .
     git commit -m '博客源文件'
     git push origin blog
     ```
     
     请注意把`git@github.com:yourname/yourname.github.io.git`替换为你自己的仓库SSH地址。
     
     以后每次有更新执行一下`git add .` -> `git commit -m '博客源文件'` -> `git push origin blog`就能把文件备份到github了。
   
   + ### 还原备份
     
     如果换了电脑操作，首先在新电脑上安装[Node.js](https://nodejs.org/zh-cn/)，安装[Git](https://git-scm.com/)，配置[SSH keys](https://sysy.su/2022/01/16/%E5%88%A9%E7%94%A8Hexo%E5%92%8CGithub-Pages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/#%E9%85%8D%E7%BD%AESSH-keys)，然后克隆github仓库：
     
     ```bash
     git clone -b blog git@github.com:yourname/yourname.github.io.git
     ```
     
     请注意把`git@github.com:yourname/yourname.github.io.git`替换为你自己的仓库SSH地址。
     
     然后会在本地出现一个`yourname.github.io`的文件夹，进去文件夹就可以看到你备份的hexo文件，接着在该文件夹里**Git Bash Here**或者在原git窗口输入`cd yourname.github.io.git`进入该文件夹，安装一下Hexo：
     
     ```bash
     npm install hexo-cli -g
     npm install
     npm install hexo-deployer-git --save
     ```
     
     注意并不需要`hexo init`这条命令，它的作用是初始化hexo，相当于把你的博客“恢复出厂设置”，所以不需要这步操作。
     
     之后你就可以正常的hexo三板斧操作你的博客了。

4. ## 可能会遇到的问题
   
   + 如果遇到安装不上hexo的情况可以考虑换成国内淘宝镜像：
     
     ```bash
     npm config set registry https://registry.npm.taobao.org
     ```
   
   + 文中出现的`blog`只是一个文件夹名，可替换为任意你喜欢的。
   
   + 文中出现的**Git Bash Here**默认都是在D盘操作，因为为了方便把博客文件放在自己指定的位置。
   
   + 想到再说。
