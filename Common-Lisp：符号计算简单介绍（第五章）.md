#第五章 变量和副作用
###5.1 导语
本章让你对lisp程序中出现的各种类型的变量加深理解，变量如何被创造的和值是如何改变的。Common Lisp比早期版本的Lisp更加有进步。我们也会讨论副作用，那些函数除了返回值之外的操作，其实改变一个变量的值就是一种副作用。
###5.2 本地变量和全局变量
每一个变量都有作用域，也就是变量可以被引用的范围，到现在为止，我们见到过的变量都是出现在函数的参数列表中的。既然变量是被限定在函数体中的，那么他们就被成为本地变量，请看下面的例子：
>(defun double (n) (* n 2))

我们每一次调用double函数，一个新的本地变量n就会被创造出来。在double函数体的外部，这个变量是不能不能被引用的，因为已经超过了它的作用域。换句话说，变量n在double函数外部是有着不同的的意思的。
>(defun double (n) (* n 2))
>(double 5) → 10
>n → Error! N unassigned variable.

在错误信息中支出的未赋值变量n并不是我们在double函数中创造的变量n。他是另一个变量对任何特定函数来说都不是本地变量的变量。他是一个全局变量，因为全局变量是被初始化为没有值的（就是未绑定，在老的术语来说），在顶级提示符中键入n的时候，也是会得到一个未定义变量错误。如果我们看这（double 5）的求值回溯图，变量n的两种意义的分别就开始显现：
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1elayde8z9pj20ha0cjgmf.jpg)
只有一个全局变量n，但是有很多本地变量n也没有关系，因为他们在不同的语言上下文里。
###5.3 setf给一个变量赋值
宏函数setf给变量赋值。如果变量已经有一个值，那么新的值就会覆盖老的值。这历史一个setf给全局变量赋值的例子：
>######> vowels VOWELS initially has no value.
Error: VOWELS unassigned variable.
>######> (setf vowels ’(a e i o u)) SETF gives VOWELS a
(A E I O U) value.
> ######>(length vowels) Now we can use VOWELS
5 in Lisp expressions.
> ######>(rest vowels)
(E I O U)
>######> vowels Its value is unchanged.
(A E I O U)
> ######>(setf vowels
’(a e i o u and sometimes y)) Give VOWELS a new
(A E I O U AND SOMETIMES Y) value.
>######> (rest (rest vowels)) Use the new value.
(I O U AND SOMETIMES Y)

setf的第一个参数是变量名；setf不会对这个参数求值。（setf可以不对参数求职是因为他是宏函数）第二个参数就是想要设置给变量的值；这个参数是求值的。它的值被计算出来然后返回设置到变量中。
Global variables are useful for holding on to values so we don’t have to
continually retype them.  Example:
全局变量用来保存值是非常有用的，我们就不是不得不重新输入了：
>######> (setf long-list ’(a b c d e f g h i))
(A B C D E F G H I)
>######> (setf head (first long-list))
A
> ######>(setf tail (rest long-list))
(B C D E F G H I)
> ######>(cons head tail)
(A B C D E F G H I)
> ######>equal long-list (cons head tail))
T
>######> (list head tail)
(A (B C D E F G H I))

head,tail和long-list都是全局变量。
###5.4 副作用
像car和+这类普通函数的用处在于他们的返回值，其他函数的用处主要在于它的副作用。setf的副作用就是改变了变量的值，这个副作用要比setf函数返回的值要重要的多。defun函数被调用的原因也是因为它的副作用：定义一个新的函数，defun函数返回的值就是所定义的函数的名字。
另一个有副作用的函数是random函数，Common Lisp的random函数是一个随机数生成器。（random n）返回一个随机选定的数字，从零开始到n（但是不包括n）。假如n是一个整数，random就会返回整数；如果输入是一个浮点数，random就会返回一个浮点数。
>######> (random 5)
3
>######> (random 5)
1
>######> (random 5.0)
2.32459
> ######>(random 5.0)
4.94179

