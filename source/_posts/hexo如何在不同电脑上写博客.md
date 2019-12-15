---
title: hexo如何在不同电脑上写博客
date: 2019-12-08 23:28:15
tags: [hexo]
categories: [hexo]
---

如果要更换电脑了发现电脑里没有原来的博客文件怎么办，这时候就可以利用git的分支保存我们博客的相关文件，每次更新都上传到分支去，更换电脑只需git clone下来即可。

<!--more-->

###  上传分支

- 先在github博客仓库中创建一个分支，在```settings```中设置```hexo```分支为默认分支，仓库克隆至本地目录

- 复制原来第一次写博客配置的那些文件（除了```.deploy_git```）。注意，需要把theme目录下的```.git```文件删除。

- 把配置相关内容上传博客

  ```shell
  git add . 
  git commit -m "add hexo branch"
  git push
  ```

  这一步如果出现错误``` warning: LF will be replaced by CRLF ```，可在该目录下添加文件```.gitattributes```文件，内容为：

  ```txt
  # Auto detect text files and perform LF normalization
  *        text=auto
  
  *.cs     text diff=csharp
  *.java   text diff=java
  *.html   text diff=html
  *.css    text
  *.js     text
  *.sql    text
  
  *.csproj text merge=union
  *.sln    text merge=union eol=crlf
  
  *.docx   diff=astextplain
  *.DOCX   diff=astextplain
  
  # absolute paths are ok, as are globs
  /**/postinst* text eol=lf
  
  # paths that don't start with / are treated relative to the .gitattributes folder
  relative/path/*.txt text eol=lf
  ```

  具体细节参考：[使用git最好的CRLF（回车，换行处理策略是什么？）]( ![img](file:///C:\Users\cm\AppData\Roaming\Tencent\QQ\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)https://stackoverflow.com/questions/170961/whats-the-best-crlf-carriage-return-line-feed-handling-strategy-with-git )，stackoverflow需翻墙，应该可以成功，如果不行尝试其他办法吧。

  

  

###  更换电脑后操作

- 安装原来需要的环境，创建github链接等等

  ```she
  npm install hexo-deployer-git
  ```

  这条命令执行完后目录中会生成node_modules和public文件夹，这是原来上传的时候忽略上传的内容

- 然后可以开始正常写博客了，```hexo n "XXX" ```

- 注意：

  如果更新了博客的配置，

  ```shell
git add . 
  git commit -m "modify xxx"
  git push
  ```
  
  
  
  更新博客配置到GitHub上以备份。
  
  
  
  