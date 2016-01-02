#第四章 条件式
###4.1 导语
做出判断是计算的一个基本功能，所有不凡的程序都会做判断。在本章，我们将学习一些特殊的做出判断的函数，叫做条件式，通过可变的断言来从一个集合中选择结果。（一个断言表达式就是一个结果被解释为true或者false的表达式）。
条件式允许函数根据不同的输入来改变他们的具体行为。既然我们可以构造自己的断言来空中条件式，那么就可以使用函数来做出任意复杂的判断。

###4.2 特殊函数if
if函数是一个最简单的lisp条件式，条件式都是宏或者特殊函数，所以他们的参数是不会被自动求值的。defun函数和quote函数是我们学过的也具有这一特性的函数。像+函数或者cons函数这种普通函数，总是对自己的参数求值的。
特殊函数if包括三个部分：测试部分（test），真值部分（true-part）和假值部分（false-part）。如果测试部分返回true，函数返回真值部分的值，如果测试部分是false，那么就跳过真值部分，返回假值部分的值，这里有一些例子：
>(if (oddp 1) ’odd ’even) → odd
>(if (oddp 2) ’odd ’even) → even
>(if t ’test-was-true ’test-was-false) → test-was-true
>(if nil ’test-was-true ’test-was-false) → test-was-false
>(if (symbolp ’foo) (* 5 5) (+ 5 5)) → 25
>(if (symbolp 1) (* 5 5) (+ 5 5)) → 10

用if函数来定义一个返回输入的数字的绝对值函数。绝对值总是非负数。对于负数，绝对值就是这个数字的负数，对于正数和零来说，绝对值就是本身。这样的话我们的函数my-abs的定义就会简单了。（我们叫这个函数my-abs是因为内建函数已经有一个abs函数了）。
>(defun my-abs (x)
>(if (< x 0) (- x) x))

if函数的测试部分就是表达式（< x 0）。如果测试部分的值是true，那么真值部分就会被求值然后返回，如果测试部分被求值为false，那么假值部分就会被求值然后返回。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1el9s4hsm3cj20pq05x3ys.jpg)
这里是一个简单的问题判断函数，symbol-test函数返回一个信息来判断输入是不是字符串。
>(defun symbol-test (x)
>(if (symbolp x) (list ’yes x ’is ’a ’symbol)
>(list ’no x ’is ’not ’a ’symbol)))
if函数如果只收到两个参数，也就是没有假值部分，那么就会自动设置为nil。
>(if t ’happy) → happy
>(if nil ’happy) → nil

###4.3 宏函数cond
cond是一个经典的lisp函数。它的输入由任意数量的测试结果语句组成，一般形式的cond函数表达式格式竟会在第五章介绍，现在我们先先来熟悉简化版本：
>(COND (test-1 consequent-1)
>(test-2 consequent-2)
>(test-3 consequent-3)
>....
>(test-n consequent-n))

cond函数的工作流程：逐一检测测试结果语句。假如语句的测试部分被求值为真，那么cond函数将会对结果部分求值然后返回；并且不再考虑其他语句。如果测试部分的结果是false，那么cond函数会跳过结果部分然后检测下一个语句。如果所有语句检测结束并且没有发现测试部分是true的语句，cond函数返回nil。
让我们用cond函数来定义一个compare函数来比较两个数字。如果两个数字是相等的，compare函数返回字符串numbers-are-the-same，如果第一个数字小于第二个，返回first-is-smaller，第一个比第二个大的话就返回first-is-bigger，每一个判断都是一个独立的cond测试结果语句。
>(defun compare (x y)
>(cond ((equal x y) ’numbers-are-the-same)
>((< x y) ’first-is-smaller)
>((> x y) ’first-is-bigger)))

凑近了看cond函数，这是一个四元素的列表，第一个元素师字符串cond其余三个元素师测试结果语句。第一个语句是一个双元素列表，第一个元素是等于判断表达式，结果部分是引号字符串。以下是一些cpmpare函数的例子：
>(compare 3 5) → first-is-smaller
>(compare 7 2) → first-is-bigger
>(compare 4 4) → numbers-are-the-same

