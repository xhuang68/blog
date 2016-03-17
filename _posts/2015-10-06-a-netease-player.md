---
layout: post
title: 在 markdown 中使用网易云音乐外链播放器
tag: Tech · 技术
---

### 概述  
网易云音乐有两种外链播放器插件。   

一种是 iframe，像这样：   
{% highlight html %}
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="http://music.163.com/outchain/player?type=2&id=29734857&auto=1&height=66"></iframe>   
{% endhighlight html %}   

另一种是 flash 插件，像这样：  
{% highlight html %}
<embed src="http://music.163.com/style/swf/widget.swf?sid=29734857&type=2&auto=1&width=320&height=66" width="340" height="86"  allowNetworking="all"></embed>
{% endhighlight html %}   

<!--more-->
### 使用   
如今很多平台都不支持 flash 了，所以我使用的是 iframe。

直接使用 iframe 是会直接显示出代码，而不会显示播放器的，需要把 iframe 的`width`和`height`标签的值加上双引号，另外我将自动播放的选项关掉了，就像这样：  
{% highlight html %}
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width="330" height="86" src="http://music.163.com/outchain/player?type=2&id=29734857&auto=0&height=66"></iframe>
{% endhighlight html %}    

效果如下：  

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width="330" height="86" src="http://music.163.com/outchain/player?type=2&id=202373&auto=0&height=66"></iframe>   

### 推荐  
一首老歌，最近老是听，好听。歌词可以瞎猜猜什么意思。来自地下丝绒，Lou 之后还写过好多首 XXX says，其中的 Caroline says 基本上是这首的翻唱。     

<p class="message">
Stephanie says<br />
<small>
<i>The Velvet Underground</i><br /><br />
Stephanie says that she wants to know<br />
Why she's given half her life, to people she hates now<br />
Stephanie says when answering the phone<br />
What country shall I say is calling from across the world<br /><br />

But she's not afraid to die, the people all call her Alaska<br />
Between worlds so the people ask her 'cause it's all in her mind<br />
It's all in her mind<br /><br />

Stephanie says that she wants to know<br />
Why it is thought she's the door She can't be the room<br /><br />

Stephanie says but doesn't hang up the phone<br />
What sea shell sea is calling from across the world<br /><br />

But she's not afraid to die, the people all call her Alaska<br />
Between worlds so the people ask her 'cause it's all in her mind<br />
It's all in her mind<br /><br />

She asks you is it good or bad<br />
It's such an icy feeling it's so cold in Alaska,<br />
it's so cold in Alaska, it's so cold in Alaska
</small></p>     

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width="330" height="86" src="http://music.163.com/outchain/player?type=2&id=4334115&auto=0&height=66"></iframe>   
