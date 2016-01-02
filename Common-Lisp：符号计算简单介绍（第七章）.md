#第七章 函数式编程（Applicative Programming）
###7.1 导语
本书设计的三种编程风格是函数式（applicative）编程，递归（recursion），还有迭代（iteration）。很多人倾向于先教递归，但是我相信函数式编程对初学者来说是最容易学习的。为了调和所有人的口味，第七章和第八章是互相独立的；可以以你喜欢的顺序阅读。
函数式编程的基础是基于函数就是数据这个概念上的，就像字符和列表都是数据，所以把一个函数作为另一个函数的输入也是可能的，并且返回一个函数作为值。我们将在本章学习函数操作，函数作为另一个函数的输入并且将它应用的列表的元素上。这写操作都是从一个原始函数开始建立的，他的名字叫做funcall。在进阶话题中，我们将写我们自己的函数操作，并且写一个构造，返回新函数的函数。
###7.2 funcall
funcall函数函数来处理输入，我们可以使用funcall来调用cons函数处理输入A和B。
>######(funcall #’cons ’a ’b) → (a . b)

井引号标记#'是一个在Common Lisp中引用函数的正确方式，如果你想要看函数cons在你的实现中看起来是什么样，请在你的Lisp中尝试一下例子：
>######> (setf fn #’cons)
>#######<Compiled-function CONS {6041410}>
>######> fn
>#######<Compiled-function CONS {6041410}>
>######> (type-of fn)
>######COMPILED-FUNCTION
>######> (funcall fn ’c ’d)
>######(C . D)

变量fn的值是一个函数对象。type-of函数显示fn是一个compiled-function函数类型。所以你看到函数和字符不是相同的。字符cons是作为cons函数的名字，但是不是真实的函数。函数和字符之间的关系是他们被解释的名字。
请注意只有一个普通函数能被#‘引用。引用宏函数或者特殊函数将会报错，引用一个#’自身或者没有指向函数的字符也会报错。
>######> #’if
>######Error: IF is not an ordinary function.
>######> #’turnips
>######Error: TURNIPS is an undefined function.

###7.3 maocar操作
mapcar是函数式操作里面使用最频繁的操作。他处理一个列表的每一个元素，一个一个处理，然后返回一个结果的列表、假设我们写好了一个函数来给个位数求平方，原先来说，函数是不会求列表的平方的，因为*函数不会操作列表。
>######(defun square (n) (* n n))
>######(square 3) → 9
>######(square ’(1 2 3 4 5)) → Error! Wrong type input to *

使用mapcar我们就可以吧square应用到列表中的每一个元素。将square函数作为mapcar的输入，我们要把#‘加在square前面。
>######> (mapcar #’square ’(1 2 3 4 5))
>######(1 4 9 16 25)
>######> (mapcar #’square ’(3 8 -3 5 2 10))
>######(9 64 9 25 4 100)

这里有一个关于mapcar操作的图示。如你所见，输入的列表的每一个元素都被独立地定位到输出的元素当中。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1elezsbqznaj208907sglm.jpg)
mapcar操作被用在长度为n的列表上，结果也会是一个长度相同的列表。所以mapcar如果被用在空列表上，那结果也是空列表。
>######(mapcar #’square ’()) → nil

###7.4 使用mapcar操作表格
假设我们使得全局变量words构成一个英语和发育的单词表格：
>######(setf words
>######’((one un)
>######(two deux)
>######(three trois)
>######(four quatre)
>######(five cinq)))

我们可以使用mapcar在表格上实行一些有用的操作。通过提取每一个入口的第一个部分既可以提取英语部分。
>######> (mapcar #’first words)
>######(ONE TWO THREE FOUR FIVE)

我们也可以通过提取每一个入口的第二个部分来提取法语单词。
>######> (mapcar #’second words)
>######(UN DEUX TROIS QUATRE CINQ)

我们可以从英语法语的一一对应的表格元素里创造一个法语英语的字典。
>######> (mapcar #’reverse words)
>######((UN ONE)
>######(DEUX TWO)
>######(TROIS THREE)
>######(QUATRE FOUR)
>######(CINQ FIVE))