random函数的副作用对于用户来说是隐藏的，他改变随机数生成器内部的几个变量，每一次被调用的时候允许生成不同的随机数。
setf函数可以改变任何变量的值，本地变量或者全局变量都可以。在本书中我们将只对全局变量使用setf，因为避免更改本地变量的值是一个好的编程风格。但仅仅是表示可以做到，下面的例子表示韩式是可以改变本地变量的值的。另外，请注意这个函数的函数体有两个括号（两个表达式），当一个函数有多个表达式的时候，他会对这些表达式都求值，然后返回最后一个表达式的值。
>(defun poor-style (p)
(setf p (+ p 5))
(list ’result ’is p))
>######> (poor-style 8)
(RESULT IS 13)
> ######>(poor-style 42)
(RESULT IS 47)
>######>p
Error! P unassigned variable

在函数poor-style内部，符号p指向的是一个本地变量，所以setf函数改变的是一个本地变量的值。全局变量并不受这个setf函数的影响。在求值回溯图中，这个赋值是嵌套在poor-style内部的setf函数的副作用。你也可以看到poor-style返回的结果并不是这个语句的结果，因为这个语句不是最后一个语句。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1elayibyf0bj20iz0fwmy6.jpg)
###5.5 特殊函数let
到现在为止，我们所见到的本地变量都是由用户定义的函数创造并调用的，例如doublel和average。另一种创造本地变量的方法就是用特殊函数let。例如，既然两个数的平均数average是他们和的一半，我们会需要一个叫做sum的本地变量来在average函数内部使用。我们可以使用let函数去创造这个本地变量并且给出一个初始值。而后，在函数内部使用这个let语句来计算。
>(defun average (x y)
>(let ((sum (+ x y)))
>(list x y ’average ’is (/ sum 2.0))))
>######>average 3 7)
>(3 7 AVERAGE IS 5.0)

let函数的语法结构是：
>(LET ((var-1 value-1)
>(var-2 value-2)
>...
>(var-n value-n))
>body)

let函数的第一个参数是一系列的变量和值。编号n中的语句是被求值的，然后本地变量n被创造并被赋值，最后函数体重大额语句被求值返回到上一层函数中。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1elayjm0ptjj20jz0lyjt9.jpg)
我们来看一下let函数的内部结构，内部的粗箭头是中空的箭头，标记了let函数的函数体。表达出let函数在average函数的语法区域内创造了自己的语法区域。当let的函数体被求值，eval会指向average创造的变量x和y。如果let函数的箭头是粗的实心的箭头，那么eval在求值的时候就无法使用x和y。这种情况下，eval会跳出函数体，在全局变量中寻找x和y的值，很明显这个会印发错误。
下面是let函数一下子创造两个本地变量的例子：
>(defun switch-billing (x)
>(let ((star (first x))
>(co-star (third x)))
>(list co-star ’accompanied ’by star)))
>######> (switch-billing ’(fred and ginger))
>(GINGER ACCOMPANIED BY FRED)

下面的求值回溯图准确的表示了let函数创造本地变量star和co-star的过程，请注意这两个变量和值的语句，（first x）和（third x），都是在本地变量被创造之前就被求值的。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1elayl48226j20sc0ipwgu.jpg)
###5.6 特殊函数let*
特殊函数let*类似于let，除了一点，他叔一个个创造本地变量，而不是像let那样一次性创造所有。因此，在第一个语句中创造的变量可以再第二个语句中直接使用。这个特性是十分有用的，特别是在长段的计算中警醒一些中间步骤。
例如，假设我想要计算物品价格变化的百分比的函数，新的价格和旧的价格作为输入。我们的函数必须计算两个输入的价格之间的差值，然后将他们的差值除以旧的价格来得到价格的变化，然后乘以一百得到百分比。我们可以使用本地变量名，diff，proportion，percentage还命名变量。使用let*来代替let，因为这些变量必须是被一个个创在，每一个的计算都依赖前一个。
>(defun price-change (old new)
>(let* ((diff (- new old))
>(proportion (/ diff old))
>(percentage (* proportion 100.0)))
>(list ’widgets ’changed ’by percentage
>’percent)))
>######> (price-change 1.25 1.35)
>(WIDGETS CHANGED BY 8.0 PERCENT)