cond函数和if函数是相似的函数，但是cond函数出现的频率可能更高一些，因为他是接受任意数量的语句作为输入的，但是同样的特性也可以用if函数嵌套来实现。这一点竟会在后续章节中介绍。
###4.4 使用T作为测试部分
使用cond函数的一个标准技巧就是包括下面格式的语句：
>(T consequent)

测试T的话结果一定就是true，所以如果cond函数检测到了这个语句必定会开始对结果部分求值。我们把这个语句放在整个函数的最后面，以保证之前所有的语句都被检测过了。举例：接下来这个函数是输入城市返回所在国家的函数，假如这个函数没有涉及一个特别的城市，那么就会返回unknown。
>(defun where-is (x)
>(cond ((equal x ’paris) ’france)
>((equal x ’london) ’england)
>((equal x ’beijing) ’china)
>(t ’unknown)))

请注意最后一个语句的是由T作为测试部分，如果上面没有语句被检测为真，那么就会到达这个语句，然后返回unknown。
>(where-is ’london) → england
>(where-is ’beijing) → china
>(where-is ’hackensack) → unknown

我们来回顾if函数的一般格式：
>(IF test true-part false-part)

我们可以将任何if函数表达式转换成cond函数表达式，只需要使用两个语句：
>(COND (test true-part)
>(T false-part))

###4.5 cond函数的另外两例
emphasize函数，将一段文字的第一个单词，从good改成great，或者把bad改成awful，返回改动过后的文字：
>(defun emphasize (x)
>(cond ((equal (first x) ’good) (cons ’great (rest x)))
>((equal (first x) ’bad) (cons ’awful (rest x)))))

例如文字(GOOD MYSTERY STORY)作为输入的话，在emphasize函数内部会发生什么？变量x首先被赋值成(GOOD MYSTERY STORY)，cond函数开始检测测试结果语句，第一个语句是：
>((equal (first x) ’good) (cons ’great (rest x)))

(FIRST X)表达式求值是good，语句的测试部分是true，结果部分会从great和输入的其余部分来构造一个新的列表，这个列表就是函数的返回值。
>(emphasize ’(good mystery story)) → (great mystery story)

现在假设我们输入文字(MEDIOCRE MYSTERY STORY)给函数emphasize。第一个语句在比较good和mediocre的时候返回nil。之后的和bad比较也是返回nil。现在cond函数已经没有语句可以检测了，所以返回nil。
>(emphasize ’(mediocre mystery story)) → nil

What if we want EMPHASIZE to return the original input instead of NIL
when it can’t figure out how to emphasize it?  We simply use the T-as-test
trick, demonstrated in the function EMPHASIZE2:
如果我们想要函数emphasize在没有匹配结果的时候返回原输入而不是nil，我们该怎么办？我么只要简单地使用T作为测试部分这个技巧就好，这样就可以定义emphasize2：
>(defun emphasize2 (x)
>(cond ((equal (first x) ’good) (cons ’great (rest x)))
>((equal (first x) ’bad) (cons ’awful (rest x)))
>(t x)))

如果cond函数到了最后一个语句，测试部分是T可以保证这个输入x将会被返回。
>(emphasize2 ’(good day)) → (great day)
>(emphasize2 ’(bad day)) → (awful day)
>(emphasize2 ’(long day)) → (long day)

函数compute接受三个输入。如果第一个输入时字符串sunm-of，那么就会返回第二和第三个的输入的和，如果是字符串product-of，那么就返回积。如果都不是的话米娜就返回(THAT DOES NOT COMPUTE)。
>(defun compute (op x y)
>(cond ((equal op ’sum-of) (+ x y))
>((equal op ’product-of) (* x y))
>(t ’(that does not compute))))

