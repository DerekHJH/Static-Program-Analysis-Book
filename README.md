# 简介

[PASCAL研究组主页上的公开课件](https://pascal-group.bitbucket.io/teaching.html)

## 简介

Getting started with static program analysis. Read this and start writing your first static program analyzer! We focus on the problem:

> ❓ How to automatically and efficiently guarantee software quality

静态程序分析入门。阅读此书并着手编写你的第一个静态程序分析器吧！本仓库关注一个非常重要的问题：

> ❓ 如何自动化地高效保障软件质量

## 什么是静态程序分析？

### 静态程序分析在计算机科学领域中的定位

![](.gitbook/assets/PL.png)

**静态程序分析**是**编程语言**中**应用**层面下的一个细分领域，它是一个非常重要的核心内容。

在理论部分，考虑的是如何设计一个语言的语法和语义，如何设计语言的类型系统等等问题；有了语言的语法、语义和类型系统之后，我们需要支撑语言的运行。因此，在环境部分，需要考虑如何为运行中的程序提供运行时环境——如何设计编译器，在运行时需要怎样的支持（如内存的分配管理）等等；应用部分则关注如何保证语言所写出程序的效率、安全性和可靠性，主要考虑如何对程序进行分析，验证和合成（如何自动合成一个程序）。

#### 编程语言的分类

当今的计算机世界，面对这样一条恶龙： 👇

> 数十年来语言的核心没有变化，但软件的规模和复杂性增长迅速，如何保证程序的可靠性？

尽管新的语言和特性层出不穷，但现在编程语言无非三大类：

* 命令式（C、C++、JAVA），imperative programming
* 函数式（[Scala](https://www.scala-lang.org/)、[Haskell](https://www.haskell.org/)），functional programming
* 逻辑式（[Prolog](https://en.wikipedia.org/wiki/Prolog)），logical programming

### 静态程序分析的应用

静态程序分析即是屠龙的宝刀之一，举例来说这一技术可以处理以下问题\(_以下概念只需要了解，重要的应用在后文中会详细讲解。_\)：

1. 提高程序可靠性
   * Null pointer dereference, memory leak, etc.
   * 空指针引用与内存泄漏等：几乎每个程序编写者都被这两个问题所困扰过
2. 提高程序安全性
   * Private information leak, injection attack, etc.
   * 隐私信息泄漏：这一问题在移动应用中较为普遍，如果你感兴趣，可以参考[这篇论文](https://www.ieee-security.org/TC/SP2012/posters/ScanDal.pdf)。
   * [注入攻击](https://en.wikipedia.org/wiki/Code_injection)：这是网络安全中非常常见的议题。不熟悉的读者可以查看[W3School](https://www.w3schools.com/sql/sql_injection.asp)或[Wiki](https://en.wikipedia.org/wiki/SQL_injection)上关于SQL注入攻击的例子。
3. 为编译优化提供基础技术
   * Dead code elimination, code motion, etc.
   * [死代码消除](https://en.wikipedia.org/wiki/Dead_code_elimination)。
   * [循环不变量的代码移动](https://en.wikipedia.org/wiki/Loop-invariant_code_motion)。更为详细的解释可以参考[StackOverFlow上的回答](https://stackoverflow.com/questions/5607762/what-does-code-motion-mean-for-loop-invariant-code-motion)。
4. 有助于程序理解
   * IDE call hierarchy, type indication, etc.
   * 为集成开发环境的功能提供帮助：当你使用VS/Idea/Clion/Eclipse/Android Studio等等IDE时，将鼠标悬停在代码上，IDE能够动态地分析并提示你所悬停对象的相关信息，背后使用的技术就是静态程序分析。

此外，静态程序分析技术也可以分析多线程程序，这是一个有难度的研究领域。主要困难在于处理多线程间的interleaving。

### 静态程序分析的市场

![](.gitbook/assets/market.png)

* **无论你将来想在学术界还是工业界深入发展，学习这一领域的知识都能为你建立独特的优势。**

### 静态程序分析与类似技术的对比

> Testing shows the presence, not the absence of bugs. --Edsger W. Dijkstra

动态的软件测试和形式化语义的验证的作用与静态程序分析类似，这一部分对这三个细分方向做简单的对比。

#### 静态程序分析

* 优点：在选定的精度下能够保证没有bug。这在教程中会详细介绍。
* 缺点：
  1. 学术门槛相对高。目前已知国内高校公开的课程资料只有北京大学，南京大学，国防科大，吉林大学的，且通俗易懂的教材稀少（详细课程及教材链接见[本文末尾](#refs)）。作为一门计算机专业的高年级选修课，入门和提高都较困难。
  2. You tell me.

#### 动态软件测试

* 优点：在工程中被广泛应用，并且有效。实现简单，便于自动化。
* 缺点：
  1. **无法保证没有bug。** 这是无法遍历所有可能的程序输入的必然结果。
  2. 在当今的由多核与网络应用带来的**并发环境下作用有限。** 某个bug可能只在特定情况下发生，因而难以稳定地复现。如果你对并发程序的动态测试细节感兴趣，可以参考[《拧龙头法测试并发程序》](https://zhuanlan.zhihu.com/p/51341151)。（截图来自南京大学《形式化语义》课程资料）

![](.gitbook/assets/concurrentProgram.png)

#### 形式化语义验证

* 优点：由于用数学的方法对程序做了抽象，能够保证没有bug。
* 缺点：
  1. 学术门槛较高，学习者必须有良好的数学基础才能入门。
  2. 验证代价较高，一般来说非常重要的项目会使用这一方式保证程序质量。甚至在操作系统这样重要的软件中，也并不一定会使用。\(截图来自鸿蒙OS直播发布会\)

![](.gitbook/assets/harmonyOS.png)

## 其他相关项目

### 软件质量保障相关

[《软件测试简介》Gitbook在线阅读地址](https://ranger-nju.gitbook.io/software-testing-intro)

[《软件测试简介》GitHub项目地址](https://github.com/RangerNJU/Software-Testing-Intro)

### 前辈们写过的优秀笔记

[适合预习和同步阅读，CSDN+简书](https://blog.csdn.net/panhewu9919/article/details/106007155)

[适合复习，个人博客笔记](https://fancypei.github.io/SA/)

## 进一步学习的资料

### 课程视频和阅读资料

- 北大熊英飞老师的《软件分析技术》课程视频公开在了[这里](https://liveclass.org.cn/cloudCourse/#/courseDetail/8mI06L2eRqk8GcsW)。
- CMU Jonathan Aldrich老师的[《17-355/17-665/17-819 Program Analysis》(2019 Spring)](https://www.cs.cmu.edu/~aldrich/courses/17-355-19sp/)。
- Anders Møller and Michael I. Schwartzbach的[《Static Program analysis》](https://cs.au.dk/~amoeller/spa/) 以及配套的 [《Lecture Notes on Static Analysis》](https://lara.epfl.ch/w/_media/sav08:schwartzbach.pdf), [视频](https://www.bilibili.com/video/BV17K4y1t727) (推荐开启cc英文字幕)
- 张健, 张超, 玄跻峰, 熊英飞, 王千祥, 梁彬, 李炼, 窦文生, 陈振邦, 陈立前, 蔡彦. 程序分析研究进展. 软件学报, 2019, 30(1): 80-109.http://www.jos.org.cn/1000-9825/5651.htm
- 国防科技大学陈立前老师的[《高可信软件技术-程序分析部分》](https://www.educoder.net/classrooms/7759/attachment)
- 吉林大学刘磊老师的《程序分析技术》24集视频（超星/尔雅学术视频，需要账号登录搜索，B站上视频不全），[书籍](https://book.douban.com/subject/24733130/) 。算是国内最早的公开视频以及专门的教材了
- 东南大学李必信老师的[《程序切片技术及其应用》](https://book.douban.com/subject/1815952/)

### 开源软件

- [Pysonar2的GitHub Repo](https://github.com/yinwang0/pysonar2)
- [Soot的GitHub Repo](https://github.com/soot-oss/soot)
  - [Wiki Tutorial：入门Soot时很重要的资料](https://github.com/soot-oss/soot/wiki/Tutorials)
