###2.11 构造（CONS）
CONS函数是创造内存单位的函数，这个函数接受两个输入，返回一个指向内存单元的指针，这个创造出来的内存单元。CAR指向第一个输入，CDR指向第二个输入。函数名CONS就是construct构造的缩写。
加入我们用括号表达式来解释CONS函数的行为，那就是在列表之前插入了一个元素。例如把字符串A插入到列表(B C D)的首部、
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekzhpxjgarj209c02t0sk.jpg)
另一个例子：把字符串SINK插入到列表(OR SWIM)中。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekzhsjsn4mj20bj035745.jpg)
有个养一个函数GREET（问候）是把字符串HELLO插入到任何对象之前形成列表：
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekzhuyqmklj20k50cxq3d.jpg)
为了切实理解CONS的运行，最好使用内部表示法来思考。CONS是一个极其简单的函数，他并不知道“在列表的前面”是具体什么意思，（请注意，在计算机内部是没有圆括号标示的）。所有的CONS所做的事情就只是创造一个新的内存单元。但是计入第二个输入是一个列表，长度为n，那么新的列表将会是长度为n+1。请看图2-1，CONS函数返回一个内存单元的指针，在效果上看，返回的是一个长度加1的列表。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el2c06q2qej20k20mcjse.jpg)
###2.11.1 CONS函数和空列表
既然已知NIL是空列表，那么一个对象和NIL作为输入，CONS函数输出的就是一个单元素列表。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2c3zbvqpj209102y3yc.jpg)
通过观察结果的内部表示可以确认，结果的CAR是指向字符串FROB，CDR是指向NIL，所以CONS函数实际上是创造了一个列表。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2c7gt3ojj205202qt8i.jpg)
还有另外一个例子，可以吧FROB用NIL来替代。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2c9whk6zj2082033jr7.jpg)
如果是从打印表达来看的话，将某个对象和NIL组合在一起，其实就是加上了一层圆括号。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el2cbwgvtrj20cx03e3ye.jpg)
### 2.11.2 使用CONS函数创造嵌套列表
CONS函数的第一输入如果是一个非空列表，结果就将会是一个嵌套函数，是一个多层次的额内存单元结构。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el2cg62d9ej20dr06bq30.jpg)
### 2.11.3 CONS函数直接创造列表
假设我们现在想要直接创造列表(FOO BAR BAZ)。我们会先将字符串和NIL组合创造出列表(BAZ)。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el2cl2seooj207l02xt8j.jpg)
然后再把BAR加上：
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el2cldc74gj20900383yd.jpg)
最后加上FOO：
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el2cldpeeuj20bp038glh.jpg)
类似于下图的模型，我们直接创造了列表(FOO BAR BAZ)。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el2cpf8o45j20es07nglm.jpg)
如果从横向看这个图，那么就会发现这和列表(FOO BAR BAZ)的内存结构图一模一样，这也是给出了一个线索，为什么CONS函数和内存单元（cons cell）使用了相同的名字。
###### 练习题
2.18 写一个双输入函数，使用CONS函数将他们组成一个列表。
 ### 2.12 CONS函数和CAR/CDR的互相转换