这是一些compute函数的例子：
>(compute ’sum-of 3 7) → 10
>(compute ’product-of 2 4) → 8
>(compute ’zorch-of 3 1) → (that does not compute)

###4.6 cond函数和括号错误
Parenthesis errors can play havoc with COND expressions.  Most COND
clauses begin with exactly two parentheses.  The first marks the beginning of
the clause, and the second marks the beginning of the clause’s test.  For
example, in the WHERE-IS function, the test part of the first clause is the
expression
括号错误是cond表达式的灾难，大部分cond函数的语句是由两个左括号开头的，一个是语句的左括号，一个是测试部分的左括号。比如where-is函数的第一个语句的测试部分就是：
>(EQUAL X ’PARIS)

所以这个语句看上去就像是这样：
>((EQUAL X ’PARIS) . . .)

如果一个语句的测试部分只是一个字符串，不是一个函数的调用，那么看上去就是一个括号开头的。请注意，where-is函数的T作为测试部分的语句是只有一个左括号的。
这里有两个更加普遍的有关cond函数的括号错误。首先假设我们在cond函数语句中遗漏了一个括号，将会发生什么？
>(cond (equal x ’paris ’france)
>(. . .)
>(. . .)
>(t ’unknown))

第一个语句遗漏了测试部分的括号，所以作为测试部分就只有equal字符串了。当测试开始，equal将会引发一个未赋值变量错误。从另一个角度看，如果使用了过多的括号的话：
>(cond ((. . .) ’france)
>((. . .) ’england)
>((. . .) ’china)
>((t ’unknown)))

如果输入是hackensack，那么就会到达第四个语句。由于多了一层括号，测试语句就是（T ‘unknown），而不是字符串T，T不是一个函数，所以测试部分就会报出一个未定义函数错误。
###4.7 宏函数and和or
我们经常需要通过简单的断言来定义复杂的断言。宏函数and和or就可以实现这个想法。再给出and和or的求值规则之前，我们先来看一个例子。假设我们想要一个断言来判断输入的数字是不是小数字（不大于两位数）。我们可以使用and函数来百事简单条件的结合：
>(defun small-positive-oddp (x)
>(and (< x 100)
>(> x 0)
>(oddp x)))

或者我们假设一个函数gtest，接受两个输入，如果第一个输入大于第二个或者其中一个是0就返回T，这些条件式请成了一个可分的集合，只要有一个满足是true那就会返回T。
>(defun gtest (x y)
>(or (> x y)
>(zerop x)
>(zerop y)))

像cond，and和or函数这样的宏函数，可以接受任意数量的语句，还可以先不对参数求值。然而对于函数and和or，语句是简单的额测试语句，不是测试结果语句。
###4.8 and和or函数的求值
and和or的意思在lisp中和逻辑上的and和or有些细微的区别。and函数的准确求值规则是：求所有语句进行一次求值，如果有一个语句返回nil，求值结束返回nil，否则就继续求值下一个语句。如果所有的语句都是非nil的结果，那么就返回最后一个语句的值。
>(and nil t t) → nil
>(and ’george nil ’harry) → nil
>(and ’george ’fred ’harry) → harry
>(and 1 2 3 4 5) → 5

or函数求值规则是：对所有语句依次求值，如果一个语句返回非nil的值，停止求值并返回这个值，否则继续求值下一个语句，没有则返回nil。
>(or nil t t) → t
>(or ’george nil ’harry) → george
>(or ’george ’fred ’harry) → george
>(or nil ’fred ’harry) → fred

###4.9 构造复杂断言
how-alike函数是从一些不同方面比较两个输入来看他们的相似的程度。使用and函数来构造复杂断言作为cond函数的语句的一部分。
>(defun how-alike (a b)
>(cond ((equal a b) ’the-same)
>((and (oddp a) (oddp b)) ’both-odd)
>((and (not (oddp a)) (not (oddp b))) ’both-even)
>((and (< a 0) (< b 0)) ’both-negative)
>(t ’not-alike)))
>(how-alike 7 7) → the-same
>(how-alike 3 5) → both-odd
>(how-alike -2 -3) → both-negative
>(how-alike 5 8) → not-alike