price-change的求值回溯图表现了let*函数如何创在本地变量。请注意表达式（- new old）仅仅使用了两个语法环境中的变量new和old。表达式（/ diff old）则是在嵌套语法环境中被使用的同时定义了本地变量diff。最终的语句返回时，let*函数使用了所有的变量。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1elaym43fh9j20pg0m20ve.jpg)
一个很普遍的错误就是在需要使用let*函数的地方使用了let函数。考虑下面的函数fauly-size-range。它使用max函数和min函数来找到一组数字中的最大最小值。max和min是common lisp的内建函数，都接受一个或者更多的输入。最后一个语句将结果除以1.0的作用是是的返回值是浮点数而不是一个分数。
>(defun faulty-size-range (x y z)
>(let ((biggest (max x y z))
>(smallest (min x y z))
>(r (/ biggest smallest 1.0)))
>(list ’factor ’of r)))
>######> (faulty-size-range 35 87 4)
>Error in function SIZE-RANGE:
>BIGGEST unassigned variable.

表达式(/ BIGGEST SMALLEST 1.0)问题的原因是它的语法环境中并不包括上面这些变量，因此符号biggest会被解释为全局变量。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1elayn9jsc1j20n40chwfi.jpg)
解决问题的方法就是将let替换为let*：
>(defun correct-size-range (x y z)
>(let* ((biggest (max x y z))
>(smallest (min x y z))
>(r (/ biggest smallest 1.0)))
>(list ’factor ’of r)))

CORRECT-SIZE-RANGE函数显示的的求值回溯图显示就是在同一个语法环境中。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1elayohhdzoj20ol0kydi7.jpg)
不要被上面的例子误导认为let*就比let好可以全部替代。在一些情况下，乐透是唯一合适的解决方法，但这里我们就不深入了。在文体上来说，可以的话最好是使用let而不是let*，因为任何阅读你代码的人不需要依赖被创造出来的本地变量的位置。依赖程度小的程序更容易被阅读。
###5.7 副作用会引发bug
最好的情况是尽量避免程序中的副作用，这里有一个random函数的副作用引发的错误。假设我们想要一个monitor抛硬币的函数。大部分时候的会返回head或者tail，但是也有有几率会返回edge，表示硬币是刚好立在地面上。我们该怎么做呢？首先选一个从0开始到101（但是不包括101）的随机数，如果产生的随机数是0到49，就会返回head，如果结果是51到100，那么就会返回tail。刚好是50的话，就会返回edge。
>(defun coin-with-bug ()
>(cond ((< (random 101) 50) ’heads)
>((> (random 101) 50) ’tails)
>((equal (random 101) 50) ’edge)))
>######> (coin-with-bug)
>HEADS
>######> (coin-with-bug)
>TAILS
>######> (coin-with-bug)
>TAILS
>######> (coin-with-bug)
>NIL

为什么函数会返回nil？问题在于每一次调用这个函数，都会有三次对表达式(RANDOM 101)的求值。假设第一次求值的结果是65，是的id一个语句返回false。第二次返回35，结果也是false，第三次返回除了50之外的数字，那就还是false，最后cond就会返回nil。
修复这个bug也是很简单的，使用let函数来保存表达式(RANDOM 101)的值，就只会求值一次然后保存为本地变量，直接测试分类就可以实现了。
>(defun fair-coin ()
>(let ((toss (random 101)))
>(cond ((< toss 50) ’heads)
>((> toss 50) ’tails)
>(t ’edge))))

###小结
如果一个变量不是这个额函数创造那就会被认为是全局变量。本地变量的作用域被现实在创造他们的语句中，例如参数列表中的变量就是被限制在本函数中使用，还有let函数和let*函数创造的变量会被认为是他们函数体中的本地变量。全局变量是具名的因为以全局作为作用域，他们不属于任何函数。
setf函数是给变量赋值的宏函数，或者说改变已经有值的变量的值。副作用就是叫做赋值的功能，正是这个副作用是setf函数的功能所在。 effects.当多个表达式出现在函数体中或者let，let*函数体重的时候，最后一个表达式的值将会被返回，其他的表达式只是为了他们的副作用而存在。
###本章涉及函数
赋值宏函数：setf
创造本地变量的特殊函数：let，let*
###Lisp Toolkit:documentation和apropos
大部分Common Lisp实现都包括了每一个内建函数和变量的在线文档。访问这个文档一个方法是使用documentation函数，返回文档字符串（documentation string）。
>######> (documentation ’cons ’function)
"(CONS x y) returns a list with x as the car
and y as the cdr."
>######> (documentation ’*print-length* ’variable)
"*PRINT-LENGTH* determines how many elements to
print on each level of a list.  Unlimited if NIL."

