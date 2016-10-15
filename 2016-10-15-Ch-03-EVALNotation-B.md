---
layout: post
title: "学习Lisp-第三章-EVAL标记法-B"
categories: Lisp
excerpt:
tags: Lisp CommonLisp 翻译
---

* content
{:toc}

《Common Lisp 符号计算简单介绍》第三章 EVAL标记法 下半部分





## 第三章 EVAL标记法

### 3.11 错误定义一个函数的四种方式

对于初学者来说，用EVAL表达式来定义函数，在正确处理语句结构上会有一些问题。让我们来看看这个问题，以函数intro的定义为例：

```
(defun intro (x y) (list x ’this ’is y))
(intro ’stanley ’livingstone) → (stanley this is livingstone)
```

请注意，intro函数的参数列表是由两个字符串组成，x和y，既没有引号也没有圆括号，在函数体中的变量x，y也是，没有引号和圆括号。

第一种错误定义函数的方式就是，参数列表中的字符串被加上了其他东西。如果参数列表中的参数被加上了引号或者括号，那么函数就不会正常工作。初学者常常尝试去给参数列表加上一些东西，想要让列表还取代字符串作为输入，这是第一个错误。

```
(defun intro (’x ’y) :bad argument list
(list x ’this ’is y))
(defun intro ((x) (y)) :bad argument list
(list x ’this ’is y))
```

第二种错误定义的方式是，在本不该出现括号的函数体里出现了多余的括号只有函数调用会有括号包围，在变量上包围括号将会引发函数定义错误。

```
(defun intro (x y) (list (x) ’this ’is (y)))
(intro ’stanley ’livingstone) → Error! X undefined function.
```

第三种错误定义的方式是给一个变量加上引号。字符串必须没有引号才会被认为是指向变量的。这里有一些例子来指出给变量加上引号会有什么事情发生。

```
(defun intro (x y) (list ’x ’this ’is ’y))
(intro ’stanley ’livingstone) → (x this is y)
```

第四种错误定义的方式是没有给应该加上引号的地方加上引号。在intro函数中，字符串x和y都是变量，但是this和is不是变量。加入我们没有给this和is加上引号，一个未赋值错误就会出现。

```
(defun intro (x y) (list x this is y))
(intro ’stanley ’livingstone) → Error! THIS unassigned variable.
```

### 3.12 关于变量的更多

在Lisp中，一个函数当被调用的时候会自动创建变量，一般来说，函数运行结束的时候会把变量销毁。来看一下double函数，每次被调用的时候都会创造一个叫做n的变量:

```
(defun double (n) (* n 2))
```

在double函数的外部。字符串n指向全局变量n，全局变量n没有被赋值，所以对nde求值就会发生错误。

```
n → Error! N unassigned variable.
```
假设我们对（double 3）求值，在double函数内部，字符串n指向一个新创建的变量，会绑定在输入上，并不是绑定全局变量n上下面的求值回溯图展现了这一过程。

![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el85p2cs7xj20eh099t93.jpg)

如果我们再一次调用double函数，例如（double 8），一个全新的变量n就会被创造出来并赋值8.在double函数外部，n指向全局变量n，没有被赋值的变量。现在我们来尝试举例有两个变量的例子，这里是函数quadruple，通过double函数定义。

```
(defun quadruple (n) (double (double n)))
```

double函数和quadruple函数都会调用输入n，假设我们对表达式（quadruple 5）求值，如下面的图像表示。当我们进入函数quadruple背部，Lisp创造一个全新的变量n并且赋值5然后求值表达式（double （double 5））。当函数double的输入5的时候会发生什么？double函数创建了自己的变量n，绑定自己的输入5。double函数的函数体求值10，现在我们已经对（double n）求值了，所以我们可以用这个结果来对（double （double n））求值，double函数被再一次调用，这一次的输入是10，所以另一个变量n被创造出来，并且绑定输入，10.然后求值（* n 2）表达式，之后double函数返回20，quadruple函数返回29作为结果，然后我们就就是并再一次回到了顶层，变量n指向全局变量的地方。

![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el862ux9wij20i00k0dhf.jpg)

### 小结

在本章我们学习了EVAL表达式，使用将函数等用列表形式来表现的方法。Lisp根据一系列的求值规则来用EVAL表示法表示函数，有关求值规则，我们学习了：

- 数字是自求值的，求值也就等于自身，T和NIL也是如此。
- 在求值一个列表的时候，第一个元素被定义为一个函数来被调用，剩下的元素被定义为函数的参数，这些参数从左到右来求值，将输入导入传递给函数进行操作。
- 字符串在如果不是一个列表的第一个元素，那么就会被解释为指向变量。一个字符串将会求值为所命名的变量。准确的说，一个变量的值取决于字符串出现的位置。变量如果没有被赋值的话，一个未赋值错误就会出现。
- 一个加上引号的列表或者字符串会被求值为自身。
用列表的格式(defun 函数名 （参数列表） 函数体)来定义一个函数。defun函数是一个特殊的函数，他的输入将不会被引用，一个函数的参数列表就是参数字符串被赋予的输入值。在函数内部，变量所具有的值指向函数的输入。

