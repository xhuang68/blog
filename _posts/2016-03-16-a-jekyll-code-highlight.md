---
layout: post
title: Jekyll 代码高亮
tag: tech
---

在使用 Jekyll 撰写博客的时候，可以通过 **Pygments** 和 **Rouge** 来对博文中的代码进行高亮显示。  
<!--more-->

---

## **Pygments**  

Jekyll 原生支持 [Pygments](http://pygments.org). Pygments 支持超过100种语言，并且支持多种输出格式，比如 HTML, RTF等等。  

### **修改 _config.yml**  
设置 ``highlighter: pygments``  

### **本地安装 Pygments**    

* Pygments 是基于 Python 的，所以机器上需要安装 Python。
* [下载](https://pypi.python.org/pypi/Pygments)最新的 Pygments, 下载完成后解压。
* 在解压后的 Pygments 目录中，运行命令：``sudo python setup.py install``.  

### **选择一种喜欢的代码高亮样式**   

Pygments 提供了多种样式，比如 *native*, *emacs*, *vs* 等等，可以在 [Pygments Demo](http://pygments.org/demo) 中选择某种语言的例子，体验不同的样式。  
通过下面的命令可以查看当前支持的样式：  
{% highlight Python %}
from pygments.styles import STYLE_MAP  
STYLE_MAP.keys()  
{% endhighlight %}  

### **选择一种样式，应用在 Jekyll 中**  

* ``cd <your repository path>/css``
* ``pygmentize -S native -f html > pygments.css`` “native”是样式名，“html”是formatter.
* 在 layout 中引用刚刚加的 pygments.css  

### **在文章中高亮代码**    

现在，可以在博客中高亮代码了：  
{% highlight text %}
{% raw %}
{% highlight java %}
public class HelloWorld {
    public static void main(String args[]) {  
    System.out.println("Hello World!");  
    }
}
{% endhighlight %}
{% endraw %}
{% endhighlight %}

效果如下：   
{% highlight java %}
public class HelloWorld {
    public static void main(String args[]) {  
    System.out.println("Hello World!");  
    }
}
{% endhighlight %}  

---

## **Rouge**  

Jekyll 原生支持 [Rouge](http://rouge.jneen.net). Rouge 支持超过60种语言。  

### **Jekyll 3 默认**    

Rouge 是 Jekyll 3 默认的代码高亮工具，对于 Jekyll 2， 需要在配置文件中做如下设置：``highlighter: rouge``.  

### **在文章中高亮代码**    

现在，可以在博客中高亮代码了：  
{% highlight text %}
{% raw %}
{% highlight ruby %}
def foo
  puts 'foo'
end
{% endhighlight %}
{% endraw %}
{% endhighlight %}

效果如下：   
{% highlight ruby %}
def foo
  puts 'foo'
end
{% endhighlight %}  

### **行号**    

highlight 中的第二个参数 linenos 为可选参数，此参数可以为代码添加行号：  
{% highlight text %}
{% raw %}
{% highlight ruby linenos %}
def foo
  puts 'foo'
end
{% endhighlight %}
{% endraw %}
{% endhighlight %}  

效果如下：  
{% highlight ruby linenos %}
def foo
  puts 'foo'
end
{% endhighlight %}