给出一个函数translate，通过assoc来定义，我们可以翻译一个英文的字符串到一个法语的字符串。
>######(defun translate (x)
>######(second (assoc x words)))
>######> (mapcar #’translate ’(three one four one five))
>######(TROIS UN QUATRE UN CINQ)

除了mapcar，还有一些其他的Common Lisp内建的函数式操作。更多的是程序员自己要用的时候直接用funcall定义的。
###7.5 lambda表达式
被函数式操作使用的函数有两种方法去定义。第一种方法就是使用defun定义一个函数，然后使用#’来引用，如我们之前做的。第二种方法是将定义直接输入进去，这种方法是用一种列表，叫做lambda表达式来实现的。
>######(lambda (n) (* n n))

既然lambda表达式是函数，那么他就可以使用#‘符号直接传输给mapcar。这个可以省去了在调用maocar之前的函数独立定义。
>######> (mapcar #’(lambda (n) (* n n))  ’(1 2 3 4 5))
>######(1 4 9 16 25)

lambda表达式和defun函数看上去很相似，除了没有函数名之外。lambda所在的地方就是defun的位置。但是lambda表达式实际上是不具名函数。lambda不是宏函数或者必须被求值的特殊函数，就像defun一样。他是一个制造器，将列表表现为函数。
lambda表达式在将双输入函数综合成单输入函数上特别有用。例如，假设我们想要给列表的每一个元素乘上10.我们也许会这样尝试。
>######(mapcar #’* ’(1 2 3 4 5))

但是本该有的10到哪里去了？函数*需要两个输入，但是mapcar只接受一个。正确的方法是写一个接受单输入lambda表达式来给输入乘以10.然后再将lambda表达式传输到mapcar。
>######> (mapcar #’(lambda (n) (* n 10)) ’(1 2 3 4 5))
>######(10 20 30 40 50)

下面是另一个使用lambda表达式的mapcar例子。我们会在每一个名字前面加上元素Hi There。
>######> (mapcar #’(lambda (x) (list ’hi ’there x))
>######’(joe fred wanda))
>######((HI THERE JOE) (HI THERE FRED) (HI THERE WANDA))

如果你在顶层提示符键入lambda表达式，你得到的结果根据特定Lisp实现的不同不同，也许你看到的会是下面这样子。
>######> (lambda (n) (* n 10)) Don’t forget to quote it!
>######Error: Undefined function LAMBDA.
>######> #’(lambda (n) (* n 10))
>######(LAMBDA (N) (* N 10))
>######> #’(lambda (n) (* n 10))
>#######<Interpreted-function 3515162>
>######> #’(lambda (n) (* n 10))
>#######<Lexical-closure {7142156}>

虽然本书中一般返回的是一个语法闭包’（Lambda 。。。）表达式。等到进阶话题我们再讨论。

###7.6 find-if操作
find-if是另一个函数式操作，如果你给find-if一个断言和一个列表作为输入的话，会发现断言判断为真的第一个元素会被返回（任何非nil的值）。
>######> (find-if #’oddp ’(2 4 6 7 8 9))
>######7
>######> (find-if #’(lambda (x) (> x 3))
>######’(2 4 6 7 8 9))
>######4

下面是具体的图形表示：
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1eleztqrq1aj208b08c747.jpg)
如果没有元素复合断言，find-if会返回nil。
>######(find-if #’oddp ’(2 4 6 8)) → nil

###7.7 用find-if写assoc
ASSOC searches for a table entry with a specified key.  We can write a simple
version of ASSOC that uses FIND-IF to search the table.
assoc是在表格入口里面搜索一个满足条件的key。我们可以写一个简单的版本的assoc来使用find-if搜索列表。
>######(defun my-assoc (key table)
>######(find-if #’(lambda (entry)
>######(equal key (first entry)))
>######table))
>######(my-assoc ’two words) → (TWO DEUX)