### 本章所学的函数

求值器：EVAL.
定义函数的宏函数： DEFUN.

### 在电脑上运行Lisp

恭喜！你已经完成了所有纸笔上的学习过程，现在是学习如何在计算机上使用Lisp的时候了。很不幸的是，我不能给你更加详细的介绍；有很多类型的计算机和更加多的Lisp实现。你可能想要多花一点时间来熟悉各种用户手册，一个更加有效率的方法是找一个熟悉你所有的机器的人来请教。

### 3.13 运行Lisp

首先需要明确的事情是在你的计算机上如何运行Lisp。最好就是键入Lisp然后回车就好了，但是可能还需要更多更复杂的操作，当Lisp开始运行的时候或打印欢迎信息，每一个实现的欢迎信息都不相同，一般看起来是这样。

```
CMU Common Lisp M2.8 (29-Mar-89)
Hemlock M3.0 (29-Mar-89), Compiler M1.7 (29-Mar-89)
Send bug reports and questions to Gripe.
>
```

这个出现在欢迎信息里的>符号叫做顶层提示符（top-level prompt）。他表示Lisp现在正在等待你键入一些东西。一些Lisp实现使用了不同的额提示符，许多是用的星号*.
接下来你需要确定的事情是一些Lisp的控制字符：

- 如何删除一个字符:按下delete键，backspace键还是其他？
- 如何撤销一整行的输入，如果你不想要的话？在一些Lisp中你可以通过键入Control-U来实现这个功能（也就是按住ctrl键的同时按u）。
- 键入什么可以使得你回到顶层提示符？许多Lisp使用Control-G或者Control-C来实现。

当我们面对一些特殊的字符的时候，请注意，计算机总是将不同的相似字符分的很清楚的，比如字母o和数字0，啊还有字母l和数字1。当对于阅读的时候这些相似的字符对阅读不会有什么障碍，但是输入计算机的话就一定要清楚。
最后，你需要了解如何退出Lisp。大部分Lisp实现要求箭如意想（quit）或者（exit）来离开程序。一些时候文件结束字符想control-d也会起作用。

### 3.14 read-eval-print循环

一台运行Lisp的计算机很像一台便携式计算器，读入一个键入的表达式，计算他（使用EVAL函数），然后在屏幕上打印结果，随后另一个顶级提示符将会出现，等待下一个表达式的输入。这个过程被称作read-eval-print loop。

这里有一个计算机和我们的键入函数之间的简单对话。在这个例子中，键入的信息会在提示符>后面以小写出现；计算机的返回会用大写返回。不是所有Lisp实现都遵循这个惯例，但是大部分是的。

![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el89odd2azj20ui0a23zs.jpg)

### 3.15 从错误中恢复

一件很重要的事情就是学会如何从错误中恢复。首先来考虑打字拼写错误，如果见入了很长的一段文字记过发现在开始的地方有错误，就会想要放弃整个一行表达式重新开始。Lisp中是现代额方法是control-G的输入，然后回到顶级提示符，这里是一个例子：

![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el89wjmv4mj20u707mgm7.jpg)

一个更加普遍的问题是，表达式的拼写正确但是结果是一个计算错误。想要增加一个数字或者字符串是一半的做法。一个计算错误出现的时候，Lisp会打印错误信息，然后进入一个不同的循环。不同于上文提到的read-eval-print循环，我们现在称他为调试器（debugger）的read-eval-print 循环。我们将在第八章学习如何使用调试器，到现在为止，你需要知道的是如何才能离开调试器回到顶级提示符。在Lisp中，control-G就是这个脱出命令。

![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el8a3zk1jwj20u30asmy7.jpg)

如果你已经在Lisp中定义了一个函数但是却不能正常工作的话，那就重新定义一下然后再次尝试。你可以重新定义很多次，没有限制，只有最后一次定义会被保存和使用。接下来的例子就显示这个特性，也显示了你可以在一个表达式输入的时候，在任意地方回车都没有关系。这是因为表达式是列表。空格和缩进不会有影响。

![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el8abvhbxzj20tz0j040o.jpg)

请确认你没有使用想cons，+，或者list这类内建函数的名字，重新定义这些函数会出现比较严重的错误，你可能需要离开然后重新开始，而且之前定义的任何函数将会被废弃。

### Lisp Tookit:ED

