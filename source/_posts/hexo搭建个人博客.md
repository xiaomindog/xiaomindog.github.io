---
title: hexo搭建个人博客
date: 2019-12-06 00:28:43
tags: [hexo,blog]
categories: hexo
---

搭建简易的hexo博客过程，一些自己常用设置和相关操作

<!--more-->

###  安装环境 

- git
- nodejs
- hexo
- 创建github同名的仓库

###  初始化

- `hexo init ` hexo自动下载一些文件到目录

- `hexo g` 生成静态文件  hexo就会在public文件夹生成相关html文件，这些文件将来都是要提交到github上

- `hexo s` 本地预览 打开浏览器访问 [http://localhost:4000](http://localhost:4000/) 即可看到内容，`Ctrl+C`停止本地预览

- 设置主题 打开_config.yml 找到theme字段： 添加主题next

- 配置站点配置文件_config.yml deploy的部分. 安装部署插件  `npm install hexo-deployer-git `  `hexo d` 部署到github上 最后打开即可显示内容

###  主题相关配置 

- 主题风格 ：编辑schemes字段。 更新主题风格 `hexo clean,hexo g, hexo d`

- 设置Menu：编辑menu字段。

- 修改文章内链接文本样式：themes/next/source/css/_common/components/post/post.styl，在末尾添加CSS样式

  ```css
  // 文章内链接文本样式
  .post-body p a{
    color: #0593d3; //原始链接颜色
    border-bottom: none;
    border-bottom: 1px solid #0593d3; //底部分割线颜色
    &:hover {
      color: #fc6423; //鼠标经过颜色
      border-bottom: none;
      border-bottom: 1px solid #fc6423; //底部分割线颜色
    }
  }
  ```

- 设置头像：编辑url字段 添加图片链接

- 社交栏：编辑social字段 `[社交平台名]: [社交地址] || [图标名称]`

- 设置首页不显示全文(只显示预览)：markdown文本中添加`<!--more-->`

- 分类和标签设置 `hexo new page "name"`  可在yourBlog\source\“name”\index.md继续编辑内容

  scaffolds/post.md文件下可设置新建文件的模板
  
- 搜索功能：待添加

- 修改分类标签样式：`/themes/next/layout/_macro/post.swig`，搜索 `rel="tag">#`，将 # 换成`<i class="fa fa-tag"></i>`

- 菜单栏图标设置：[icomoon](https://icomoon.io/#preview-essential)找到想要的图标即可

- 

###  相关操作

