#第三章 EVAL标记法
### 3.1 导引
在进一步深入学习Lisp之前，我们必须切换到一个更加适合的标记法，EVAL标记法。不再使用矩形来表示函数，而是使用列表。矩形图示是很容易理解，但是EVAL表示法有以下几个优点：
- 一些复杂到不能用矩形图示来表示的编程概念，可以用EVAL表示来表达。
- EVAL图示是很方便就可以够用键盘敲击来输入的，盒形图示就不行。
- 从数学的角度来看，使用一般列表来表示函数是非常优雅的，因为我们可以确定使用同一种表达式来表示函数和数据。
- 在Lisp中，数据就是函数，EVAL表示可以允许我们定义函数来接受其它函数作为输入。我们将在第7章介绍这个特性。
- 当你精通了EVAL表达式，那也就是掌握了用Lisp和电脑交流的方式。

###3.2 EVAL函数
EVAL函数是Lisp的核心。主要的作用就是求Lisp表达式的数值并且计算结果。大部分表达式是由一个函数和一些输入组成的，假如给EVAL函数输入（+ 2 3），他就会调用内建函数+来处理输入2和3.并且+函数返回数值5，因此我们会说，表达式（+ 2 3）的值是5。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el7mjmoa2xj208102x0sj.jpg)
从现在开始，我们就不再画一个EVAL函数的矩形了，而是使用一个箭头来表示。
 >(+ 2 3) → 5

如果使用两个箭头的话，会显得稍稍冗长一些。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el7mm8exm5j206603b0sj.jpg)
想要展现更多细节的话，就会使用三箭头的图形表示。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el7mncn3obj20cw0423yi.jpg)
细线和粗线代表的意思等一会儿再解释，我们先来看一些EVAL表达式的例子。
>(+ 1 6) → 7
>(oddp (+ 1 6)) → t
>(* 3 (+ 1 6)) → 21
>(/ (* 2 11) (+ 1 6)) → 22/7

###3.3 EVAL表达式可以做到矩形表达式所做的任何事
很明显，矩形表达式能够表示的表达式，那么EVAL表达式也能够做到。
比如下面的表达式就可以写成
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el7mqmssfaj20m40bswel.jpg)
类似的EVAL表达式也可以写成矩形表达
>(* 3 (+ 5 6))
>(not (equal 5 6))

![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el7mtuwpbuj20kx0763yi.jpg)
你可能注意到了，矩形表达式和EVAL表达式读起来正好是相反的顺序，矩形表达式的顺序应该是，5，6，EQUAL，NOT。相对应的EVAL表达式的读法是NOT，EQUAL，5，6.在矩形表达式中，计算式从左向右的计算顺序。在EVAL表达式中一个函数的输入时从左向右这么被处理的。但是对于嵌套结构的表达式来说，求数值的真正过程是从最深处的表达式开始从内向外的计算顺序，使得函数调用的顺序是从右向左的。

###3.4 求值规则定义EVAL函数的行为
EVAL函数的运行是伴随一系列求值规则的。其中一个规则就是数字和其他确定的对象是自求值的，意思就是自己求自己的值。特殊字符串T和NIL也是自求值的。
>23 → 23
>t → t
>nil → nil

数字，T，NIL的求值规则：数字，字符串T，NIL是自求值的。
也有规则是针对列表的。列表的第一个元素将会定义一个待调用的函数；剩余的元素是函数的非求值参数。这些元素必须是被求值，从左到右的顺序来决定函数的输入。例如，求值表达式（ODDP （+ 1 6）），首先要做的就是求值ODDP函数的参数，列表（+ 1 6）。为了实现求值这个参数，我们开始求值函数+的参数。1求值得1,6求值得6,。现在我们可以调用函数+使用这些输入得出结果7，7就是作为函数ODDP的输入，然后返回结果T。
列表的求值规则：列表的第一个元素就定义了一个待调用的函数，余下的元素就是这个函数的参数。调用函数操作这些已求值的参数。
以下图示是称作求值回溯图（evaltrace diagram），展示了列表（ODDP （+ 1 6））是如何求值的。请注意求职过程是从内部嵌套的表达式（+ 1 6）开始，到外层表达式，ODDP函数结束。这种由内而外的性质是由求值回溯图的形状来反映。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el7mytn74cj20ex0b1aak.jpg)
这里有另一个在函数调用之前，参数被求值的例子，表达式（EQUAL （+ 7 5） （* 2 8））的求值回溯图。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el7mzx6etuj20gy0gggmn.jpg)
###练习题
3.1 列表（NOT （EQUAL 3 （ABS  -3）））的求值结果是？
3.2 用EVAL表达式来写一个表达式，8加上12再除以2.
3.3 一个数的平方可以让这个数乘以自己来求得，写一个EVAL表达式来求3的平方加上4的平方。
3.4 画出下列EVAL表达式的求值回溯图。
>(- 8 2)
>(not (oddp 4))
>(> (* 2 5) 9)
>(not (equal 5 (+ 1 4)))