一个有趣的现象就是CONS函数和CAR/CDR之间的相互转换。给出一个函数x。x的CAR和CDR组合在一起就是x本身。例如，列表x的CAR是字符串A，x的cCDR是列表(E I O U)，那列表x肯定就是(A E I O U)。
这种互相转换的形式还可以使用等式来表达。
x = CONS of (CAR of x) and (CDR of x)
然而，这种转换关系只有在，列表非空的情况下成立。当x是NIL的时候，列表x的CAR和CDR也是NIL。加入我们将x的CAR和CDR部分组合在一起，也即是NIL和NIL组合，昌盛的不是空列表NIL而是列表(NIL)。这就是意味着，他们是相同的，但是就我们所知，NIL和(NIL)是不同的。这也提醒我们NIL并不是一个寻常的列表。现在的既有现实就是，这个互换性只支持非空列表，也就是至少列表中需要含有一个元素。
### 2.13 列表
把一些元素组合起来狗在一个列表在Lisp中是一个很普遍的操作，LIST函数就是实现这个功能的内建函数。LIST函数接受任何数量的输入，并把它们结合成一个列表作为结果输出。LIST函数构造了一个以NIL为结束的内存单元链条，元素数量就是输入的数量。图片2-2显示了这一个具体过程。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el2erapd50j20ab035dfp.jpg)
回顾CONS函数总是构成一个新的内存单位，行为是降低一个输入加入到第二个输入当中。而LIST函数是完全创造一个新的内存单元链条。在括号表达式中，表现为用圆括号将所有输入包括进来，无论输入的数目是多少。LIST函数的输出结果总是比输入要多一层的括号。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2evj9rnvj207n035q2r.jpg)
LIST函数分配三个新的内存单元：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el2fgjczw1j20af034jr7.jpg)
填充CAR的指针
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2fgxdxjoj20ag031jr8.jpg)
填充CDR指针并形成链条，返回第一个内存单元的指针
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2fgxojbfj20f1047mx3.jpg)
图片2-2 LIST函数如何形成一个新的列表
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el2fm1k975j209002umwz.jpg)
LIST函数的工作实际上是构成一个新的内存单元链条，CAR部分是指向各个输入的指针，而输出的结果就是第一个内存单位的指针。下面是一些例子：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el2freul0uj208v0fwdg1.jpg)
下面是一个叫做BLURT函数的定义，接受两个输入并且使用它们填充句子中的空缺。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el2fu2fda6j20ah06edft.jpg)
BLURT函数举例：
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el2fvb2l0oj20b203bjr9.jpg)
我们现在来看看CONS函数了LIST函数的区别，CONS函数够早的是一个内存单元，而LIST函数构造的是一个新的内存单元链条，并且不论输入数量。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el2fy3nnpxj20fa09pmxa.jpg)
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el2fy4227gj20f2031jra.jpg)
理解LIST函数的拎一个方式就是将其看做CONS函数的级联调用。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el2g5xuv7qj20kg0cx74q.jpg)
######练习题
2.19 写出下列计算过程的结果
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2g734n1wj20ag0g00sy.jpg)
###2.14 替换一个列表中的首元素
假设我们想要用字符串WHAT来替换一个列表中的首元素。首先REST函数可以被用来提取不包括首元素在内的子列表，然后CONS函数再把字符串WHAT加入到首元素的位子上。这样一个自定义的函数被称为SAY-WHAT。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2gf2g7flj20ck07tq30.jpg)
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2gg5yox0j20dq0373ye.jpg)
(TAKE A NAP)输入REST函数之后输出的结果是(A NAP)。将字符串WHAT组合到和这个列表中就得到了新的列表(WHAT A NAP)。
如所见，SAY-WHAT函数并不是真的替换了列表的首元素。所做的事情是产生了一个新的列表然后将生层的内存单元的CDR指针指向了这个列表。最后的结果如下图所示：
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2gl59mjyj20fg06rt8q.jpg)
######练习题
2.20 下列计算过程返回的结果是？
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el2gmlexcrj20cy0jft92.jpg)
2.21 定义一个四输入函数，返回一个两元素的嵌套函数。第一个元素师前两个输入构成的列表，第二个元素是后两个输入构成的列表。
2.22 假设我们要定义一个叫做DUO-CONS 的函数，在列表之前加上两个元素。请注意一般的CONS函数式在列表前面加上一个元素。DUO-CONS函数将会接受三个输入。例如，假设输入是字符串PATRICK，字符串SEYMOUR，还有列表(MARVIN)，那么DUO-CONS函数的输出将会是列表(PATRICK SEYMOUR MARVIN)。请定义这样一个函数。
2.23 TWO-DEEPER函数的作用是给输入加上两层括号。例如，输入是TWO-DEEPER，输出是((TWO-DEEPER))，输入是(BOW WOW)输出就是(((BOW WOW)))。请使用LIST函数来定义这个TWO-DEEPER函数。并使用CONS函数来定义另一个版本。
2.24 什么内建函数可以将字符串NIGHT从列表(((GOOD)) ((NIGHT)))中提取出来？
### 2.15 LIST断言
假如输入是列表LISTP输出T，如果不是列表就输出NIL。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2h6f2811j20ko0913yl.jpg)
如果输入是内存单元（cons cell）CONSP断言就会返回T。CONSP几乎等同于LISTP，只是在对待NIL作为输入的时候有所不同。NIL是一个列表，但却不是一个内存单元。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el2ha3rcd5j20be09tdfu.jpg)
如果输入不是一个内存单元，那函数ATOM就会返回T。函数ATOM和函数CONSP刚好是相对立的，同一个输入的情况下，一个返回T的同时，另一个必定返回NIL。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el2hee93iij20ho0e80sy.jpg)
单词ATOM来源于希腊语atomos，意思是不可分割的，原子。数字和字符串因为不能再拆分所以是不可分割的，而非空列表不是不可分割的。FIRST函数和REST函数就可以拆分他们。
NULL断言在输入是NIL的情况下返回T。这个行为和NOT断言是一样的。根据惯例，Lisp程序员在准备逻辑操作的时候使用NOT，比如把true改成false，false改成true。放加测一个列表是不是为空的时候使用NULL。
###小结
本章介绍了在Lisp中最丰富的数据类型，列表。列表同时具有打印形式和内部存储形式，列表可以包括字符串，数字或者其他列表作为元素。
我们可以使用CAR或者CDR（FIRST和REST）来把列表拆分，也可以使用CONS函数和LIST函数来构造列表。LENGTH函数可以计算在列表中的元素数目，这个数目也就是列表的顶层元素的个数。
CAR和CDR的要点在于：
- CAR和CDR只接受列表作为输入
- FIRST函数和REST函数与CAR和CDR相同。
- SECOND和THIRD等同于CADR和CADDR.
- Common Lisp提供了C____R的内建函数，其中间由四位组合。