same-sign断言的作用是使用and和or的组合来测试是否两个输入具有相同的符号：
>(defun same-sign (x y)
>(or (and (zerop x) (zerop y))
>(and (< x 0) (< y 0))
>(and (> x 0) (> y 0))))

or函数的输入返回T的时候，same-sign返回T。这些输入中的每一个都是一个and函数表达式。第一个测试部分就是X是否是0并且Y是否是0。第二个测试部分就是x是否是负数并且y是否是负数，还有第三个测试部分x和y是否是正数：
>(same-sign 0 0) → t
>(same-sign -3 -4) → t
>(same-sign 3 4) → t
>(same-sign -3 4) → nil

###4.10 为什么and和or是条件式
为什么and和or是条件式而不是普通函数呢？理由就是他们并不被要求对每一个语句都求值。and函数中的任何语句如果返回nil，或者or函数中的任何语句返回非nil，那么接下来的语句都不会被求值。这个特性是很有用的，因为我们很需要暂停求值来避免错误的发生。比如，考虑一下posnump断言：
>(defun posnump (x)
>(and (numberp x) (plusp x)))

如果输入是一个数字并且是个正数，那么posnump断言返回T。内建的plusp断言被使用来判断一个数字是不是正数，但是plusp不能用在非数字的情况下，不然就会发生输入类型错误。所以在调用plusp之前，很重要的事情就是用posnump确认输入是不是一个数字，如果不是就不会去调用plusp了。这历史posnump的一个错误版本：
>(defun faulty-posnump (x)
>(and (plusp x) (numberp x)))

如果fault-posnump被字符串fred调用，而不是一个数字，他做的第一件事情时间差fred是不是大于0，并且引发一个输入类型错误。如果是正常的posnump调用的话米娜就会返回nil，plusp也不会调用。
###4.11 条件式是可以互相替换的
使用and和or来定义的函数也可以使用cond和if来定义，反之亦然，我们来回顾posnump的定义：
>(defun posnump (x)
>(and (numberp x) (> x 0)))

这里有一个使用if来代替and定义的posnump版本：
>(defun posnump-2 (x)
>(if (numberp x) (> x 0) nil))

这个posnump的版本是先判断是不是数字，然后如果判断继续，真值部分就开始对表达式（> x 0）求值。如果测试数字是false，假值部分就是nil。下面的posnump版本是由cond定义的：
>(defun posnump-3 (x)
>(cond ((numberp x) (> x 0))
>(t nil)))

我们看一下另一个条件式的使用，这是使用cond定义的where-is函数的原始版本：
>(defun where-is (x)
>(cond ((equal x ’paris) ’france)
>((equal x ’london) ’england)
>((equal x ’beijing) ’china)
>(t ’unknown)))

cond有四个语句，我们也可以使用if函数重写这个where-is函数，这样子三个if函数一起使用的话，这种结构就叫做if嵌套。
>(defun where-is-2 (x)
>(if (equal x ’paris) ’france
>(if (equal x ’london) ’england
>(if (equal x ’beijing) ’china
>’unknown))))

假设输入beijing到函数where-is-2，如下求值回溯图所示，本地变量X被赋值beijing，函数体开始求值，where-is-2函数题是一个if函数来检查输入是不是paris。当然不是paris，所以if函数就开始求值假值部分，价值部分也是一个if函数，测试输入是不是等于london，也不是米那就是转到假值部分，也是一个if函数，第三个if函数测试输入是不是等于beijing，是的！所以返回真值部分，china。第三个if函数返回china也就是第二个if函数的假值部分的值，在一次返回到第一个if函数的假值部分，就是china。最终结果就是china。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el9s6lz1i1j20lx0mf0ue.jpg)
我们也可以写另一个使用and和or来定义的where-is版本，这个版本使用十分简单的两层模式而不是if函数需要的嵌套模式。
>(defun where-is-3 (x)
>(or (and (equal x ’paris) ’france)
>(and (equal x ’london) ’england)
>(and (equal x ’beijing) ’china)
>’unknown))

