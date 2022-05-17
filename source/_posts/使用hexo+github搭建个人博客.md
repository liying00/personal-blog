---
title: 使用hexo+github搭建个人博客
---

time: 2020-01-06

1.首先安装 git node npm等必备
2.安装hexo框架
<pre>cnpm-install -g hexo-cli</pre>
输入hexo-v验证一下是否安装成功
<pre>hexo init blog</pre>
<pre>npm install</pre>
<pre>hexo server</pre>
    快速搭建方法：
	<pre>npm install hexo-deployer-git --save
	hexo clean && hexo g && hexo d</pre>
	
3.在github上创建项目
4.在文件夹下执行
<pre>git init
git add.
git commit -m "init blog"
git remote add origin https://github.com/...
git push -u origin master</pre>

5.在github setting中配置所购买好等域名



last：最后挑选自己配置好的主题 在hexo官网 我这里用的是一个叫做oishi的主题。
      选到合适的主题以后需要将主题clone到项目下面的theme文件夹下面。



