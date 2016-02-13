title: 基于C#反射特性实现数据到实体的自动映射
date: 2016-01-18 12:34:19
categories: [编程语言]
tags: [反射,数据,C#]
---
&emsp;&emsp;在游戏开发中我们常常面临这样一种需求，即将策划提供的表格数据转化为相应的实体类。这个需求从某种程度上来说起着连接程序员和策划的作用，如同Shader起着连接程序员和美术的作用一样。我没有刻意贬低美术和策划的想法，可是国内大量游戏在游戏玩法上的类似、画面风格上的雷同，实在难以让我对国内的策划和美术产生敬意。因为在游戏玩法上无法做到创新，所以在这种情况下，以“数据驱动成长” + “游戏玩法复制”的策略基本成为了国内游戏研发的常规策略。这就造就了国内策划和美术的能力相当有限的情况，因为策划通常除了办公软件和脚本语言以外什么都不会，美术通常除了常用的3D建模软件以外什么都不会。我问大家为什么Shader要让程序员来写，大家苦笑道因为这是中国啊。
<!--more-->
&emsp;&emsp;好了，在文章开始说这番闲话的目的就是像告诉大家在工作中要学会换位思考，从某种程度上来说，中国的策划和美术是相当幸福的，因为和策划、美术交界的这些工作通常都是由程序员来做，我没有刻意黑策划和美术的意思，请各位知足吧！言归正传，我们今天的话题是基于C#反射特性实现数据到实体的自动映射。假设策划给了我们这样的一张Excel表格：

![实际工作中的表格一定比这个表格丑]()

现在需要我们将其转化为程序员熟悉的实体类，虽然解析Excel或者XML或者JSON这样的工作对一个程序员来说根本不能叫做工作，可是这样重复的事情一遍遍地做还能叫做程序员吗？程序员的使命就是让繁琐重复的事情变得高效简洁，因此对重复的事情我们要坚决说NO。如果你经常做这些重复的事情，请注意下面的内容，因为它会帮你从无止尽的循环中break出来。

#什么叫做反射？
&emsp;&emsp;

#反射能做什么？

#利用反射解析Excel

#利用反射解析XML

#小结
