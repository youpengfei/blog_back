---
layout: post
title: "Rake 是什么？"
date: 2013-09-09 17:22
comments: true
categories: 闲散技术
---
 前一段时间忽然有点神经质的非得搞一个个人独立域名的博客。刚开始的是wordpress后来因为vps的问题就放弃了（主要是成本问题）s。后来很多大哥大姐们纷纷在博客上“痛心疾首”宣布要用换成[octopress](http://octopress.org/)。换成这个博客当然有诸多好处，最大的好处当然是不用花钱。
 > 这个世界上但凡不用花钱的东西，都得花精力去研究。
 
 研究对于我来说是再简单不过了。最大的屏障其实是学习使用这个博客的命令。毕竟我是一个java系统的程序员。对于像ruby这样的东西还不是很熟悉。还好这个博客系统已经做的相当成熟了，只要你有一个github帐号就可以搞定所有的事情，但是ruby的版本需要再1.9.3，跟官网一样就行。详细介绍怎么使用这个工具，官方网站上已经有了很详细的介绍了，我就不再扯闲篇了。言归正传，我开始使用rake这个命令。由于我以前没有用过ruby这个语言，所以不知道rake是什么。
 
 经过不断的Google，终于知道了她的庐山真面目，其实rake在java的世界里面也有一个与之对应的工具，那就是ant。不过很多人都会很郁闷，ruby是一个解释性的语言，代码是不需要编译的为什么会有一个像ant一样的东西呢。
 
 答案当然是肯定的，因为ruby的一个很成熟的框架叫rails。里面有一句很经典的名言。
 >[DRY](http://en.wikipedia.org/wiki/Don't_repeat_yourself) Don't repeat yourself
 
 所以不可能产生一个没有任何用处的东西。
 以上说明rake肯定是有用处的。有什么用呢？用过windows的人都知道有一个以bat为后缀的批处理文件，在linux里面有一个以.sh为后缀的文件（当然linux是不会计较你用任何一种后缀的）。这些东西都是需要批处理的。有了批处理，就有任务的概念。所以rake中也有任务的概念。具体的写法是这样的。文件的名称要叫RakeFile。
{% codeblock lang ruby%}
task :default => [:test]
task :test do
  puts "hello,world;"
end
{% endcodeblock %}

这样在当前目录下运行rake，就会打印出hello，world。这就是一个很简单的demo，当然是我从官方文档上面抄袭下来的。这是ruby代码。 也是可以些很多个任务。但是默认的就只有一个。例如：
{% codeblock lang ruby%}
task :default => [:test]
task :test do
  puts "hello,world;"
end
task :test2 do
  puts "hello,world2;"
end

task :test3 do
  puts "hello,world3;"
end
{% endcodeblock %}

如果你想要运行test2，需要运行rake test2。
当然rake还支持继承等多种功能。[官方网站](http://rake.rubyforge.org/)有更加深入的说明。
这篇博客就是为了解释rake是什么。希望能起到抛砖引玉的作用。