字符串NIL也有一些重要事项：
- NIL是一个字符串，在Lisp中是表达no或者false的唯一手段。
- NIL是一个列表，空列表，长度是0。
- NIL在Lisp中式唯一一个既是字符串又是列表的对象。
- NIL是一个内存单元链条的结束标记。当列表以打印形式来体现，按照惯例，链条的结尾的NIL是被省略的。
- NIL和()在内部形式中式等同的。
- NIL的CAR和CDR被定义为NIL。
######复习题
2.25 为什么cons cell和CONS会有相同的名字？
2.26 下面两个函数在给予相同输入(A B C)的时候会怎样操作？
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el2hyfq98zj20jh0ftdgb.jpg)
2.27 在什么时候，一个列表包含的内存单元要比这个列表所有的元素还多？
2.28 试过只是使用CAR和CDR，有没有可能定义出一个函数来返回列表的最后一个元素，无论这个列表有多长？请解释。
###本章涉及的函数
列表函数：FIRST,SECOND,THIRD,FOURTH,CAR,CDR,CONS,LIST,LENGTH.
CAR和CDR的组合：CADR,CADDR等等。
断言：LISTP,CONSP,.ATOM,NULL。
## 第二章进阶话题
### 2.16 列表的单数算术运算
列表是可以用在单数（位数是1）算术运算上的。在这个系统中，数字式被表示成标记符号组成的列表，就像一个在监狱中的犯人在牢房的墙上刻下记号来数过去了几天。数字1就是一个标记符号，2就是两个标记符号，以此类推。数字0被标记为没有标记。我们不考虑附属的情况。
假设使用x作为标记符号，我们可以将数字写成标记符号x组成的列表。
0就是NIL
1就是(x)
2就是(x x)
3就是(x x x)
鉴于已经定义的单数，我们可以研究一下，列表操作函数来操作他们。REST函数在数字钟减去1.就像SUB1函数定义的一样，从自然数中减去1，
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2j0aeclaj20ak03w744.jpg)
1减去1的情况就是得到0：
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2j1diq3sj20a4045dfo.jpg)
但是0减去1的道德并不是-1，请注意我们定义的个位数运算时没有负数的。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el2j34xuoij209503p744.jpg)
LENGTH函数可以讲这个标记列表转换成为自然数：
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el2j4pdj9rj20c203x0sl.jpg)
不是所有列表原始函数都能在一元运算中使用。CAR函数不可以。然而，将费原始函数运用在一元运算中是可能实现的。
######练习题
2.29 写一个函数UNARY-ADD1来给个位数加1、
2.30 CDDR会对艺一元数字有什么操作？
2.31 写一个UNARY-ZEROP断言。
2.32 写一个UNARY-GREATERP断言，功能类似于>断言。
2.33 CAR也可以被看做一个一元数字的断言。不是返回T或者NIL，CAR返回的是X或者NIL。请注意，X或者其他任何非NIL的对象在Lisp中都被看做true。当一元数字输入的CAR函数的时候会有什么问题？
###2.17 非列表单元结构
一个严格意义上的列表是用NIL来作为结尾的。作为管理，括号表达式的时候是省略括号的。所以下列表达就能被写成(A B C)。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el2jermdx1j20f103bgli.jpg)
还有另一种内存单位结构并不是严格意义上的列表，因为在结尾并不是指向NIL。这样的结构该如何用括号表达式来表达呢？
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el2jh1jktuj20es03m747.jpg)
开始打印一个列表的括号表达式的时候，Lisp最先从做便当与那括号开始打印，但后是依次的个股元素，并且用空格分割开来。加入这个列表是用NIL来作为结尾，Lisp将会用右括号来结尾，如果不是NIL来结尾，在打印右括号之前先打印一个空格，一个小数点，再来一个空格，然后是一个院子对象结束。这样的列表称作点式列表(dotted list)，并不是严格意义上的列表。
(A B C . D)
到现在为止，产生不以NIL为结尾的内存结构的方法只有通过CONS函数。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el2jn6ii4ej209g03sjr8.jpg)
CONS函数的结果被称为点对（dotted pair）。括号表达式写成（A . B）,内部形式是这样
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el2kag9qc9j204v03bmwy.jpg)
一个点对是一个CDR指向不为NIL的内存单元。点列表(A B . C)包括两个内存单元，构成如下：
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el2kteft1tj20eq06odft.jpg)
在内部形式中，(A B . C)是这样
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el2kuykjc8j209x03hjr8.jpg)
虽然LIST函数式笔CONS函数更加方便的构成列表的方法，但是LIST函数只能构成一般的列表，也就是NIL结尾的列表，不能构成点式列表，点式列表就必须用到CONS函数。
######练习题
2.34 写一个表达式，来构造列表(A B C . D)，使用CONS函数的级联调用。
2.35 画出点式列表((A . B) (C . D))的内部表达，写一个表达式来狗仔这个列表。
###2.18 循环列表
点式列表看上去可能有点奇怪，但是更奇怪的也是有的，就是要介绍的循环列表（circular list）：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el2l4svodwj20ex04naa0.jpg)
如果计算机想要展示上图中的列表，那么一些问题就可能出现，根据各个打印设备的不同有所不同，这个一会儿再讨论。计算机可能陷入没有尽头的循环当中，或者尝试打印一部分的列表，使用省略号等等：
      (A B C A B C A B ...)