对表达式（where-is-3 ’london）求值。输入x被绑定为london，之后or函数开始一一检测有没有语句不是nil的结果。第一个语句就是一个and表达式，and对其中的表达式（equal x ‘paris）求值得到一个nil的结果，and停止求值并且返回nil。or开始检测下一个语句，也是一个and表达式，（equal x ’london）返回了T，所以and开始检测下一个语句，‘england求值为englad，and跳出语句，返回最后一个语句的值，既然检测到一个非nil的值，那么or就会返回england。
既然if，cond和and，or都是条件表达式而且可以相互转换，那为什么还需要那么多呢？统一一个不是很好？这是一个为了方便的原因。if是最方便使用的简单函数们就像绝对值函数一样方便。and和or十分适合来写复杂的断言。cond是对分支选择的时候的最简单的方案。在不同的任务中选择正确的条件式是编程艺术的一部分。
###小结
条件式是的计算机可以做出判断来控制行为。if函数是一个简单的条件式；它的语法就是（if 条件 真值部分 假值部分），cond函数，最普遍的条件式，接受一系列测试结果语句作为输入，逐个求值知道找到一个true的结果为止，然后返回结果的值。没有true的情况下，cond返回nil。
and和or也是条件式，and对语句逐一求值，知道有一个返回nil，and就返回这个值。假如所有的语句都返回true，and就会返回最后一个语句的值。or对语句逐一求值，知道有一个非nil的值出现，然后返回那个值。如果所有的语句都是nil，or就会返回nil。and和or都不被看做都是断言因为他们不是普通函数。
在使用cond编程的时候，把（T 结果）放在所有的语句的最后是一个很有用的技巧。既然检测T是永远返回True的，这个语句就类似于某种杂物箱，最后没有语句是true的时候，最后会是这个结果被返回。
条件式一个很重要的特性就是，他们并不对所有的输入都求值。这个特性使得我们可以用预先准备的表达式来方式一个敏感易出错的表达式印发求值错误而使得程序停止。条件式嫩而过做到这一点是因为他们是宏函数或者特殊函数，而不是普通函数。
###本章出现函数
条件式: IF, COND, AND, OR
断言: PLUSP
###lisp toolkit：step
step是一个交互式工具，在一个lisp表达式求值的时候我们步进看到内部发生的所有细节。这个几乎就是调试器的功能（在程序中找到并排除错误），但是在用来学习想条件式这样新的特殊函数的时候也是很有效果。
每一个common lisp实现都提供了这个工具的定制版本，只有名字是有统一标准的。大部分步进器（stepper）都是接受单字母命令来告诉程序在下一个迭代的时候该做什么，比如继续步进，不要步进直接求值，进入调试器，等等。步进器接受？问号字符来作为帮助帮助文档的调用。在本书中，我们只使用一个命令，n，来进入求值的下一步。
因为step是一个宏函数，它的输入不应该被引用，这历史step函数的一个例子。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el9s7r6g3kj20sw08bmxz.jpg)
还有一个更加详细的my-abs函数的例子，我们自己版本的求绝对值得函数，特殊函数block在步进输出中的出现可以被忽略。一些lisp实现给每一个函数定义之前都加上了block，在其他实现这个格式是含糊的而且不出现在步进输出中的。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el9s8rjrdyj20t20l1765.jpg)
step的输出很像没有箭头的求值回溯图。这里有一个求值回溯图来比较一下：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1el9s9yyehkj20e20hvgmf.jpg)

###第四章 进阶话题
###4.12 布尔函数
布尔函数就是指输入和输出都是真值的函数（nil或者T）。我们在前几章已经遇见过布尔函数，术语布尔函数是来自于十九世纪英国数学家布尔。布尔逻辑在今天被用来被描述计算机芯片的逻辑行为。
布尔函数的另一个名字叫做逻辑函数，因为使用逻辑值true和false的缘故。我们来定义一个双输入的逻辑与函数logical-and：
>(defun logical-and (x y) (and x y t))

