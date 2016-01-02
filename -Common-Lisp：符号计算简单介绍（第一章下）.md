###1.9函数的组合
到现在为止我们已经了解了许多Common Lisp的内建函数，这些内建函数通常被称为原始函数，或者原语。我没将会以丰富的组合方式使用这些内建函数还创在新的函数。
####1.9.1定义函数ADD1
我们来定义一个函数来给它的输入加上1.已经存在原始函数满足条件：+函数将两个数字加在一起输出和的值。我们的ADD1函数将会取得一个输入，然后加1作为输出。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvk0vxnk4j20b2061jrg.jpg)
现在，我们已经定义好的ADD1函数可以用来给我们想要的任何数字加1。先画一个盒子然后附上名字ADD1，在提供一个输入，比如5：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvk2ykj1gj207h03hwec.jpg)
我们可以看看这个函数内部是如何工作的：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvk52dnpgj20bo062jrf.jpg)
####1.9.2定义函数ADD2
现在假设我们需要一个函数来个输入加上2.我们可以用定义ADD1同样的方式来定义ADD2。但是Lisp总是希望有多种方法来解决一个问题；有时候更会对寻找可选的方案着迷。例如，我们可以用两个ADD1来定义ADD2：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvk9puiu7j20dq06wmxd.jpg)
一旦我们定义了ADD2，就可以给我们想要的任何数字加上2.而从ADD2的外部来看，我们无从得知是上述哪一种方案在内部被采用了。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekvkb8z8btj207q03na9x.jpg)
但是当我们探寻ADD2内部的时候，可以很明确的看到正在发生的事情，数字5流入第一个ADD1函数，产生6作为结果，然后6又流入第二个ADD1函数，之后的结果就是7.
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvkdpmlkij20e2060wen.jpg)
如果我们再往深处看，我们可以看见+函数在每一个ADD1函数的内部工作。：
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekvkeycnxlj20j508dwf1.jpg)
当然这是我们现在所能达到的最深的层次，因为+函数已经是原始函数了。
####1.9.3定义TWOP函数
我们也可以用新学习到的知识来地你一一个自己的断言，其实断言也只是输出结果是特殊类型的函数而已。断言是返回结果是T或者NIL的函数。这个TWOP断言是判断输入是不是2的定义如下：
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekvkhkieoej20bb067wem.jpg)
一些使用TWOP函数的例子
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekvkhkieoej20bb067wem.jpg)
###### 练习题
1.4定义一个从输入中减去2的函数SUB2。
1.5说明如何使用ZEROP和SUB2还定义TWOP函数
1.6HALF函数返回的是输入的一半的数字。用两种方式来定义HALF
1.7定义一个MULTI-DIGIT-P断言，当输入大于9 的时候返回T
1.8下图函数的功能是什么？
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekvkhkieoej20bb067wem.jpg)
### 1.9.4定义ONEMOREP函数
我们来尝试定义一个双输入函数。一个ONEMOREP断言，用来测试第一个输入是不是大于第二个输入。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvkl449w9j20et094jrp.jpg)
清楚ONEMOREP是如何运行的了吧？假如第一个输入比第二个输入大1，给第二个输入加上1将会使他们相等。在这种情况下，这个EQUAL断言将会返回T。换言之，假如第一个输入并不是比第二个输入大1，那么两个给EUQAL断言的输入就不是相等的，所以就会返回NIL，例如下：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvkl449w9j20et094jrp.jpg)
在你的脑子里（其实大声说出来也没关系），追溯上例的数据在ONEMOREP中的处理流程，你可能会这么描述：“第一个输入是7，第二个输入是6,6先输入到ADD1，输出了7,两个7输入到EQUAL函数，既然7和7是相等的，所以EQUAL函数的输出是T。所以T即使ONEMOREP的结果。”当然也可以从另一个角度来描述：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvkn69u6aj20gf08wq39.jpg)
对于第二个例子你可能会这么说：“第一个输入是7，第二个输入是3,3输入ADD1函数，输出了一个4。7和4输入到EQUAL函数，既然4和7是不相等的，那么EQUAL的结果就是NIL，所以ONEMOREP的结果就是NIL。”。
###### 练习题
1.9定义一个断言TWOMOREP：假如第一个输入比第二个输入大2，则输出T。使用ADD2函数到定义中。
1,10找到一个方法，使用SUB2替换ADD2函数来定义TWOMOREP。
1.11定义一个AVERAGE函数，平均的的含义是两个数的和的一半。
1.12定义一个MORE-THAN-HALF-P断言：如果第一个输入大于第二个输入的一半就返回T。
1.13下图函数无论输入什么都会返回同一个结果，请问返回什么？
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekvkozertcj20gv05rt8x.jpg)
### 1.10NOT断言
NOT是一个对立断言：也就是会把yes变成no，把no变成yes。在Lisp术语中，意思就是说，输入是T，NOT就会返回NIL。而输入时NIL，NOT就会返回T。NOT的一个很好的特性就是结合一些其他的断言能够很方便地推导出他们的对立面；比如我们可以使用NOT和EQUAL结合来推导出不等于这个断言，或者通过NOT和ZEROP来推导出非零这个断言。我们将在下一节里看到这定义过程，首先来看一些NOT的例子：
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekvkqrwb4dj208x07ijrd.jpg)
按照惯例，NIL在Lisp中是唯一表示no的方法，任何其他输入都会被认为是yes。所以在NOT函数中，只要不是输入NIL就会输出NIL。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvksmsb3ij209m03lgli.jpg)
这不仅仅是一个无端端的惯例。将NIL定义为唯一的false表示，除了NIL之外的都被作为也是对待是非常有用处的。这个将会在后续章节作出解释。
######练习题
1.14将下列计算过程的结果写明：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvkuc0nfpj207o07jweg.jpg)
###1.11反转一个断言
假设我们要定义一个断言来测试两个输入是否不相等，就是相当于相等的对立面。我们可以从EQUAL断言开始建立把他的结果作为NOT断言的输入，最后得到结果。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekvkxugj25j20ep068glv.jpg)
因为这个NOT函数的关系，无论何时EQUAL函数得出T，NOT-EUQAL都会得出“NIL”的结论，相应的无论EQUAL函数得出NIL，NOT-EQUAL都会得出T。下例中，字符串PINK和GREEN是不同的，所以EUQAL输出NIL而NOT将其改变为T。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvl51fcpuj20hh05lt8y.jpg)
在下例中PINK和PINK是相同的，所以EQUAL输出T，NOT将其改变为NIL。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekvl64mx3yj20h105kmxd.jpg)
###### 练习题
1.15定义一个NOT-ONEP断言：当输入不是1的时候返回T
1.16定义一个NOT-PLUSP断言：当输入不大于0的时候返回T
1.17一些早期的Lisp方言是没有EVENP原语的：只有ODDP，说明如何使用ODDP来定义EVENP。
1.18在什么样的条件下，下例函数返回T？
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekvl76yptvj20ik05kweq.jpg)
1.19在输入NIL的情况下，下例函数的结果是什么？如果输入时T呢？是不是所有的数据经过这个函数处理之后结果都不变？如果输入是RUTABAGA那结果是什么？
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekvl853klqj20dk05omx9.jpg)
1.20真值函数：输入和输出都是真值的函数，就是T和NIL。NOT就是一个真值函数。（虽然NOT接受除了T和NIL意外的输入，但是却只关心输入是不是true）；写一个XOR函数，异或真值函数，当输入一个是T，一个是NIL的时候输出T，当两个输入同时是T或者同时是NIL的时候输出NIL（提示：其实没有看上去那么难）
### 1.20函数的输入的个数
一些函数的输入个数是固定的，比如ODDP，就要求一个输入，还有EQUAL必须是两个输入。但是有很多函数是可以接受不定数量的输入的。比如数学运算函数+-*/就接受任何数量的输入。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekvla0z047j207v03iq2t.jpg)
为了三个数相乘，就需要把前两个数先相乘，然后把所得的结果再和第三个数相乘，如图：
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekvlb50uv1j20fm078gls.jpg)
当-或者/收到两个以上输入的时候，结果就是由第一个输入依次被减（或者被除）被其余的输入。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekvlc9432lj208507owei.jpg)
当只有一个输入的时候，-和/的操作室不一样的。-的操作是把输入取负，换言之，就是把正号改为负号或者反过来，通过0减去这个数字来实现。/的操作是用1除以输入的数字，也就是给出一个倒数。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvm5a85m8j209d07yaa0.jpg)
双输入的情况明显就是定义算术运算函数的情况。当多于或者少于两个输入的情况下，实际上是被转换成为两个输入的情况来处理。例如上例中的4的倒数就是一个1/4的除法运算。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvm6ootrwj20co064mx8.jpg)
### 1.13错误（ERRORS）
虽然我们构建的函数系统十分简单，但是已有一些错误的类型存在于其中。给函数的输入时一个错误类型的时候就会报一个错误。例如，+函数可以加数字，但是不能加字符串：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvmb6349pj20ec038dft.jpg)
另一种错误就是给了函数太多或者太少的输入
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvmcekcp9j20d507it8y.jpg)
最后，错误的产生也有可能是因为一个函数不能按要求完成计算，比如当被要求某个数字除以0的时候就出现这种错误：
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekvmdi7kptj20cv03e748.jpg)
学习分别错误是学习编程很重要的一部分，你不用怀疑会花费大量的时间来熟练这项技能。因为很少有程序是在第一次编写就回回完美运行的。
######练习题
1.21下列每一个函数有什么不妥？
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekvmf7m57qj20fu0iyaay.jpg)