这个种方式是不正确的，因为他表现了列表包含超过十个以上的元素， 但是实际上列表只有三个元素。
Common Lisp提供了完整而正确的方法来解决循环结构，使用的而方法就是以井号#为基础的，井号等位标记法（sharp-equal notation）。基本上，为了表示循环结构，我们需要一个方法，来给一个内存单元赋予一个标签，然后稍后才能再找回到这里。（例如，上图中的循环列表，第三个内存单元的CDR指向回到了第一个单元。）我们使用证书作为标签，然后这#n= 来标记一个对象，用#n#来在表达式中表示对象位置。
       #1=(A B C . #1#)
######练习题
2.36 反驳：列表不能只用CONS函数来构造。提示：想想单元被构造起来的顺序。
更加奇怪的是下面这一个，这个内存单元的CAR是指向自己的。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el2mahngozj207i03yglf.jpg)
如果计算机想要打印这个结构，那么将会技术在一个左括号循环中。但是如果是命令打印井号等位表达式：
      #1=(#1# . A)
###2.19 非列表内存结构的长度
列表的长度是顶层层次的内存单元个数，但是列表(A B C . D)的长度是3，而不是4，同样长度的列表是(A B C)，同样也可以写成(A B C . NIL)。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el2mfe0c8dj20bl03vglg.jpg)
如果给出一个循环列表作为输入，例如#1=(A B C . #1#)，LENGTH函数可能返回一个值，在大部分视线中是会陷入无限循环。