###3.5 用EVAL表达式定义函数
在矩形表达式中，我们通过表现内部的具体细节来定义一个函数。函数的输入被描绘成为箭头，在EVAL表达式中，我们使用列表来定义函数，通过变量名字来指向函数的参数。我们可以命名矩形表达式的输入通过在箭头上写上名字的方式。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el83tliuvpj20ob0fhjrv.jpg)

以EVAL表达式来定义AVERAGE函数
>(defun average (x y)
>       (/ (+ x y) 2.0))

DEFUN函数是一类特殊的函数，名字叫做宏函数（macro function），规定他的参数并不是要求值的，所以宏函数不是一定要背引用的。DEFUN函数的作用是用来定义其它函数。第二个元素是参数列表，也就是定义将会用到的参数的名字。剩下的元素就是DEFUN函数定义的函数体（body），函数的内部细节。另外DEFUN这个名字是define的缩写。
只要在计算机中定义函数AVERAGE一次，就可以使用EVAL表达式来调用AVERAGE函数。比如这样调用，（AVERAGE 6 8），AVERAGE函数就会使用6作为X的值，8作为Y的值来进行计算，最后得出结果7。
这里还有一个defun定义函数的例子
>(defun square (n) (* n n))

函数名叫做square，参数列表是（n），也就是接受一个指向n的参数。函数提示表达式（* n n），理解这个定义的正确方式是定义一个n的square的函数，返回n的平方。
除了特殊字符串T和NIL之外，几乎所有的字符串都可以作为函数的参数名使用，x，y，n一般是最常用的，但是bozo，artichoke等等也是可以的。一个计算贸易成本的函数可能给函数参数的命名就是quantity，price，和handling-charge。
>(defun total-cost (quantity price handling-charge)
>(+ (* quantity price) handling-charge))

### 3.6 变量（variables）
变量（variable）是数据存储的地方。我们再来看average函数。当我们调用average函数，Lisp创造了两个变量来存储输入，之后函数体中的表达式会根据参数名来指向这个变量。这些变量的名字就是x和y。很重要的一点就是辨析清楚变量和字符串之间的区别。变量不是字符串，变量是通过字符串来命名的，函数也是通过字符串命名。
变量的值就是所存储的数据。当我们对列表（average 3 7）求值的时候，Lisp创造出变量x和y，并且给他们分别赋值，3和7.在average函数的函数体中，字符串x指向第一个变量，字符串y指向第二个变量。这些变量也只能在函数体内部被引用，在average函数外部就不能引用了。当然字符串x和y在函数外部仍然存在，但是与在函数内部的意思已经完全不同，下面的求值回溯图很好地说明了计算结果的过程：
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el84chvnpnj20h10hi75d.jpg)

术语“变量“的使用是计算机编程中特有的，在数学里，一个变量的意思是一个未知数据的意思，不是一个计算机内存中的数据。但是这两个方面的意思也并不是完全对立，一个函数的输入在函数定义的时候也的确是未知的一个数据。