lambda表达式（实际上是语法闭包）被my-assoc传输到find-if中，使用表格入口（one un）作为输入。如果第一个元素和kay相同那么就会返回入口。find-if函数调用了表格中每一个入口额闭包，知道找到一个是的闭包返回T。
请注意表达式（equal key （first entry））出现在lambda表达哈斯的函数体中，并且指向两个变量。entry是lambda的本地变量，但是key不是。key是assoc的本地变量。这体现了lambda表达式很重要的一点，在lambda表达式的函数体中，我们不仅可以指向他自己的本地变量，还可以指向任何包括了lambda表达式的函数的本地变量。

###7.8 remove-if和remove-if-not
remove-if是另一个使用断言作为输入的函数式操作。remove-if从一个列表中删除所有满足断言的对象，并返回余下的元素的列表。
>######> (remove-if #’numberp ’(2 for 1 sale))
>######(FOR SALE)
>######> (remove-if #’oddp ’(1 2 3 4 5 6 7))
>######(2 4 6)

下面是一个图形表示：
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1elezuo3qn6j208408274a.jpg)
假设我们想要找到一个数字列表中的所有正数。断言plusp测试一个数字是不是大于0.然后反转结果来删除小于0的数字，最后留下正数元素。
>######> (remove-if #’(lambda (x) (not (plusp x)))
>######’(2 0 -4 6 -8 10))
>######(2 6 10)

remove-if-not比remove-if使用的更加频繁。他的行为就像remove-if一样，除了他不会自动反转断言的结果之外。这表示他只会删除断言返回为nil的元素。所以如果玄色plusp作为断言，remove-if-not将会返回所有正数。
>######> (remove-if-not #’plusp ’(2 0 -4 6 -8 10))
>######(2 6 10)
>######> (remove-if-not #’oddp ’(2 0 -4 6 -8 10))
>######NIL

还有另外一些关于remove-if-not的例子：
>######> (remove-if-not #’(lambda (x) (> x 3))
>######’(2 4 6 8 4 2 1))
>######(4 6 8 4)
>######> (remove-if-not #’numberp
>######’(3 apples 4 pears and 2 little plums))
>######(3 4 2)
>######> (remove-if-not #’symbolp
>######’(3 apples 4 pears and 2 little plums))
>######(APPLES PEARS AND LITTLE PLUMS)

有一个函数count-zeros，会数数字列表中有多少个0。输出所有0作为子集。
>######(remove-if-not #’zerop ’(34 0 0 95 0)) → (0 0 0)
>######(defun count-zeros (x)
>######(length (remove-if-not #’zerop x)))
>######(count-zeros ’(34 0 0 95 0)) → 3
>######(count-zeros ’(1 0 63 0 38)) → 2
>######(count-zeros ’(0 0 0 0 0)) → 5
>######(count-zeros ’(1 2 3 4 5)) → 0

###7.9 reduce操作
reduce是一个用来逐个元素计算到一个元素中的函数式操作。reduce接受一个函数和一个列表作为参数输入，单数不像其他我们见到的操作，reduce接受的函数必须是双输入的。例如：把列表中的数字相加。
>######(reduce #’+ ’(1 2 3)) → 6
>######(reduce #’+ ’(10 9 8 7 6)) → 40
>######(reduce #’+ ’(5)) → 5
>######(reduce #’+ nil) → 0

相似的，为了把一串数字相乘，我们使用*函数来作为reduce的输入。
>######(reduce #’* ’(2 4 5)) → 40
>######(reduce #’* ’(3 4 0 7)) → 0
>######(reduce #’* ’(8)) → 8

我们也可以将reduce应用在列表的列表上，吧一个表格编程一个一层的列表，可以不断使用append来实现：
>######> (reduce #’append
>######’((one un) (two deux) (three trois)))
>######(ONE UN TWO DEUX THREE TROIS)

下面是一个图形的演示：
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1elezuo3qn6j208408274a.jpg)
###7.10 every
every接受一个函数和一个列表作为输入，检测列表中的元素，都符合断言返回T，有一个不符合就返回nil。
>######> (every #’numberp ’(1 2 3 4 5))
>######T
>######> (every #’numberp ’(1 2 A B C 5))
>######NIL
>######> (every #’(lambda (x) (> x 0)) ’(1 2 3 4 5))
>######T
>######> (every #’(lambda (x) (> x 0)) ’(1 2 3 -4 5))
>######NIL

