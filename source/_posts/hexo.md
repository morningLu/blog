---
title: hexo
date: 2018-12-24 20:29:41
tags:
categories:
    - 技术(未分类)
---
### 1、hexo环境搭建
0. 参考网站 [Hexo+GitHub搭建博客CSDN](https://blog.csdn.net/qq_27754983/article/details/76143478)
1. 创建git仓库 morningLu.github.io
2. 安装文件
  1. [node.js下载](https://nodejs.org/en/download/)
  2. [git下载](https://git-for-windows.github.io/)
  3. hexo
    1. [hexo介绍](https://hexo.io/zh-cn/docs/index.html)
    2. git-bash 安装命令: npm install hexo-cli -g
    3. git-bash 安装命令: npm install hexo-deployer-git --save
    4. git-bash git安装命令: npm install --save hexo-deployer-git
3. git 可以上传代码
4. 生成项目文件
  1. 在一个空的文件夹下执行git-bash命令 hexo init
  2. hexo new xxxx (生成md文件，xxxx为md文件名字)
  3. hexo s (本地预览 [local](http://127.0.0.1:4000/))
  3. hexo g (生成静态文件)
  4. hexo d (public文件夹上传到git，ps: 上传到git前要hexo g, 本地预览不需要)
5. hexo和github进行关联
  更改_config.ym文件
  ```
  deploy:
    type: git
    repository: git@github.com:morningLu/morningLu.github.io.git (ssh https)
    branch: master (分支)
  ```
### 2、hexo 生成博文插入本地图片
0. 参考网站 [csdn原文](https://blog.csdn.net/hqweay/article/details/80847495)
1. 把主页配置文件_config.yml 里的post_asset_folder:这个选项设置为true
2. git-bash 可以上传本地图片的插件安装: npm install hexo-asset-image --save
3. hexo n "xxxx" (xxxx为对应md文件名字)
4. ![图片描述]<font face="黑体">(</font>xxxx/图片名.jpg<font face="黑体">)</font>

### 3、hexo 创建分类
1. hexo new page xxxx (xxxx为分类名)
2. 在文章的最上面添加 categories:
```
title: 摄影基础课
date: 2018-12-25 17:10:26
tags:
categories:
    - 摄影
```