现在，我们可以解释求值回溯图中粗箭头和细箭头的意思了。细箭头是用表达式的值连接起来的。一个粗箭头是被用在战士进入以一个函数内部的函数体和从函数体中出来，得到结果的过程。在粗箭头包括的范围内，我们见到的是函数内部的运行情况。咋average函数的函数体中，变量被创造，表达式被求值。对于像+或者/这类函数，因为他们是原始函数，所以我们没什么机会用粗箭头来看他们内部的情况。对于用于定义的函数average，我们可以由一个细箭头开始表示表达式函数调用，然后中间表示粗箭头来展现函数体。下面用抽象语法来展现这种表达方式：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el84e2rvktj20mc092js5.jpg)

求值回溯表达式是十分灵活的：如果需要可以隐去不必要的细节，比如不展示函数体。另一个简化求职回溯过程的方法是不展示数字的求值，既然数字都是自求值的。有时候也省略字符串的求值。下图是ONEMOREP函数的一般简要格式的回溯图:
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el84f9njfcj20hk0bgdgi.jpg)
### 3.7 对字符串求值
一个函数给自己参数使用的名字和其它函数使用的参数名字是互相独立的。两个函数，比如half和square函数都可以叫他们的参数n，但是在函数half中的n只是指向half函数的输入，与square函数中的n没有任何关系。
EVAL表示法中对于字符串的规则是很简单的：对于字符串的求值规则：一个字符串的值就是变量指向的数据。
在函数half和square的函数体之外，字符处啊n指向的就是全局变量（global variable）n。全局变量就是和任何函数都没有关系的变量。PI作为Common Lisp的内建变量就是一个全局变量的例子。
>pi → 3.14159



Lisp程序员有时候非正式讨论变量求值的时候，他们会说变量求他们的值，真正的意思是一个字符串求他所指向的变量的值，既然有许许多多变量都被称作n，那么n指向甚么值就看变量n在什么地方出现了。假如出现是在square函数的函数体内部，那么得到的变量就是函数square的输入，假如出现在函数体外部，那么得到的就是全局变量n。
假如你求得变量还没有被赋值，Lisp就会报错，变量未赋值错误（unassigned variable error）。比如，在内建变量中，并没有一个佳作eggplant的变量。那对字符串eggplant的求值就会报变量未赋值错误。当然，除非在某函数的内部对这个字符串求值，指向某一个输入。
>eggplant → Error! EGGPLANT unassigned variable.

Common Lisp中也没有叫做n的内建变量，所以在函数half和square的函数体外部求n的值也是会报同样的错误。
###3.8 将字符串和列表作数据使用
假设我们想要调用函数equal，并且将字符串kirk和spock作为输入。在矩形表达式中这是很方便的，因为字符串和列表总是被当做数据看待。但是在EVAL表达式中，字符串是被看做具名变量的，所以假如我们写成这样。
>(equal kirk spock)

Lisp将会认为我们尝试比较的是两个名字叫kirk和spock的全局变量的值。既然给不出这样两个变量的值，那就会报错。
>(equal kirk spock) → Error! KIRK unassigned variable.


我们真正想要比较的不是变量的值，而是量给字符串本身，我们告诉Lisp，这个两个字符串不是变量的引用而是作为数据看待，只要在字符串前面加上一个单引号就好了。
>(equal ’kirk ’spock) → nil

因为字符串T和NIL都是被求值为自己，所以不需要用单引号来标记他们，其他字符串需要这么标记。
>(list ’james t ’kirk) → (james t kirk)

一个字符串是否在函数定义中被看做数据，或者在函数输入中被略过，为了防止被求值，就必须加上前面的单引号。
>(defun riddle (x y)
>(list ’why ’is ’a x ’like ’a y))
>
>(riddle ’raven ’writing-desk) → (why is a raven like a writing-desk)

列表在被当做数据看的时候也必须加引号，不然就会被Lisp求值，一个典型的结果就是报错，未定义函数错误（undefined function）。
>(first (we hold these truths)) → Error! WE undefined function.
>(first ’(we hold these truths)) → we

对引用对象的求值规则：一个被引用的对象求值为自己本身。
这里有一些加引号和没加引号的列表，之间的区别很明显
>(third (my aunt mary)) → Error! MY undefined function.
>(third ’(my aunt mary)) → mary
>(+ 1 2) → 3
>’(+ 1 2) → (+ 1 2)
>(oddp (+ 1 2)) → t
>(oddp ’(+ 1 2)) → Error! Wrong type input to ODDP.