如果every被调用的时候，nil作为第二个参数，他会返回T，最简单的空列表自然没有元素不满足条件了。
>######> (every #’oddp nil)
>######T
>######> (every #’evenp nil)
>######T

every也可以操作多个列表，只要给出一个支持多个输入的断言。
>######> (every #’> ’(10 20 30 40) ’(1 5 11 23))
>######T

10是比1大的，20大于5,30 大于11,40 大于23，所以every返回T
###小结
函数式操作是将韩式应用在另一个函数的数据结构。有很多函数式操作，其中一些是内建在Lisp中的。高级的Lisp程序员会创造自己的操作。
mapcar会把函数应用到列表中的每一个元素，然后返回一个结果的列表。find-if搜索一个列表并且返回符合断言的第一个元素。remove-if删除列表中所有符合断言的元素，所以结果中只剩下了不符合断言的元素。remove-if-not比remove-if的使用频率要高，他返回的元素师符合断言的，不符合都被删除了。every只在每一个元素都符合断言的时候返回T，reduce将列表中的元素一个个整合成一个输出。
###本章涉及函数
函数式操作: MAPCAR, FIND-IF, REMOVE-IF, REMOVE-IF-NOT, REDUCE, EVERY.
###Lisp Toolkit: TRACE和DTRACE
trace宏被用在观察特定函数的调用时刻和返回时刻。每一个调用的时候都会看到函数的参数，当函数返回的时候你会看到返回值。每一个Lisp实现都有自己的风格来展现追踪信息，下面是一个典型的例子：
>######(defun half (n) (* n 0.5))
>######(defun average (x y)
>######(+ (half x) (half y)))
>######> (trace half average)
>######(HALF AVERAGE)
>######> (average 3 7)
>######0: (AVERAGE 3 7)
>######1: (HALF 3)
>######1: returned 1.5
>######1: (HALF 7)
>######1: returned 3.5
>######0: returned 5.0
>######5.0

如果你调用trace的时候没有加上参数的话，那么就会返回现在正在追踪的函数。
>######> (trace)
>######(HALF AVERAGE)

untrace宏是关闭trace的追踪功能的，可以关闭一个或者多个。既然untrace和trace一样是宏函数，纳闷它的参数也是不需要加引号的。
>######> (untrace HALF)
>######(HALF)

如果不加任何参数的调用untrace，untrace就会对所有正在追踪的参数进行解除。
>######> (untrace)
>######(AVERAGE)

在本书接下来的内容中，我们会使用更加细致的追踪格式来展现每一个在参数列表里的变量，他们的绑定的值：
>######> (average 3 7)
>######----Enter AVERAGE
>######| X = 3
>######| Y = 7
>######| ----Enter HALF
>######| | N = 3
>######| \--HALF returned 1.5
>######| ----Enter HALF
>######| | N = 7
>######| \--HALF returned 3.5
>######\--AVERAGE returned 5.0
>######5.0

如果你的Lisp的trace没有这么酷炫，不要紧张，你可以用和我一样的。这个工具叫做dtrace，在本书的最后给出了完整的程序。这个追踪的风格是对追踪函数的输入特别有帮助，甚至函数的输入是很长，或者嵌套，列表。
>######(defun add-to-end (x y)
>######(append x (list y)))
>######(defun repeat-first (phrase)
>######(add-to-end phrase (first phrase)))
>######> (dtrace add-to-end repeat-first)
>######(ADD-TO-END REPEAT-FIRST)
>######> (repeat-first ’(for whom the bell tolls))
>######----Enter REPEAT-FIRST
>######| PHRASE = (FOR WHOM THE BELL TOLLS)
>######| ----Enter ADD-TO-END
>######| | X = (FOR WHOM THE BELL TOLLS)
>######| | Y = FOR
>######| \--ADD-TO-END returned
>######| (FOR WHOM THE BELL TOLLS FOR)
>######\--REPEAT-FIRST returned
>######(FOR WHOM THE BELL TOLLS FOR)
>######(FOR WHOM THE BELL TOLLS FOR)