程序员不是经常使用documentation函数的，因为有一个更快捷的方法来获取文档字符串。在我的机器上，例如，当我把鼠标挡在一个符号上，或者按下control-meta-shift-s的时候，对应的文档就会有一个弹框出来。
你可以在定义函数的时候带上文档字符串，位置的话应该放在参数列表的后面。
>(defun average (x y)
>"Returns the mean (average value) of its two
>inputs."
>(/ (+ x y) 2.0))
>######> (documentation ’average ’function)
>"Returns the mean (average value) of its two
>inputs."

为函数写文档字符串是一个好习惯。也会对使用你程序的人有所帮助，在你需要的时候在线文档总是可以方便获得的，
另外一个给程序加上文档的方法是在文件中加上注释。Lisp程序中的注释必须有前导分号。加载Lisp程序的时候，只要遇到分号，那么本行之后的所有的内容豆浆杯忽略。注释的好处是帮助那些测试程序的人；注释将被Lisp无视而且也不作为在线文档的一部分。但是他们因为提供了比文档字符串多得多的信息而十分实用。注释可能更加精细，例如，解释函数中一个或两个更加精细的语句。
按照惯例，Lisp注释出现在三处。第一处出现在一个语句的右边，并且是前导一个分号。第二种是出现在一个函数当中，并且独占一行，前导两个空格。第三种是出现在函数定义之外，并且前导三个分号。一些Lisp编辑器会基于注释的前导分号个数自动进行缩进。一耳光如此风格的注释如下“
>;;; Function to compute Einstein’s E = mc2
>(defun einstein (m)
>(let ((c 300000.0)) ; speed of light in km/sec.
>;; E is energy
>;; m is mass
>(* m c c)))

另一个很有用的文档函数就是apropos，它的作用是找出所有具有包含你指定的字符串的内建函数。例如，假设你想要找到所有包含字符串total的变量和函数。你可以使用aprops：
>######> (apropos "TOTAL" "USER")
>ARRAY-TOTAL-SIZE (function)
>ARRAY-TOTAL-SIZE-LIMIT, constant, value: 134217727

我们看见的是common lisp的内建函数有ARRAY-TOTAL-SIZE和一个内建常量ARRAY-TOTAL-SIZE-LIMIT。（一个常量就是一个值不可以改变的变量，pi就是一个常量。）
apropos的第二个参数名字叫做包名。你应该总是使用字符串“USER”（全部大写）作为第二个参数。否则lisp就会输出很多实现中定义的函数，这些函数来自其他包，你可能并不在意那些包是什么。包是common lisp另一个隐藏的特性，在本书我们也不准备讨论。
###第五章进阶话题
###5.8 符号和值单元
我们来回顾一下，一个符号是由五个部分组成的。两个部分我们到现在已经介绍了，就是符号的名字和函数单元。现在介绍第三个组成部分，每一个符号都有的就是值单元部分。他指向的就是这个符号所命名的全局变量。例如，有一个全局变量叫做TOTAL，它的值是12，然后这个TOTAL的内部结构看起来是这样：
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1elayr62zncj20cg08vt8r.jpg)
相似的，有个全局变量fish的值是trout，结构看起来就是这样。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1elayr6l1d2j20pd0c8t92.jpg)
符号T和nil都是求值为自身的，因为他们的值单元是指向自己的，换句话书哟，符号T就是以自己名字命名的全局变量符号T；nil的值也是符号nil。这些符号的内部结构都是有一个循环存在的，
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1elayr76p0ej20s30ak3yw.jpg)
一个符号可以给很多变量命名，所以其中只有一个是全局变量。换言之，只有一个是存在在全局变量的语法环境中的。值单元为那个变量所保留。所有其他变量都必须存在于本地语法环境中，他们的值也是存储在除了符号值单元的其他地方。Common Lisp不特别制定本地变量的值存储在哪里。这些细节留给具体实现去处理。
因为字符的组成单元有函数部分和之部分的分别，我们可以有相同名字的函数和变量。如果我们给全局变量CAR赋值Rolls-Royce，符号car看上去就是这样：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1elayr7s8bqj20o408xt98.jpg)
由Common Lisp来决定一个符号是指向函数还是一个变量，这都是基于符号出现的语法环境。如果一个符号出现在列表的第一个元素，那么就会被认为是一个函数名，在其他位置的话就会被认为是变量名。所以(CAR ’(A B C))调用的额是car函数，返回的是A。而(LIST ’A ’NEW CAR) 中的car指向的是i个全局变量产生值(A NEW ROLLS-ROYCE)。
###区别全局变量和本地变量
到现在我们已经清楚了，符号并不是变量本身；他们是作为变量的名字存在（或者也作为函数的名字）。准确的说一个符号的指向是根据符号的出现的位置决定的。在下面的例子中，有两个叫做x的变量，全局变量x的值是57，而函数newvar的变量x被赋予甚么值都没有关系。
>(setf x 57)
>(defun newvar (x)
>(list ’value ’of ’x ’is x))
>######> x
>57
>######> (newvar ’whoopie)
>(VALUE OF X IS WHOOPIE)
>######> x
>57