最后一个例子出错的原因是oddp被调用的时候，列表（+ 1 2）的值需要作为输入。引号的出现使得列表的求值被绕过，oddp接收到的是列表而不是数字，oddp的输入必须是数字，不能是列表。
现在我们来看一下有引号存在的求值回溯过程
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el84qaog43j20o30a0gmw.jpg)
###3.9 错误引用问题
对于Lisp程序员来说，很容易就对引号感到困惑，而且引号的使用有时候不是放错地方就是在需要的地方少用了引号。Lisp提供的错误信息有时候是很好的关于错误的提示。一个未赋值变量错误或者一个未定义函数错误通常都显示是一个引号被遗漏的原因。
>(list ’a ’b c) → Error! C unassigned variable.
>(list ’a ’b ’c) → (a b c)
>(cons ’a (b c)) → Error! B undefined function.
>(cons ’a ’(b c)) → (a b c)

另一方面来说，输入类型错误或者意想不到的输出结果也可能是一个引号放错了位置的表示。
>(+ 10 ’(- 5 2)) → Error! Wrong type input to +.
>(+ 10 (- 5 2)) → 13
>(list ’buy ’(* 27 34) ’bagels) → (buy (* 27 34) bagels)
>(list ’buy (* 27 34) ’bagels) → (buy 918 bagels)


给一个列表加上引号，放在列表外面的目的是为了防止列表被求值。如果引号被放在列表里面的话，EVAL表达式就会尝试对列表进行求值，结果就是错误的。
>(’foo ’bar ’baz) → Error! ’FOO undefined function.
>’(foo bar baz) → (foo bar baz)

###3.10 构造列表的三种方式
有三种方式来通过EVAL表达式来构造列表。可以吧列表直接写出来，然后前加引号防止被求值
>’(foo bar baz) → (foo bar baz)

或者我们可以使用list和cons函数来为独立元素构造一个列表，假如我们使用这个方法，我们需要把每一个元素都前加引号。
>(list ’foo ’bar ’baz) → (foo bar baz)
>(cons ’foo ’(bar baz)) → (foo bar baz)


使用这种方法的一个好处就是，在构成列表的元素汇总，可以有一些是被当时计算出来的而不是直接定义的。
>(list 33 ’squared ’is (* 33 33)) → (33 squared is 1089)

如果整个列表是被直接引用的话，那么列表内部的所有元素都不会被求值。
>’(33 squared is (* 33 33)) → (33 squared is (* 33 33))

我们也已经见过一些引号没有被正确使用的情况
>(list foo bar baz) → Error! FOO unassigned variable.
>(foo bar baz) → Error! FOO undefined function.
>(’foo ’bar ’baz) → Error! ’FOO undefined function.

###3.11 错误定义一个函数的四种方式
对于初学者来说，用EVAL表达式来定义函数，在正确处理语句结构上会有一些问题。让我们来看看这个问题，以函数intro的定义为例：
>(defun intro (x y) (list x ’this ’is y))
>(intro ’stanley ’livingstone) → (stanley this is livingstone)

请注意，intro函数的参数列表是由两个字符串组成，x和y，既没有引号也没有圆括号，在函数体中的变量x，y也是，没有引号和圆括号。
第一种错误定义函数的方式就是，参数列表中的字符串被加上了其他东西。如果参数列表中的参数被加上了引号或者括号，那么函数就不会正常工作。初学者常常尝试去给参数列表加上一些东西，想要让列表还取代字符串作为输入，这是第一个错误。
>(defun intro (’x ’y) :bad argument list
>(list x ’this ’is y))
>(defun intro ((x) (y)) :bad argument list
>(list x ’this ’is y))

第二种错误定义的方式是，在本不该出现括号的函数体里出现了多余的括号只有函数调用会有括号包围，在变量上包围括号将会引发函数定义错误。
>(defun intro (x y) (list (x) ’this ’is (y)))
>(intro ’stanley ’livingstone) → Error! X undefined function.

