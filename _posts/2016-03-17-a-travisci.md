---
layout: post
title: 静态页面生成器装备持续集成 · 上
tag: tech
---

![intro]({{ site.baseurl }}/img/20160317/1.jpg)  

最近把博客全盘折腾成了 Jekyll 静态页面，部署到了 GitHub Pages 上面，并且利用 Travis CI 做了自动化构建和部署。这篇文章是一个简要的备忘记录。上部分主要写概念和工作流，下部分主要记录 Travis 对 Jekyll 的配置细节。  
<!--more-->

### **Jekyll**  

[Jekyll](https://jekyllrb.com/) 不用多讲，静态页面生成器。 其它类似的还有 [hexo](https://hexo.io/), [octopress](http://octopress.org/) 等等。  

### **持续集成**  

> 持续集成是一种软件开发实践。在持续集成中，团队成员频繁集成他们的工作成果，每人每天可能集成一次，甚至多次。每次集成会经过自动构建（包括自动测试）的检验，以尽快发现集成错误。许多团队发现这种方法可以显著减少集成引起的问题，并可以加快团队合作软件开发的速度。  

自动构建工具是持续集成的一种出色实践。实际上，代码提交后，由软件自动完成代码的测试、构建，并将过程中状态与构建物产出才是持续集成的意义。Travis CI 就是一个在线的、分布式的持续集成服务，用来构建及测试在 GitHub 托管的代码。这个软件的代码同时也是开源的，提供了多种编程语言的支持，包括 Ruby, JavaScript, Java, Scala, PHP 等。  

*原理：Travis CI 会在每一次提交之后生成一个虚拟机来执行事先安排好的 build 任务，你可以调整这个虚拟机的软件环境，甚至能执行 sudo apt-get install 自定义虚拟环境。*    

### **静态页面生成器 + 持续集成**  

我们在每次修改添加新文章后，静态页面生成器都需要重新构建、发布，这样的机械化工作交给 Travis CI 再合适不过了。另外，利用 Travis CI 还可以带来很多其他的方便。  

比如，为 **多人协作** 带来的方便。一般情况下，我们需要本地产生静态档案然后 push 到 GitHub 上。如此这般，对于普通博客或许还算轻松，但是对于偶尔会有的 pull request 就比较麻烦。试想一下，在合并了 pull request 时候，我们必须把最近的 commit 在拉到本地进行手动 build, 之后还需再 push 进行部署。 这样一来，总会有很多麻烦，往往 pull request 合并之后，网页却还未及时更新。  

再比如，为 **测试** 带来方便。最简单的测试代码是运行 ``jekyll build`` 来确保 Jekyll 对站点的构建不会出错。它并不检查站点的输出结果，而只确保构建正确地进行。当需要测试 Jekyll 的输出结果时，html-proofer 是最佳的工具选择。这个工具会检查输出站点中所有的链接和图片的有效性。可以很方便地使用命令行 ``htmlproof`` 执行该工具，或者写一段 Ruby 代码来执行该 gem。点击 [html-proofer](https://github.com/gjtorikian/html-proofer) 查看更多细节。利用持续集成，我们可以将此测试部分放到 Travis CI 去，一旦测试通过，网页便可完成自动部署。  

### **GitHub Pages**  

GitHub Pages 为我们提供了便捷的网页 host 服务，此服务直接来自 GitHub 的代码库。因此我们可以直接编辑代码库然后 push 之后网页就可以及时更新了。    

GitHub Pages 的 Slogan 叫做是：

> Websites for you and your projects.    

所以 GitHub Pages 支持两种设置：**User site** 和 **Project site**。    

**异同**    

以上两种网页设置都是直接由 GitHub 代码库生成，唯一的区别是 User site 的 publishing branch 是 **master**，而 Project site 的 publishing branch 是 **gh-pages**。而且每个 GitHub 代码库只支持唯一一个 User site， 但是可以支持多个 Project site。  

**域名**    

GitHub Pages 的域名设置简单明了。User site 拥有一个唯一的域名 `username.github.io`，而每个 Project site 则是对应的 `username.github.io/projectname`。  

**CNAME**  

GitHub Pages 当然支持自定义域名，只需要在代码库中添加 *CNAME* 文件即可。  

举个例子，用户 [muan](https://github.com/muan/) 拥有一个名为 [muan.github.com](https://github.com/muan/muan.github.com) 的 User Pages 代码库，现在我们就可以通过 `muan.github.io` 来访问。接着 muan 为这个代码库添加了一个 *CNMAE* 文件，内容为 `muan.co`，这意味着我们现在可以通过 `muan.co` 这个 url 来访问代码库。    

除此之外，muan 还拥有一个叫做 [emoji](https://github.com/muan/emoji/) 的 Project Pages 代码库。现在这个代码库可以通过 `muan.github.io/emoji` 或者 `muan.co/emoji` 来访问。之后 muan 为这个代码库添加了一个 *CNMAE* 文件，内容为 `emoji.muan.co`，现在我们则可以通过 `emoji.muan.co` 来访问这个网页了。    

更多关于 [GitHub Pages](https://pages.github.com/), [CNAME 设置](https://help.github.com/articles/setting-up-your-pages-site-repository/) 和 [在 DNS 供应商处设置自定义域名](https://help.github.com/articles/quick-start-setting-up-a-custom-domain/) 的内容。  