出现在这里Lisp Toolkit小节和后续的章节会介绍Lisp编程环境的一些重要工具。在这些工具中，比如语言定义编辑器（Language-specific editor），编程格式转换（program formatters），和代码层次的调试器（source-level debugger）都可以在其他编程语言中找到并且使用。但是他们是最先出现在Lisp中的。其他工具仍然是Lisp独有的，其中两个，sdraw和dtrace，是这本书独有的。源代码在最后的附录当中。

我们最先接触到的工具就是Lisp编辑器，由于common lisp标准中并没有规定lisp实现利一定要用哪一种编辑器，所以现在我们没有办法给出你编辑器的具体细节。但是大概上，能告诉你，为什么他们不同于一般的文本编辑器，为什么你应该花时间来学习使用你的lisp实现提供的编辑器。

> 在lisp中最常见的额错误就是括号错误。因此几乎是一定要的就是在定义函数的时候每一次都检查括号的数量和配对。——Elaine Gord

上面的引言是写在25年前，那时候lisp程序员还是在打孔纸带上写程序。现在，当然我们使用交互式的编辑器。lisp编辑器不是普通的文本编辑器：编辑器“理解”lisp程序的语法。在我的机器上，无论什么时候我键入右括号，编辑器都会高亮左括号。这个功能防止了我在输入表达式的时候发生括号错误。另一个编辑器的工作就是自动缩进。如果一个函数占用多行，那么就会自动缩进来规范格式以增加可阅读。

一些早期的lisp书籍是在系统缩进这想法出现之前写就的，那时候的程序就是这样

![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el8bcs3x2kj20nv03ngly.jpg)

现在自动缩进之后就会变成这样

![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el8be271ojj20js05t74n.jpg)

还有两个好功能是lisp编辑器可以提供的，一个是在编辑表达式的时候就对表达式求值。你可以用鼠标点击几次定义的函数，在不离开编辑器的情况下就可以对表达式求值。第二个就是，对于在线文档的快速查看。假如我想要看任何lisp函数或者函数的文档，我可以用几次击键来唤出。编辑器本身支持在线文档。

Common Lisp标准定义了lisp实现和编辑器之间的接口。这个接口就是一个叫做ED的函数。在顶级提示符的状态键入（ED）将会进入编辑器。更多的lisp实现会提供更加快捷的方式，击键Control-E。ED也可以接受参数来进行操作。

## 第三章 进阶话题

### 3.16 没有参数的函数

假设我们想要写一个函数把85和97相乘。请注意这个函数是没有输入的，他计算的只是之前已经有的常量。既然没有输入，那么参数列表就是空的。空列表当然也就是NIL，我们来定义这个叫做test的函数：

```
(defun test () (* 85 97))
```

这之后，我们会看到

```
(test) → 8245
(test 1) → Error! Too many arguments.
```

test是一个函数，所以必须使用括号来调用，字符串test在没有括号的情况下会被解释为变量的引用。

```
test → Error! TEST unbound variable.
```

### 3.17 特殊函数quote

quote函数是一个特殊函数，他并不对自己的输入进行求值，而是把输入简单的返回作为输出。

```
(quote foo) → foo
(quote (hello world)) → (hello world)
```

早期版本的lisp使用quote而不是单引号来表示不对一些对象求值，也就是本来写成这样的

```
(cons ’up ’(down sideways))
```
在早期版本中写成这样的

```
(cons (quote up) (quote (down sideways)))
```

在现代lisp系统中，使用单引号作为quote函数的标记。然而在内部，系统会将单引号转换为quote函数。我们在使用多重的quote函数的时候可以看到这点。第一层quote会被求值过程使用，其余的单引号会被保留下来。
>’foo → foo
>’’foo → ’foo also written (quote foo)
>(list ’quote ’foo) → (quote foo) also written ’foo
>(first ’’foo) → quote
>(rest ’’foo) → (foo)
>(length ’’foo) → 2

### 3.18 字符串的内部结构

到现在为止我们已经通过他们的名字介绍了一些字符串，但是字符串在Common Lisp中实际上是组合对象，也就是说字符串是由不同的部分组成的。概念上，一个字符串有五个指针的组合组成，其中一个指针就是字符串的名字。其他的部分将在稍后定义。字符串fred的内部结构看起来如下：

![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el8dqyftjuj20cq08yjre.jpg)

上图在引号中出现的fred叫做字符串（string）。有关strings是一个字符的序列，我们会在接下来的第九章详细介绍。现在他满足的是储存字符串的名字。一个字符串和字符串的名字是两回事情。
一些字符串，想cons或者+，等等被用在lisp内建函数的命名上。这个字符串cons在函数内有一个指针指向编译代码对象（compiled code object）来表现机器语言指令来创建新的内存单元。

![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el8e0080g8j20jd08yjrq.jpg)

当我们画出一个lisp表达式的矩形表达式时候，比如（equal 3 5）。常常只是画出字符串的名字而不是展现内部结构。