第三种错误定义的方式是给一个变量加上引号。字符串必须没有引号才会被认为是指向变量的。这里有一些例子来指出给变量加上引号会有什么事情发生。
>(defun intro (x y) (list ’x ’this ’is ’y))
>(intro ’stanley ’livingstone) → (x this is y)

The fourth way to misdefine a function is to not quote something that
should be quoted. In the INTRO function, the symbols X and Y are variables
but THIS and IS are not. If we don’t quote THIS and IS, an unassigned
variable error results.
第四种错误定义的方式是没有给应该加上引号的地方加上引号。在intro函数中，字符串x和y都是变量，但是this和is不是变量。加入我们没有给this和is加上引号，一个未赋值错误就会出现。
>(defun intro (x y) (list x this is y))
>(intro ’stanley ’livingstone) → Error! THIS unassigned variable.

### 3.12 关于变量的更多
在Lisp中，一个函数当被调用的时候会自动创建变量，一般来说，函数运行结束的时候会把变量销毁。来看一下double函数，每次被调用的时候都会创造一个叫做n的变量:
>(defun double (n) (* n 2))

在double函数的外部。字符串n指向全局变量n，全局变量n没有被赋值，所以对nde求值就会发生错误。
>n → Error! N unassigned variable.

假设我们对（double 3）求值，在double函数内部，字符串n指向一个新创建的变量，会绑定在输入上，并不是绑定全局变量n上下面的求值回溯图展现了这一过程。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el85p2cs7xj20eh099t93.jpg)
如果我们再一次调用double函数，例如（double 8），一个全新的变量n就会被创造出来并赋值8.在double函数外部，n指向全局变量n，没有被赋值的变量。现在我们来尝试举例有两个变量的例子，这里是函数quadruple，通过double函数定义。
>(defun quadruple (n) (double (double n)))

double函数和quadruple函数都会调用输入n，假设我们对表达式（quadruple 5）求值，如下面的图像表示。当我们进入函数quadruple背部，Lisp创造一个全新的变量n并且赋值5然后求值表达式（double （double 5））。当函数double的输入5的时候会发生什么？double函数创建了自己的变量n，绑定自己的输入5。double函数的函数体求值10，现在我们已经对（double n）求值了，锁我我们可以用这个结果来对（double （double n））求值，double函数被再一次调用，这一次的输入是10，所以另一个变量n被创造出来，并且绑定输入，10.然后求值（* n 2）表达式，之后double函数返回20，quadruple函数返回29作为结果，然后我们就就是并再一次回到了顶层，变量n指向全局变量的地方。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el862ux9wij20i00k0dhf.jpg)

###小结
在本章我们学习了EVAL表达式，使用将函数等用列表形式来表现的方法。Lisp根据一系列的求值规则来用EVAL表示法表示函数，有关求值规则，我们学习了：
- 数字是自求值的，求值也就等于自身，T和NIL也是如此。
- 在求值一个列表的时候，第一个元素被定义为一个函数来被调用，剩下的元素被定义为函数的参数，这些参数从左到右来求值，将输入导入传递给函数进行操作。
- 字符串在如果不是一个列表的第一个元素，那么就会被解释为指向变量。一个字符串将会求值为所命名的变量。准确的说，一个变量的值取决于字符串出现的位置。变量如果没有被赋值的话，一个未赋值错误就会出现。
- 一个加上引号的列表或者字符串会被求值为自身。
用列表的格式(defun 函数名 （参数列表） 函数体)来定义一个函数。defun函数是一个特殊的函数，他的输入将不会被引用，一个函数的参数列表就是参数字符串被赋予的输入值。在函数内部，变量所具有的值指向函数的输入。