这个普通函数和宏函数and在某些方面有些区别。首先如上所述，这个函数必须输两个输入，这一点不是很要紧，因为我们可以通过嵌套和多层结构来组合接受更多输入。逻辑与logical-and返回逻辑值T或者nil。别无其他。
>(logical-and ’tweet ’woof) → t
>(and ’tweet ’woof) → woof

最最重要的是一个事实就是logical-and并不是一个宏函数；他不能控制它的参数是否被求值。在接下来的例子中，表达式（oddp ’fred）引发了一个错误，因为是使用logical-and，而不是and，因为and是不会对第二个表达式求值的。
>(and (numberp ’fred) (oddp ’fred)) → nil
>(logical-and (numberp ’fred) (oddp ’fred)) → Error! FRED wrong type input to ODDP.

布尔函数比条件式要简单，在lisp中布尔函数对应于电子学中的布尔电路，他们都是从家算计内部的原始电路而来的逻辑操作。
###4.13 真值表
真值表是描述布尔函数很方便的表达方式。为了表述一个函数，我们简单认为每一种T和NIL的组合都被考虑为输入，然后写下函数的每一个输出结果。下图是函数NOT的真值表。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el9sb61uemj20bj04o0so.jpg)
下面是逻辑与的真值表，既然函数是双输入的，那么考虑到所有可能的情况，真值表也即是2的平方，4行。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1el9sb6heshj20ka06yaad.jpg)
###4.14 德摩根定理（Demorgan's Theorem）
德摩根定理着眼于and和or的互换性。如果有其中一个的表达式九个一和not组合装换成另一个的表达式。下面是德摩根定理的两种表现：
>(and x y)  =  (not (or (not x) (not y)))
>(or x y)  =  (not (and (not x) (not y)))

这些等式看上去比较难以理解，我用自然语言来解释一下。第一个等式是说假如x和y都是true，那么x不是false，y也不是false。第二个等式是说如果x或者y是true，那么x和y不会都是false。自然语言听上期就清晰多了，但是我们是不是确认这些等式是正确的呢？我们来测试一下。
>(defun demorgan-and (x y)
>(not (or (not x) (not y))))
>(defun demorgan-or (x y)
>(not (and (not x) (not y))))
>(logical-and t t) → t
>(demorgan-and t t) → t
>(logical-and t nil) → nil
>(demorgan-and t nil) → nil
>(logical-or t nil) → t
>(demorgan-or t nil) → t
>(logical-or nil nil) → nil
>(demorgan-or nil nil) → nil

这个测试肯定不是完全的，剩下的测试你可以自己完成。
德摩根定理证明了在逻辑上and和or是可以互相转换的。但是是不是同样适用于lisp中的条件式and和or呢？不尽然。多重的not函数的使用意味着一些非典型的真值将会通过这个过程成为典型的真值输出，所以德摩根定理是不适用的。
>(and ’foo ’bar) → bar
>(not (or (not ’foo) (not ’bar))) → t

然而德摩根定理确实保留了and和or的条件特性。像（and x y）这样的语句会求值也会被这样(NOT (OR (NOT X) (NOT Y)))求值，语句也不会被求职忙活着不会被其他表达式求值。
>(and (numberp ’fred) (plusp ’fred)) → nil
>(not (or (not (numberp ’fred)) (not (plusp ’fred)))) → nil

德摩根定理在简化复杂断言构成的表达式上特别有用，函数如下：
>(defun complicated-predicate (x y)
>(not (and (evenp x) (evenp y))))

函数体可以被转化成or组成的表达式：
>(or (not (evenp x)) (not (evenp y)))

既然evenp是oddp的补集，那么我们可以导出：
>(defun simplified-predicate (x y)
>(or (oddp x) (oddp y)))