在newvar函数内部，名字x是指向本地变量x，这个变量是由函数创造的并且赋值whoopie。在函数外部，x指向的是全局变量x，它的值是57，符号x的值单元一直是指向57的。函数newvar的本地变量存储在其他地方。求值回溯图展现了两个X之间的关系。
全局变量的值是57
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1elaysicuiej20m20csta3.jpg)
全局变量的值还是57
对符号x的求值规则是在newvar函数的函数体中，从内想挖的在语法环境中寻找，是不是有这样一个名字的变量。当前环境中的变量有这么一个，被赋值whoopie，eval就不会去指向全局变量x。
真实的规则是要比这个复杂一点。eval函数性当前语法环境向外寻找的时候只有遇到语法环境结束或者找打该变量才会停止。在语法环境结束的情况下，是不可以向更外层寻找的；只能指向全局变量。下面的例子说明的很清楚：
>(setf a 100)
>(defun f (a)
>(list a (g (+ a 1))))
>(defun g (b)
>(list a b))
>######> (f 3)
>(3 (100 4))

在这个例子中，我们创造了一个全局变量叫做A，赋值100.当调用F的时候，他创造了本地变量A赋值3。然后在调用函数g，g的语法环境是独立于f的。（每一个由defun定义的函数都有独立的而语法环境）。在求值回溯图中，粗线指示出了g的边界，在f中创造的变量，在g中是不能访问的。所以在g的函数体中，没有具名变量A，eval就忽视了语法环境的边界指向了全局变量A。 
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1elaythlgwmj20jy0ozjt6.jpg)
###5.10 绑定，作用域和赋值
因为Common Lisp是从更加古老的方言进化而来的，所以在术语上所继承的一些东西就不是很合适。本书致力于使用一套正确的不容易混淆的术语体系，但是为了和其他的书籍保持一致，也为了庞大的Lisp社区，我来化一节的篇幅来辨清术语绑定（binding）的误用。
由于某些历史原因，变量具有一个值被称作被绑定，变量没有值的情况被称作未绑定。我们在书中所说的未赋值错误，相同意思的错误信息，在大部分lisp实现中被称为未绑定变量。
创造一个新变量并且给他赋值的过程被称作绑定。假如变量是出现在参数列表中，被称作lambda绑定。如果是出现在let或者let*的语句的变量列表中，被称作let绑定。
但是旧时候的lisp程序员是自己陷入到术语逻辑陷阱中，当他们讨论变量绑定的时候，其实并不是真的在说有语法作用域的lisp。当变量的语法作用与出错，Common Lisp会提供另一种作用域规则，叫做动态作用域，我们会在地十四章接触到。动态作用域是大部分早期lisp方言的标准配置，除了Scheme和T之外。对于动态作用域来说，“绑定”并不是一定就是有一个值，因为一个变量被绑定了但是没有值也是可能的。
在后续章节的中，老的lisp程序员可能会对函数f和g这么表达，符号a被函数f绑定到3上在Common Lisp中这样的表述是不恰当的。符号从未被绑定。只有变量被绑定。还有，没有唯一的叫做a的变量，而是有两个。即使是F的本地变量a存在时候，全局变量a也可以被g在f函数体的语法环境之外引用。这样合适的措辞才Common Lisp中就可以这样说，F函数将本地变量a绑定到3。 