######本章所学的函数
求值器：EVAL.
定义函数的宏函数： DEFUN.
###在电脑上运行Lisp
恭喜！你已经完成了所有纸笔上的学习过程，现在是学习如何在计算机上使用Lisp的时候了。很不幸的是，我不能给你更加详细的介绍l；有很多类型的计算机和更加多的Lisp实现。你可能想要多花一点时间来熟悉各种用户手册，一个更加有效率的方法是找一个熟悉你所有的机器的人来请教。
###3.13 运行Lisp
The first thing you need to find out is how to start up Lisp on your computer.
If you’re lucky you can just type "lisp" and hit the Return key, but you might
have to type something more complicated. When Lisp starts up it prints a
greeting message. Each implementation has its own style of greeting, but a
typical message looks something like this:
首先需要明确的事情是在你的计算机上如何运行Lisp。最好就是键入Lisp然后回车就好了，但是可能还需要更多更复杂的操作，当Lisp开始运行的时候或打印欢迎信息，每一个实现的欢迎信息都不相同，一半看起来是这样。
>CMU Common Lisp M2.8 (29-Mar-89)
>Hemlock M3.0 (29-Mar-89), Compiler M1.7 (29-Mar-89)
>Send bug reports and questions to Gripe.
>>

这个出现在欢迎信息里的>符号叫做顶层提示符（top-level prompt）。他表示Lisp现在正在等待你键入一些东西。一些Lisp实现使用了不同的额提示符，许多是用的星号*.
接下来你需要确定的事情是一些Lisp的控制字符：
- 如何删除一个字符:按下delete键，backspace键还是其他？
- 如何撤销一整行的输入，如果你不想要的话？在一些Lisp中你可以通过键入Control-U来实现这个功能（也就是按住ctrl键的同时按u）。
- 键入什么可以使得你回到顶层提示符？许多Lisp使用Control-G或者Control-C来实现。

当哦我们面对一些特殊的字符的时候，请注意，计算机总是将不同的想相似字符分的很清楚的，比如字母o和数字0，啊还有字母l和数字1。当对于阅读的时候这些相似的字符对阅读不会有什么障碍，但是输入计算机的话就一定要清楚。
最后，你需要了解如何退出Lisp。大部分Lisp实现要求箭如意想（quit）或者（exit）来离开程序。一些时候文件结束字符想control-d也会起作用。
###3.14 read-eval-print循环
一台运行Lisp的计算机很像一台便携式计算器，读入一个键入的表达式，计算他（使用EVAL函数），然后在屏幕上打印结果，随后另一个顶级提示符将会出现，等待下一个表达式的输入。这个过程被称作read-eval-print loop。
这里有一个计算机和我们的键入函数之间的简单对话。在这个例子中，键入的信息会在提示符>后面以小写出现；计算机的返回会用大写返回。不是所有Lisp实现都遵循这个惯例，但是大部分是的。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el89odd2azj20ui0a23zs.jpg)
###3.15 从错误中恢复
一件很重要的事情就是学会如何从错误中恢复。首先来考虑打字拼写错误，如果见入了很长的一段文字记过发现在开始的地方有错误，就会想要放弃整个一行表达式重新开始。Lisp中是现代额方法是control-G的输入，然后回到顶级提示符，这里是一个例子：
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el89wjmv4mj20u707mgm7.jpg)
一个更加普遍的问题是，表达式的拼写正确但是结果是一个计算错误。想要增加一个数字或者字符串是一半的做法。一个计算错误出现的时候，Lisp会打印错误信息，然后进入一个不同的循环。不同于上文提到的read-eval-print循环，我们现在称他为调试器（debugger）的read-eval-print 循环。我们将在第八章学习如何使用调试器，到现在为止，你需要知道的是如何才能离开调试器回到顶级提示符。在Lisp中，control-G就是这个脱出命令。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el8a3zk1jwj20u30asmy7.jpg)
如果你已经在Lisp中定义了一个函数但是却不能正常工作的话，那就重新定义一下然后再次尝试。你可以重新定义很多次，没有限制，只有最后一次定义会被保存和使用。接下来的例子就显示这个特性，也显示了你可以在一个表达式输入的时候，在任意地方回车都没有关系。这是因为表达式是列表。空格和缩进不会有影响。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el8abvhbxzj20tz0j040o.jpg)
请确认你没有使用想cons，+，或者list这类内建函数的名字，重新定义这些函数会出现比较严重的错误，你可能需要离开里搜谱然后重新开始，而且之前定义的任何函数将会被废弃。
###Lisp Tookit:ED
出现在这里Lisp Toolkit小节和后续的章节会介绍Lisp编程环境的一些重要工具。在这些工具中，比如语言定义编辑器（Language-specific editor），编程格式转换（program formatters），和代码层次的调试器（source-level debugger）都可以在其他编程语言中找到并且使用。但是他们是最先出现在Lisp中的。其他工具仍然是Lisp独有的，其中两个，sdraw和dtrace，是这本书独有的。源代码在最后的附录当中。
我们最先接触到的工具就是Lisp编辑器，由于common lisp标准中并没有规定lisp实现利一定要用哪一种编辑器，所以现在我们没有办法给出你编辑器的具体细节。但是大概上，能告诉你，为什么他们不同于一般的文本编辑器，为什么你应该花时间来学习使用你的lisp实现提供的编辑器。
>在lisp中最常见的额错误就是括号错误。因此几乎是一定要的就是在定义函数的时候每一次都检查括号的数量和配对。——Elaine Gord

