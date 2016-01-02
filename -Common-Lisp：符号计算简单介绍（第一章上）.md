# 第一章 函数和数据
### 1.1导引
这一章讲通过一些内建的Lisp函数例子还对函数和数据的表示法做一个概览。假如你已经有一些其他语言的编程经验，你可以在几分钟内跳读这一章，你会看到一些有字符组成的运算函数，一个Lisp的关键数据类型，一些以yes或者no为结果的断言。当你认为你已经完全领会了全部内容之后们可以阅读概述那一章节来检验一下自己是不是真的理解了。
假如你是一个编程新手，那这一章就是专门为你设计的，我们将开始解释什么是函数什么是数据。数据的意思可以称作信息，例如数字，单词和一系列事物。你可以想象一个函数就像一个盒子，而数据从盒子的中间流过。函数以某一种方式操作数据，然后结果就是输出的数据。
在介绍了Lisp提供的一些内建函数之后，我们将学习如何将现有的函数组合在一起创造出一个新的函数（其实这就是计算机编程的本质）。引述出一些十分有用的创造函数的技巧和方法。
### 1.2操作数字的函数
或许大家最熟悉的函数就是简单地数学运算函数，加减乘除。下图体现的就是两个数如何相加：
![加函数.jpg](http://upload-images.jianshu.io/upload_images/46495-c7b10c57aafb7836.jpg)
这个函数的名字是“+”，我们可以用几种方式来描述，在上图中到底发生了什么。从数据的观点来看，数字2和3流入到函数中，然后数字5从函数中流出。从函数的观点来看，函数“+”接受数字2和3作为输入，然后产生5作为结果输出。从程序员的角度来看，我们调用或者说唤起函数“+”，输入是3和3，然后函数返回值5.这些都是用不同的方式讨论数据和程序是等同的；你将在本书的许多地方遇到这个观点。
下表表示的就是Lisp函数对数字的操作：

符号| 意义
----|----
+|计算出两个数加在一起的值
-|取得第一个数减去第二个数的值
*|计算两个数相乘得到的值
/ |计算第一个数被第二个数除得到的值
ABS|计算一个数的绝对值
SQRT|求一个数的根

让我们来看另一个数据流穿过函数的例子。ABS，输出绝对值的函数，顾名思义，正数不变，负数取反的函数。
![ABS.jpg](http://upload-images.jianshu.io/upload_images/46495-5af3bb4f00ed2da8.jpg)
数字-4输入到函数ABS，计算出来的绝对值作为结果输出4。
###1.3三种数字类型
在本书中，我们打的交道最多的是正数（integers），就是所有的自然数字。Common Lisp也提供了许多其他种类的数字。应该了解的一种就是浮点数（floating point numbers）。一个浮点数总是会被写成带有一个十进制的小数点；例如，数字5会被写成5.0。这个SQRT函数一般来讲是返回一个浮点数作为结果，就算输入是整数的情况下也是。
![SQRT.jpg](http://upload-images.jianshu.io/upload_images/46495-8e09fe9b13092ad2.jpg)
分数（ratio）是另一种数字形式。在便携式计算器中，1/2的表示常常是浮点数来表示的比如0.5。但是在Common Lisp中，我们也可以吧二分之一表示成1/2。Common Lisp把分数自动简化成为最小分母的形式；例如分数4/6,6/9，都会被简化成2/3。
当我们调用一个运算函数，并输入一个整数的时候，Common Lisp通常将会产生一个整数形式或者分数形式的结果。假如我们的输入时整数浮点数混合，结果就将会是一个浮点数。
![分数浮点数.jpg](http://upload-images.jianshu.io/upload_images/46495-836768394c545934.jpg)
### 1.4输入的顺序是十分重要的
按照惯例，当我们说函数的“第一个”输入的时候，我们指的是函数盒子左边最顶上的那个箭头。那“第二个”输入就是那个次高的箭头，以此类推。输入的顺序对于函数来说是非常重要的。举个例子，8除以2和2除以8是不一样的。
![分数.jpg](http://upload-images.jianshu.io/upload_images/46495-69ef04f63a9d5154.jpg)
当8除以2的时候，结果是4，而2除以8的时候，结果是1/4。另外有一点，在这里的分数并不一定是小于1的。
![大于1的分数.jpg](http://upload-images.jianshu.io/upload_images/46495-803714636af814a5.jpg)
### 1.5字符串（SYMBOLS）
字符串是Lisp中另一种类型的数据。大家会发现他们比数字会更有趣一些。字符串是典型的跟随英语单词来的（比如TUESDAY），还有词组（比如BUFFALO-BREATH），或者约定俗成的缩写（就像SQRT是“square root”的缩写）。字符串命名几乎包含了实际上所有的字母和数字的组合。加上一些特殊字符，比如连字符-。这里有一些Lisp字符串的例子：
X
ZORCH
BANANAS
R2D2
COMPUTER
WINDOW-WASHER
LORETTA
WARP-ENGINES
ABS
GARBANZO-BEANS
YAER-TO-DATE
BEEBOP
甚至可以写得很长
ANTIDISESTABLISHMENTARIANISM
你可能注意到了在这些字符串中包含了一些数字，比如“R2D2”，但是这不足以让他们成为数字。很重要的一件事情就是分清楚数字特别是整数和字符的区别，他们的定义会有帮助理解：
整数：由“0”到“9”组成的序列，可以选择“+”或者“-”作为前缀。
字符：任何字母，数字，和被允许使用的特殊字符（不包括数字）组成的序列。
所以FOUR是一个字符串，而4就是一个整数，+4也是一个整数，但是+就是一个字符串。而且7-11也是一个字符串。
###1.6 特殊的字符串
T Truth，真，表示“yes”
NIL 表示假“no”
T和NIL在Lisp中是基础不过的感念，以至于你问一个专业的Lisp程序员yesorno的问题，得到的回答往往不是英语，而是T和NIL。（“老王！去吃饭呗？”“NIL，刚吃了”）更重要的是说，详实的Lisp函数回答问题的答案也是T或者NIL。这样yesorno的问题被称作断言（predicates）。
### 1.7 一些简单的断言
一个断言就是一个回答问题的函数。断言的输出是固定的，在回答yes的时候输出T，回答no的时候输出NIL。我们将要学习的第一个断言是判断输入是不是一个数字。名字叫做NUMBERP（读作“number-pee”，就是“Number predicate”的简写）：
![断言1.jpg](http://upload-images.jianshu.io/upload_images/46495-c4593895ea5a5d92.jpg)
相似的，断言SYMBOLP就是测试输入是不是一个字符串，是的话就返回T，不是的话就返回NIL。
![断言2.jpg](http://upload-images.jianshu.io/upload_images/46495-ea4aa1280dcf719b.jpg)
![断言3.jpg](http://upload-images.jianshu.io/upload_images/46495-261c02c5f54c5b8a.jpg)
断言ZEROP，EVENP和ODDP只接受数字作为输入，如果输入是0的，ZEROP返回T。
![ZEROP.jpg](http://upload-images.jianshu.io/upload_images/46495-75e1f62d74bfb9b2.jpg)
如果输入是奇数的话，ODDP将会返回T，否则将会返回NIL。而断言EVENP怎是判断偶数。
![ODDP.jpg](http://upload-images.jianshu.io/upload_images/46495-2ddb875748f874b7.jpg)
到现在为止，你应该注意到凡是后缀P的函数名一般都是断言。（“老王！饿P？”“T，饿爆了”）虽说不是所有断言都遵守这个规律，但是大部分是的。
还有两个断言：<返回T，如果第一个输入大于第二个输入。（这两个也是我们后缀P规律的第一个例外）。
![大于小于.jpg](http://upload-images.jianshu.io/upload_images/46495-68c951ccfb68d1e1.jpg)
### 1.8 EQUAL断言
断言EQUAL是用来比较两个输入是不是相同。如果两个输入相同，EQUAL返回T，反之则返回NIL。在Common Lisp中的断言与EQUAL功能近乎相同的断言有EQ，EQL和 EQUALP,他们之间的区别还不会困扰我们，对于初学者，EQUAL是第一个需要掌握的。
![等于.jpg](http://upload-images.jianshu.io/upload_images/46495-45be6a2f9c1898fc.jpg)
![1.jpg](http://upload-images.jianshu.io/upload_images/46495-e346edca96f11bdb.jpg)
附注：第一章比较长，（上）到这里经原始函数讲完了，（下）开始讲组合函数。