### 本章概述
在本章，学习了两种数据类型，数字和字符串。也学习了一些内奸的函数来操作数据。
断言是一种特殊类型的函数，根据输入的不同来使用T和NIL来回答问题。字符串NIL意思就是false，字符串T的意思即是True。实际上，在Lisp中，除了NIL之外都被看做是True。
函数在使用之前必须先被定义。我们可以通过组合不同现有函数的方式来创造新的函数。一个特别的有效的方式就是通过NOT函数来到处本来的对立面，这个方法经常使用在程序设计中，NOT-EQUAL函数就是从EQUAL函数导出得来。
#####复习题
1.22所有的断言都是函数吗？所有的函数都是断言吗？
1.23本章介绍的断言之中哪一个没有后缀P？
1.24NUMBER是不是一个数字？SYMBOL是不是一个字符串？
1.25为什么FALSE在Lisp中表示真？
1.26True or False？
（a）所有的断言接受T或者NIL作为输入
（b）所有的断言把T或者NIL作为输出
1.27举出一个例子，会引起EVENP的输入类型错误。举出一个例子会引起输入错误数字错误。
本章出现的函数：
数学运算函数：+-*/，ABS，SQRT

###进阶话题

在每一章的最后部分都有进阶话题章节，不但会介绍一些进阶的编程材料，而且会在更加光广阔的数学和逻辑层面来进一步介绍计算机编程。
本章节可以选读。初学者或许希望跳过这第一个难关直接通读全书。之后的章节也是一样，在一些地方会有进阶话题的材料。这些地方都被清楚地标示出来，可以轻松回过头来找到再读。
###1.14 Lisp的历史
Lisp的源起可以追溯到1956年，一个夏季的人工智能研究会议在达特茅斯大学举办。在这个会议上，John McCarthy学到了一个新的名词叫做表处理（List Processing），这个概念是由Allen Newellhe J.C.Shaw,Herbert Simon提出的。在50年代，大部分程序是由汇编语言来写的，是一种根据计算机硬件电路直接定义的语言，Newell，Shaw和Simon等人创造了一种更加抽象的语言，叫做IPL（Information Processing Language的缩写），来操作人工智能领域最重要的两个数据类型，字符串和列表。但是IPL的语法还是和汇编语言很相似（当然指一样糟糕）。
另一方面，在1950年代FORTRAN语言已经被开发出来。专门设计为排序后的数字化计算的FORTRAN被广泛应用在科学计算中。Fortran可以实现让程序员编写像A=(X+Y)*Z这样的代数表达式来替代编写汇编语言的指令程序。一项全面的革新出现了，那就是程序员编写类似于数学表达式那样的代码，而计算机来负责把这些代码翻译成汇编语言。这项新的创意是的FORTRAN成为了强力的数值计算语言。而McCarthy则想要建立在字符计算上同样强力的语言。
其中一个他提出的设想就是，在FORTRAN之上，建立一个列表处理的子程序集合来实现。这个想法被供职于IBM的Herbert Gelerntner和Carl Gerberich付诸实践，并且命名为FIPL（FORTRAN List Processing Language）。但是McCarthy他自己从IPL，FORTRAN和FLPL之中汲取灵感，在工作于达特茅斯大学和MIT的时候设计了LISP语言（LISt Processor）、Lisp的第一个版本在一台IBM704机器上被开发出来。
Lisp 1.5是第一个被广泛使用的Lisp方言。《Lisp 1.5 编程手册》也有McCarthy等人在1962年推出。
到了1964年，Lisp已经跑在一些诸如装了MIT兼容时分系统的IBM7094机器上了。从而也成为了第一个解释型语言。DEC（Digital Equipment Corporation）在Lisp的发展历史上也起到了突出的作用，最先运行早期Lisp的计算机之一就是DEC的PDP-1。PDP-6，PDP-10（之后的DECSystem-20）计算机也都为了Lisp的优化而特别设计。
60年代中期过后，Lisp实现开始出现分歧。MIT开发了MacLisp，而Bolt，Berank和Newman还有Xerox一起开发了InterLisp，标准Lisp 1.6是MacLisp的早期版本的一个分支。这些放眼中的每一个本质上都源自于Lisp 1.5，但是去都各自走向了不兼容的道路。
在70年代，Guy Steelehe Gerald Suss定义了一种新的Lisp方言叫做Scheme，从Algol语言家族中获取一些优点，结合Lisp强力的语法和数据结构。而后Scheme的扩展方言开始发展更新，同步于Lisp的发展。
到了80年代早期，事实上的互不兼容的Lisp实现已经有数十种，还有六七种主要的方言。于是由Scott Fahlman, Daniel Weinreb, David Moon, Guy Steele, and Richard Gabriel领导的项目Common Lisp应运而生，为的是整合现有方言的优点，融汇成一个紧密的整体。Common Lisp的第一个标准在1984年横空出世，而后1989年的修订版本也相继推出。Common Lisp很快就成为了包括学术向和工业向使用的Lisp的选择。其余的方言几乎销声匿迹。只剩下Scheme还在教育应用领域有着忠实的使用者。
许多重要的编程思想在与Lisp的碰撞中首次出现。编译和解释函数的融合，垃圾回收机制，递归函数调用，代码级别的追踪和调试，还有语法制导编辑。时至今日，Lisp依然是函数式编程，面向对象编程和并发编程风格等前言领域的主要语言。
更多关于Lisp历史的信息，请看之后McCarthy和Gabriel引述的扩展阅读章节。
附注：如果图片不能刷出来的话，请评论告知，尽量及时替换。