duntrace是用来抵消dtrace的效果的。不要尝试同事trace一个函数有dtrace一个函数，你会得到很奇怪的结果。
我们可以使用dtrace来观察函数式操作的行为，就像find-if。我们将会traceoddp函数和使用oddp作为find-if的输入。
>######(defun find-first-odd (x)
>######(find-if #’oddp x))
>######> (dtrace find-first-odd oddp)
>######(FIND-FIRST-ODD ODDP)
>######> (find-first-odd ’(2 4 6 7 8))
>######----Enter FIND-FIRST-ODD
>######| X = (2 4 6 7 8)
>######| ----Enter ODDP
>######| | NUMBER = 2
>######| \--ODDP returned NIL
>######| ----Enter ODDP
>######| | NUMBER = 4
>######| \--ODDP returned NIL
>######| ----Enter ODDP
>######| | NUMBER = 6
>######| \--ODDP returned NIL
>######| ----Enter ODDP
>######| | NUMBER = 7
>######| \--ODDP returned T
>######\--FIND-FIRST-ODD returned 7
>######7

接下来说最后一点关于trace和dtracede的使用，虽然他们可能被使用在trace内建函数，比如oddp，这个有时候会产生危险。要避免追踪最基础的内建函数，比如eval，cons和+。否则你的Lisp可能会陷入无限循环中，你也不得不重启程序。
###第七章进阶话题
###7.11 操作多个列表
在本章一开始我么会使用了mapcar来讲一个单输入函数应用到了列表的元素中。然而mapcar并没有被局限在单输入函数。例如，给出一个人的列表和一个工作的列表，我们可以使用mapcar来将一个双输入函数实现为每一个人一个工作。
>######> (mapcar #’(lambda (x y) (list x ’gets y))
>######’(fred wilma george diane)
>######’(job1 job2 job3 job4))
>######((FRED GETS JOB1)
>######(WILMA GETS JOB2)
>######(GEORGE GETS JOB3)
>######(DIANE GETS JOB4))

mapcar平行地使用两个列表，一步一步接受每一个元素。如果一个列表比另一个要短的话，mapcar会在短的那个到头的时候停止。
另一个操作多列表的例子就是将两个列表对应相加的问题：
>######> (mapcar #’+ ’(1 2 3 4 5) ’(60 70 80 90 100))
>######(61 72 83 94 105)
>######> (mapcar #’+ ’(1 2 3) ’(10 20 30 40 50))
>######(11 22 33)

###7.12 function特殊函数
单引号‘是quote特殊函数的缩写。#’是特殊函数function的缩写。当写下#‘cons的时候和写下（function cons）是等同的。
quote函数总是返回不求值的参数，但是function函数的工作方式有点不一样，function返回的是他的未求值参数的函数性解释。如果参数是符号，一般是返回符号的函数单元的内容，一般是一个编译代码对象。
>######> ’cons
>######CONS
>######> #’cons
>#######<Compiled-function CONS 6041410>

换句话说，如果function的参数是一个lambda表达式，结果通常是一个语法闭包。
>######> #’(lambda (x) (+ x 2))
>#######<Lexical-closure 3471524>

function返回的结果总是某种函数对象，这些对象都是一种数据的格式，吉祥字符和列表一样。例如，我们可以将他们存储在变量中。我们也使用funcall或者apply来可以调用他们（apply在进阶话题3.21中讨论过）
>######> (setf g #’(lambda (x) (* x 10)))
>#######<Lexical-closure 41653824>
>######> (funcall g 12)
>######120

变量G的值是一个语法闭包，也就是一个函数。但是G本身不是任何函数的名字。如果我们写下（G 12）,会得到一个函数未定义错误。

