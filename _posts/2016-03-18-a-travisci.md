---
layout: post
title: 静态页面生成器装备持续集成 · 下
tag: tech
---

之前的两篇文章已经理顺了思路，实现起来也就容易不少。每个步骤网上都能查阅到相当多的资料，这里简单记录一下我的过程。  

<!--more-->

### **准备 Dev repo 和 Pages repo**  

这一步实际上就是在 GitHub 上建立起对应的代码库，我这里选择的是在统一代码库下建立 dev 和 gh-pages 两个 branch，回忆一下，如果是 User site 负责部署的 branch 应该是 master。  

### **准备 Jekyll**   

在 dev 分支上安装 Jekyll 或者其他框架所需要的基本文件，包括主题、日志等，然后提交。这里有 [Jekyll 安装文档](https://jekyllrb.com/docs/home/) 和 [Hexo 安装文档](https://hexo.io/docs/)。  

### **准备 Travis CI**  

在 Travis CI 官网进行登录，选择自己需要的 GitHub 代码库进行同步，并且打开此代码库的 `Build only if .travis.yml is present` 设置选项。此时准备工作基本就绪，只需要在 dev 分支上进行 .travis.yml 文件的配置然后 commit 和 push，即可开启整个工作流。  

### **配置 .travis.yml 文件**  

Travis 的配置文件主要有三个内容：告诉 Travis CI 的虚拟机建立什么样的语言环境；告诉此虚拟机安装哪些依赖来构建合理的编译环境；告诉虚拟机执行哪些脚本来对框架进行编译构建以及部署。其中还涉及到一个非常重要的问题，即是赋予虚拟机对于 GitHub 代码库进行有限操作的权限。   

**权限**  

我们当然不会把 GitHub 的账号密码明文地放到 .travis.yml 文档里秀给大家看，所以给 Travis CI 授权一般有两种方式：  

1. 通过 SSH key 配置加密私钥，再通过配置 push 时的 SSH 来授权 Travis 对 GitHub 的访问。这个方法的具体实现可以参见这篇文章：[用 Travis CI 自動部署網站到 GitHub](https://zespia.tw/blog/2015/01/21/continuous-deployment-to-github-with-travis/), 这篇文章同样也详细介绍了如何为 Hexo 配置 .travis.yml 文件。    
2. 通过 GitHub 的 [Personal Access Token](https://github.com/settings/tokens) 来对 Travis CI 进行授权。我采用的就是这种方式。  

**.travis.yml**  

我配置 Travis 的具体步骤如下：

* 在 GitHub 新建一个 Personal Access Token, 命名可以类似 `Travis deployment for repo <YOUR_REPO>`.  
* 在本地安装 Travis: `gem install travis`  
* 配置 Genfile，告知 Travis 虚拟机安装指定程序及依赖。我的 Gemfile 内容如下，之后虚拟机会自动从源下载并安装文件中的程序及依赖：  
{% highlight shell %}
source "https://rubygems.org"

gem "jekyll"
gem "jekyll-paginate"
{% endhighlight %}
* 为你的代码库新建 .travis.yml 文件，我的文件内容如下：  
{% highlight shell %}
language: ruby
rvm:
- 2.2

bundler_args: ''

branches:
  only:
  - dev

before_install:
- git config --global user.name "Travis CI"
- git config --global user.email ${EMAIL}

script:
- bundle exec jekyll build

after_success:
- git clone https://$GITHUB_REPO
- cd $(basename ${GITHUB_REPO%.git})
- rsync -az --delete --exclude '.git*' ../_site/ .
- touch .nojekyll
- git add -A .
- git commit -m "Generated Jekyll site by Travis CI - ${TRAVIS_BUILD_NUMBER}"
- git push --force "https://${DEPLOY_KEY}@${GITHUB_REPO}" HEAD:${REPO_TARGET_BRANCH}

env:
  global:
  - NOKOGIRI_USE_SYSTEM_LIBRARIES=true
  - GITHUB_REPO: github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
  - REPO_TARGET_BRANCH: master
  - GH_REF: github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
{% endhighlight %}
这个文件当中， `language` 和 `rvm` 设置了 Travis 虚拟机的语言配置环境及版本，`branches` 说明了 Travis 仅能操作 dev 分支。接下来三部分的脚本分别是安装制定程序前对 Travis git 的配置，安装后执行的测试脚本，测试脚本成功后的操作指令（发布到网页的指定分支）。

* 利用 Travis 的 secret 属性来加密你的 Email 和新建的 Token，并且添加到 .travis.yml 文件中:    
{% highlight shell %}
# fill with Github verified email address
$ travis encrypt EMAIL=me@domain.com --add  

# fill with your personal access token
$ travis encrypt DEPLOY_KEY=token --add  
{% endhighlight %}
* 把上面文档中的 `GITHUB_REPO` 和 `REPO_TARGET_BRANCH` 更换成你自己的代码库和需要更新的 branch.  
* Commit and push. Travis 便会自动发现 commit, 编译项目，在 push 回指定分支，完成部署。大功告成！  

### **参考**  

* [Jekyll + Github pages + Travis CI, sitting in a tree](http://nick-dunn.co.uk/blog/jekyll-github-travis-ci-sitting-in-a-tree/)  
* [Auto-deploying built products to gh-pages with Travis](https://gist.github.com/domenic/ec8b0fc8ab45f39403dd)   