上面的引言是写在25年前，那时候lisp程序员还是在打孔纸带上写程序。现在，当然我们使用交互式的编辑器。lisp编辑器不是普通的文本编辑器：编辑器“理解”lisp程序的语法。在我的机器上，无论什么时候我键入右括号，编辑器都会高亮左括号。这个功能防止了我在输入表达式的时候发生括号错误。另一个编辑器的工作就是自动缩进。如果一个函数占用多行，那么就会自动缩进来规范格式以增加可阅读。
Some of the earliest Lisp books were written before anyone thought of
systematically indenting programs to make them readable. A program that
would have been written this way back then:
一些早期的lisp书籍是在系统缩进这想法出现之前写就的，那时候的程序就是这样
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el8bcs3x2kj20nv03ngly.jpg)
现在自动缩进之后就会变成这样
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el8be271ojj20js05t74n.jpg)
还有两个好功能室lisp编辑器可以提供的，一个是在编辑表达式的时候就对表达式求值。你可以用鼠标点击几次定义的函数，在不离开编辑器的情况下就可以对表达式求值。第二个就是，对于在线文档的快速查看。假如我想要看任何lisp函数或者函数的文档，我可以用几次击键来唤出。编辑器本身支持在线文档。
Common Lisp标准定义了lisp实现和编辑器之间的接口。这个接口就是一个叫做ED的函数。在顶级提示符的状态键入（ED）将会进入编辑器。更多的lisp实现会提供更加快捷的方式，击键Control-E。ED也可以接受参数来进行操作。
###第三章 进阶话题
###3.16 没有参数的函数
Suppose we wanted to write a function that multiplies 85 by 97. Notice that
this function requires no inputs; it does its computation using only
prespecified constants. Since the function doesn’t take any inputs, when we
write its definition, it will have an empty argument list. The empty list, of
course, is NIL. Let’s define this function under the name TEST:
假设我们想要写一个函数把85和97相乘。请注意这个函数是没有输入的，他计算的只是之前已经有的常量。既然没有输入，那么参数列表就是空的。空列表当然也就是NIL，我们来定义这个叫做test的函数：
>(defun test () (* 85 97))

这之后，我们会看到
>(test) → 8245
>(test 1) → Error! Too many arguments.

test是一个函数，所以必须使用括号来调用，字符串test在没哟括号的情况下会被解释为变量的引用。
>test → Error! TEST unbound variable.

###3.17 特殊函数quote
quote函数是一个特殊函数，他并不对自己的输入进行求值，而是把输入简单的返回作为输出。
>(quote foo) → foo
>(quote (hello world)) → (hello world)

早期版本的lisp使用quote而不是单引号来表示不对一些对象求值，也就是本来写成这样的
>(cons ’up ’(down sideways))

