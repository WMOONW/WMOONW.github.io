---
layout: post
title: Eclipse与GitHub整合
date: 2018-01-27
categories: blog
tags: [eclipse, github]
description: 在eclipse中将项目提交到GitHub。

---

# Eclipse与GitHub整合

1. 在GitHub创建一个`Repository`.

1. 生成ssh key

    如果你的ssh2已经有了需要使用的id key,请先备份,然后将目录清空.
    点击`Window`->`Preferences`->`General`->`Network`->`SSH2`,点击`Key Management tab`页,点击`Generate RSA Key`,然后点击`Save Private key`,将`key`保存自定义目录.
    点击`Export Via SFTP`,在弹出窗口填入`git@github.com`,此时你的`ssh`目录会多出一个`known_hosts`文件,此文件与`id_rsa.pub`一样重要.
    将生成的`id_rsa.pub`打开,删除空行复制里面的内容,然后粘贴到`github`的`ssh keys`中.
  重启eclipse,查看`ssh`选项卡中是否能load出`RSA Key`与`known hosts`,如果不能检查以上步骤,否则你是连不上`github`的.
  
1. PUSH配置

    创建一个应用,然后在应用上`右键`->`Team`->`Share Project`,选择`git`,点击`next`,点击`use or create repository in parent folder`,不用理会上面的警告,直接finish.
    在应用根目录下创建一个README,随便写入内容,然后`右键`->`team`->`commit`,但此时文件仍然在你本地,并没有push到远程服务器上.
    接着`右键`->`team`->`remote`->`push`,此处填写你的项目地址,协议,填写完后点击`next`,如果出现`ssh://git@github.com:22 The authenticity of host “github.com” can’t be established. RSA key`的错误信息请重启eclipse,重启完毕后继续此步骤.
    如果没有异常,在弹出窗口直接点击`add all branches spec`按钮,最后点击`finish`,整个过程完毕,点击github你的主页就能看到你的代码.
    之后在Eclipse中修改好项目代码之后，commit之后直接在项目上右击`Team`->`push`就可以进行代码提交。

1. Fetch

    上传到github上之后，团队中另外的成员可以通过`Eclipse`->`File`->`Import`->`Project from Git`->`URI`来提取工程。
    在团队开发中，通常在新的一天的开始工作之前，把最新的代码fecth下来。直接在项目`右击`->`Team`->`fetch`,再`执行`->`Team`->`Merge`,服务器上的新文件就会添加进来了。