###7.13 函数式操作中的关键字参数
一些函数式操作，比如FIND-IF, REMOVE-IF, REMOVE-IFNOT,
还有REDUCE，接受可选的关键字参数。例如，这个：from-end关键字，如果给出一个非nil的值，会导致列表被从右向左处理。
>######> (find-if #’oddp ’(2 3 4 5 6)) Find the first odd number.
>######3
>######> (find-if #’oddp ’(2 3 4 5 6) Find the last odd number.
>######:from-end t)
>######5

关键字:FROM-end市委reduce特别定制的，他会使得元素从右到左的一个个处理，而不是默认的从左到右。
>######> (reduce #’cons ’(a b c d e))
>######((((A . B) . C) . D) . E)
>######> (reduce #’cons ’(a b c d e) :from-end t)
>######(A B C D . E)

REMOVE-IF 和 REMOVE-IF-NOT也接受关键字：count作为参数来定义删除元素的最大个数。请查看你的lisp实现的用户手册或者在线文档来了解哪些关键字参数是被特定函数所接受的。mapcar和every不接受任何关键字参数，他们接受的是一个列表的变量数字。 
###7.14 作用域和语法闭包
回顾7.7小结的my-assoc那个例子，既然lambda表达式是在被传递给find-if并且是在findif函数体内被调用，怎么可能对于他来说是指向一个my-assoc的本地变量呢?为什么不能看到本地变量？
>######(defun my-assoc (key table)
>######(find-if #’(lambda (entry)
>######(equal key (first entry)))
>######table))
>######(my-assoc ’two words) Þ (TWO DEUX)

首先要记住的是产地给find-if的不是原来的lambda表达式，而是一个由function创造的语法闭包。这个闭包记忆住了她的语法环境。在下面的求值回溯图中，一个空的箭头展现了必报的作用域绑定，一个圆弧连接这个箭头指向这个的上层区域，也就是my-assoc的函数体。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1elekvbl6hhj20m00m9go6.jpg)
###7.15 写一个函数式操作
使用funcall，我们可以写出我们自己的以一个函数为输入的函数式操作。我们的操作将会调用inalienable-rights。他会将输入应用到特殊的列表。我们从美国独立宣言里引用一段。
>######(defun inalienable-rights (fn)
>######(funcall fn
>######’(life liberty and the pursuit of happiness)))
>######> (inalienable-rights #’length)
>######7
>######> (inalienable-rights #’reverse)
>######(HAPPINESS OF PURSUIT THE AND LIBERTY LIFE)
>######> (inalienable-rights #’first)
>######LIFE
>######> (inalienable-rights #’rest)
>######(LIBERTY AND THE PURSUIT OF HAPPINESS)

如果调用inalienable-rights的不是函数的话会引发错误，因为funcall需要第一个输入是函数。
>######> (inalienable-rights 5)
>######Error! 5 is not a function

inalienable-rights的输入必须是一个以单列表作为输入的函数，我们不能使用cons函数作为输入，因为cons需要两个参数。
>######> (inalienable-rights #’cons)
>######Error! CONS requires two inputs, but only got one

然而，我们可以在lambda表达式内部使用cons来接受一个参数。
>######> (inalienable-rights
>#######’(lambda (x) (cons ’high x)))
>######(HIGH LIFE LIBERTY AND THE PURSUIT OF HAPPINESS)

###7.16 制造函数的函数
写一个值是另一个函数的函数式完全可能的，假设我们想要去制造一个函数，如果输入大一一个指定的数字n就返回T。我们可以通过构造指向n的lambda表达式来制造函数，并且返回那个lambda表达式。
>######(defun make-greater-than-predicate (n)
>#######’(lambda (x) (> x n)))

MAKE-GREATER-THAN-PREDICATE的返回值是一个语法闭包。我们可以将这个值存储在其他地方，或者传给funcall做参数，或者任何函数式操作。
>######> (setf pred (make-greater-than-predicate 3))
>#######<Lexical-closure 7315225>
>######(funcall pred 2) -> nil
>######(funcall pred 5) -> t
>######(find-if pred ’(2 3 4 5 6 7 8 9)) -> 4

###本章涉及函数
引用函数的特殊函数：function