在早期版本中写成这样的
>(cons (quote up) (quote (down sideways)))

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
###3.19 lambda 表达式
lambda表达式是由Alonzo Church，普林斯顿大学的数学家，创造出来的。Church想要一个清楚的方式去表示函数，输入还有计算。在lambda表达式中，一个在一个数字上加上3的函数如下所示，λ既是希腊字母lambda: λx.(3+x)
John McCarthy，lisp的创造者，是Church先生的学生。他通过定义函数来继承了lambda表达式。函数λx.(3+x)在lisp中的等价形式是：(lambda (x) (+ 3 x))
函数f(x,y) = 3x+y2的lambda表达式会被写成λ(x,y).(3x+y2)，他的lisp形式就是(lambda (x y) (+ (* 3 x) (* y y)))
如所见，lambda表达式的语法和lisp是十分相似的，甚至更像defunct函数，但是不想defun函数，lambda并不是一个函数，他更像一个想eval一样的额制造器，我们将在第七章学习更多关于lambda表达式的内容。
defun函数的工作室将函数名称和函数本身连接在一起。当键入一个新的函数定义，例如half。将会有两种命名过程，第一个是，字符串half命名字符串对象，之后字符串对象指向函数。在下图中，你可以看到字符串half指向字符串对象half。函数部分指向一个函数对象。准确的函数对象是什么样子取决于Common lisp实现的不同。但是正如下图所示，可能是一个lambda表达式在某处。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el8exol9l7j20sg0910t7.jpg)
当然，lambda表达式也只是内存单元构成的列表。每一个lambda表达式中的字符都是五个指针组成的，比如N和/，/的叫名字哦叫做除法函数，包括一个指针指向内建的除法函数来实现除法功能，所以间接地，half函数指向了内建的处罚函数，图1-3展现出了这些细节。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1el8f43jhl7j20mz0p9t9y.jpg)
###3.20 变量的作用域
变量的作用域是指变量能够被引用的区域范围，例如，变量n取得half函数的输入，作用域就是half函数的函数体范围。另一个方式去表达这个概念是说变量n是函数half的本地变量（local）。全局变量由不绑定的作用域，他们可已在任何地方被引用。
在求值回溯图中，本地变量的作用域被限制在粗箭头包含的创造变量范围内。在粗箭头之外的地方变量是不能被引用的、接下来的程序就是说这个。
>(defun parent (n)
>(child (+ n 2)))
>(defun child (p)
>(list n p))

程序是有错误的，我们来看看parent函数在创造一个本地变量n之后调用了child函数，问题在哪里呢？
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el8fvn7qo4j20i60e8aax.jpg)
在求值回溯图中，粗箭头描绘了作用域范围。parent函数的作用域的n只是在parent函数体中有效，在child函数的粗箭头范围内，是不能被引用的。所以n出现在child函数中被解释为一个全局变量，没有被赋值过。因此就会出现赋值错误。

###3.21 EVAL和APPLY
EVAL是一个lisp原始函数，每一次eval的使用都给出一层计算。
’(+ 2 2) → (+ 2 2)
(eval ’(+ 2 2)) → 4
’’’boing → ’’boing
(eval ’’’boing) → ’boing
(eval (eval ’’’boing) → boing
(eval (eval (eval ’’’boing))) → Error! BOING unassigned variable.
’(list ’* 9 6)) → (list ’* 9 6)
(eval ’(list ’* 9 6)) → (* 9 6)
(eval (eval ’(list ’* 9 6))) → 54
我们不会使用在任何程序中直接使用eval，但是会间接地一直使用eval。你可以想象计算机作为一台物理上的eval的表现形式。当lisp被启动，键入的每个项目都会被求值。
apply也是一个lisp原始函数，apply把一个函数或者一系列对象作为输入，他调用对应的函数使用这些对象作为输入。apply函数的第一个参数被加上#‘符号而不仅仅是加上引号’。#‘符号是一个合适的方法来把函数作为另一个函数输入的方法。这个将在第七章给出更多解释和细节。
(apply #’+ ’(2 3)) → 5
(apply #’equal ’(12 17)) → nil
The objects APPLY passes to the function are not evaluated first. In the
following example, the objects are a symbol and a list. Evaluating either the
symbol AS or the list (YOU LIKE IT) would cause an error.
传给apply的对象首先并不是被求值，在接下里的例子中，对象是一个字符串和一个列表，对字符串或者列表求值会产生错误。
(apply #’cons ’(as (you like it))) → (as you like it)
###进阶话题中的函数
EVAL相关函数：apply
特殊函数：quote
