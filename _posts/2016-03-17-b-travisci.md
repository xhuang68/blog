---
layout: post
title: 静态页面生成器装备持续集成 · 中
tag: tech
---

### **工作流**    

有了对 Jekyll, Travis CI 和 GitHub Pages 的了解，我们对于自动构建 Jekyll 工作流的构思是：  
本地向 Github 上 push 代码后，如果该代码属于目标源（暂时称为 Dev repo），Travis CI 就自动构建 Jekyll 环境编译它，并将产出的静态博客 push 回我们的 Github pages 源（暂时称为 Pages repo）。然后即可在 pages 上查看新发布的博客。 如下图：  

![intro]({{ site.baseurl }}/img/20160317/2.jpg)   

<!--more-->

**分解工作流**  

1. *User -> push -> Dev repo*  
事先在 Github 上建立好 repository 即可。有两种方案可供参考：
* 建立 username.github.io 源，并开设 dev 分支，将此分支作为 Dev repo. 而 master/gh-pages 分支就是 Pages repo. 这样的好处是只用维护一个 repository.  
* 另一种方案，建立两个 repositories, 一个作为 Dev repo, 另一个 username.github.io 源就作 Pages repo. 此方案的好处是可以开设多个项目 pages.
我采用的是前一种方案。其实两种方案实现代码基本相同，使用第二种方案时注意最后配置正确 Pages repo 的 push 路径和权限即可。  

2. *Dev repo -> sync -> Travis CI*
在 Travis CI 中开启 Dev repo 的同步开关，然后在 Dev repo 中添加 .travis.yml 文件。这样 Travis CI 就能自动同步之后 push 的代码了。另外记得在 Travis CI 的同步设置中启用 `Build only if .travis.yml is present` 项，这样能在 repository 中有多个 branch 时，让 Travis CI 只构建放置了.travis.yml 文件的 branch.  

3. *Travis CI -> build and push -> Pages repo*  
这里再分解为 build 和 push 两步：
* *build*  
Travis CI 的自动化构建完全依靠唯一的 .travis.yml 脚本文件。需要在此文件中添加构建环境、构建 Jekyll、生成博客及后续 push 到 Pages repo 的全部脚本。有些类似于 Docker 中的 dockerfile。  
* *push*  
这一步比较麻烦，涉及到权限设置，加密，下部分会仔细介绍。  

4. *User -> View the pages*  