![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el8e2t2vsgj20ty05ydfy.jpg)

我们要是展现更多细节的话，那么表达式（equal 3 5）将会都是这样。

![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el8e4a048uj20tl0esaau.jpg)

我们可以使用内建函数symbol-name和symbol-function来提取字符串的许多部分。接下来的对话就是展现这个，你会在自己的计算机上看见不太一样的结果，但是意思是差不多的。

### 3.19 lambda 表达式

lambda表达式是由Alonzo Church，普林斯顿大学的数学家，创造出来的。Church想要一个清楚的方式去表示函数，输入还有计算。在lambda表达式中，一个在一个数字上加上3的函数如下所示，λ既是希腊字母lambda: λx.(3+x)

John McCarthy，lisp的创造者，是Church先生的学生。他通过定义函数来继承了lambda表达式。函数λx.(3+x)在lisp中的等价形式是：(lambda (x) (+ 3 x))

函数f(x,y) = 3x+y2的lambda表达式会被写成λ(x,y).(3x+y2)，他的lisp形式就是(lambda (x y) (+ (* 3 x) (* y y)))

如所见，lambda表达式的语法和lisp是十分相似的，甚至更像defunct函数，但是不像defun函数，lambda并不是一个函数，他更像一个eval一样的制造器，我们将在第七章学习更多关于lambda表达式的内容。

defun函数的工作是将函数名称和函数本身连接在一起。当键入一个新的函数定义，例如half。将会有两种命名过程，第一个是，字符串half命名字符串对象，之后字符串对象指向函数。在下图中，你可以看到字符串half指向字符串对象half。函数部分指向一个函数对象。准确的函数对象是什么样子取决于Common lisp实现的不同。但是正如下图所示，可能是一个lambda表达式在某处。

![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el8exol9l7j20sg0910t7.jpg)

当然，lambda表达式也只是内存单元构成的列表。每一个lambda表达式中的字符都是五个指针组成的，比如N和/，/的叫名字叫做除法函数，包括一个指针指向内建的除法函数来实现除法功能，所以间接地，half函数指向了内建的除法函数，图1-3展现出了这些细节。

![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el8f43jhl7j20mz0p9t9y.jpg)

### 3.20 变量的作用域

变量的作用域是指变量能够被引用的区域范围，例如，变量n取得half函数的输入，作用域就是half函数的函数体范围。另一个方式去表达这个概念是说变量n是函数half的本地变量（local）。全局变量由不绑定的作用域，他们可已在任何地方被引用。

在求值回溯图中，本地变量的作用域被限制在粗箭头包含的创造变量范围内。在粗箭头之外的地方变量是不能被引用的、接下来的程序就是说这个。

```
(defun parent (n)
(child (+ n 2)))
(defun child (p)
(list n p))
```

程序是有错误的，我们来看看parent函数在创造一个本地变量n之后调用了child函数，问题在哪里呢？

![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el8fvn7qo4j20i60e8aax.jpg)

在求值回溯图中，粗箭头描绘了作用域范围。parent函数的作用域的n只是在parent函数体中有效，在child函数的粗箭头范围内，是不能被引用的。所以n出现在child函数中被解释为一个全局变量，没有被赋值过。因此就会出现赋值错误。

### 3.21 EVAL和APPLY

EVAL是一个lisp原始函数，每一次eval的使用都给出一层计算。

```
’(+ 2 2) → (+ 2 2)
(eval ’(+ 2 2)) → 4
’’’boing → ’’boing
(eval ’’’boing) → ’boing
(eval (eval ’’’boing) → boing
(eval (eval (eval ’’’boing))) → Error! BOING unassigned variable.
’(list ’* 9 6)) → (list ’* 9 6)
(eval ’(list ’* 9 6)) → (* 9 6)
(eval (eval ’(list ’* 9 6))) → 54
```

我们不会使用在任何程序中直接使用eval，但是会间接地一直使用eval。你可以想象计算机作为一台物理上的eval的表现形式。当lisp被启动，键入的每个项目都会被求值。
apply也是一个lisp原始函数，apply把一个函数或者一系列对象作为输入，他调用对应的函数使用这些对象作为输入。apply函数的第一个参数被加上#‘符号而不仅仅是加上引号’。#‘符号是一个合适的方法来把函数作为另一个函数输入的方法。这个将在第七章给出更多解释和细节。

```
(apply #’+ ’(2 3)) → 5
(apply #’equal ’(12 17)) → nil
```

传给apply的对象首先并不是被求值，在接下里的例子中，对象是一个字符串和一个列表，对字符串或者列表求值会产生错误。

```
(apply #’cons ’(as (you like it))) → (as you like it)
```

### 进阶话题中的函数

EVAL相关函数：apply
特殊函数：quote
