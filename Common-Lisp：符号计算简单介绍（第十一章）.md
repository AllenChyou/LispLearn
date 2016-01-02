#第十一章 迭代和块结构
###11.1 导语
名词“迭代”的意思就是重复，或者说一遍又一遍的做一件事情。递归和函数式操作就是重复的，但是迭代（iteration）（也被称作循环（looping））是一个最简单的循环（repetitive）控制结构。实际上，所有的编程语言都会包括一些写迭代表达式的方法。
在lisp中的迭代比大部分其他语言要更加精妙一些。Lisp提供强大的迭代结构，叫做do和do * 。还有更简化的版本叫做dotimes和dolist。
在本章我们也会学习块结构，一个从algol族语言（包括pascal，modula，ada）中引申过来的概念，我们可以看到如何将表达式组织进块（blocks）中，如何给块命名，以及为什么块很有用。
###11.2 dotimes和dolist
最简单的迭代格式就是dotiimes和dolist，两个都是宏函数，就是说他们不会对所有参数求值，他们有相同的语法。
![1.jpg](http://upload-images.jianshu.io/upload_images/46495-426972be97d7d0a6.jpg)
dotimes会发福对函数体内的语句求值n次，次数是由一个步进的所因变量控制，从0一直到n-1。然后就会返回结果语句的值，如果忽略的话默认就是nil。（结果语句是括起来的因为是可选的）下面是一个dotimes的例子，从0数到3.索引变量被命名为I，请注意dotimes返回的结果是nil。
![2.JPG](http://upload-images.jianshu.io/upload_images/46495-2ae986212c5137e2.JPG)
dolist和dotimes的语法是一样的，只是把步进的数字替换成了步进的元素列表。在接下来的例子中，dolist返回的值是字符flowers。
![3.JPG](http://upload-images.jianshu.io/upload_images/46495-526f6d34ab0aa5da.JPG)
###11.3 离开循环体
不想再继续循环，想马上离开循环提的时候，可以调用return函数，return函数接受一个输入，返回的值就作为迭代语句的结果。当return函数被用在强制结束迭代的时候，迭代的结果语句表达式将会被忽略。
一个叫做find-first-odd的函数返回的是列表的第一个奇数。它使用dolist来循环列表中的元素，找到奇数的时候调用return来离开循环。如果列表不包括奇数，之后当循环结束，dolist会返回nil。一个关于find-first-odd很有意思的点就是循环体中包括了两个语句，而不是一个。循环体可以包含任何数量的语句。
![4.JPG](http://upload-images.jianshu.io/upload_images/46495-10db930d0880996b.JPG)
dolist设定一个特定的返回值语句是十分有用的，如下例，函数check-all-odd使用dolist来检查是不是所有元素都是odd，如果是，dolist就会在循环结束的时候返回字符T，如果有任何非奇数的数字存在，函数马上从循环中返回，返回值是nil。
![5.JPG](http://upload-images.jianshu.io/upload_images/46495-33ee7e33655aedc5.JPG)
###11.4 递归搜索和迭代搜索的比较
用在搜索普通列表的时候，迭代比递归用起来更加简单。据不同的实现来说，也许也是更有效率的。比较两个find-first-odd的版本，这些代码已经将format语句简化省略了。
![6.JPG](http://upload-images.jianshu.io/upload_images/46495-b99fdf5078bdb002.JPG)
迭代版本有几个小优点。首先，这个终极的是语句是没有疑问的，dolist一定会在列表的尽头停止操作。而在递归版本中我们必须要特意加一个cond语句来含混的检查这个边界。第二，在迭代版本中变量E是迭代中自带的，不需要另外设置步进变量，这个很方便。在递归版本中，我们不得不使用（rest x）来指向下一个操作对象，在每一个递归中含混的计算每一个（rest x）。
在其他情况下，递归可以比迭代更加简洁而且自然。例如，你可以很方便的用car/cdr递归来搜索一个树，没有相等同的方法来优雅实现这样的功能。当然迭代的解决方法是存在的，但是以很丑陋的方式。
###11.5 使用赋值构建结果
在第八章，我们见到了使用不同的方式来建立一个结果，比如通过递归调用构建一个列表。在迭代程序里，结果是通过反复赋值来构建的，我们首先见到如何在dolist或者dotimes的函数体里通过setf的赋值来构建结果。之后张洁丽你会看到如何使用do来赋值。
Let’s start by using DOTIMES to compute the factorial function. First we
create an auxiliary variable PROD with initial value one. We will repetitively
update this value in the body of the DOTIMES, and then return the final value
of PROD as the result of the DOTIMES. Since the index variable I varies
from zero to N-1 rather than from one to N, we must add one to I each time
we reference its value in the body. Thus, (IT-FACT 5) counts from zero up to
four, but it multiples PROD by the numbers one through five.
我们首先来看看使用dotimes计算阶乘函数，首先我们创建一个辅助变量prod，初始值是1，我们会在dotimes的函数体内反复更新这个变量，然后最后返回prod的值。因为索引变量时从0到n-1，而不是0到n，我们要每一次都给I加1。（it-face 5）是从0到4，但是prod的相乘数字是1到5。
![7.JPG](http://upload-images.jianshu.io/upload_images/46495-6c117ca932c3f634.JPG)
下面是另一个使用赋值的例子，写一个迭代的交集函数。变量elementE币绑定在集合x的元素上，如果element是集合y的元素，会被压入result-set，否则就不会。当所有的x的元素被处理结束，dolist返回result-set的值。
![8.JPG](http://upload-images.jianshu.io/upload_images/46495-a65aec87a501f093.JPG)
###11.6 使用mapcar的迭代和递归的比较
mapcar是应用一个函数到列表里每一个元素的最简单方法。考虑一下列表中的数字计算平方的问题，函数是版本是比递归版本要简单得多的。
![9.JPG](http://upload-images.jianshu.io/upload_images/46495-df2ac09a85324495.JPG)
mapcar所做的事情不仅仅是处理输入的列表，并在结尾处停止，而且还把结果组合成一个列表。素有这些操作在递归版本中都必须明确作出处理。如果哦我们使用dolist来写一个迭代的版本的话，终结街侧竟会自动处理，但是我们仍然必须使用复制来构建结果。下面就是这样一个尝试。
![10.JPG](http://upload-images.jianshu.io/upload_images/46495-3bcfd6e44a5ba5be.JPG)
The function’s result is faulty: It’s backwards. This is typical for an
iterative solution. Since the function proceeds through the input list from left
to right, and pushes each result onto the front of the result list, the result list
ends up backwards. The square of the first number in the input list is the last
number in the result list, and so on. We can fix this by writing (REVERSE
RESULT) as the result-form of the DOLIST.
函数的结果是个作物，看上去是反过来的。这是一个迭代方案的典型结果。因为函数是从左到右进行处理的，那么在压栈的时候就会是倒过来的顺序。第一个数字的平方在结果列表中是最后一个，依次类推。我们可以再最后加上一个reverse来修正结果。
![11.JPG](http://upload-images.jianshu.io/upload_images/46495-6f425b8a7bd76c30.JPG)
如果你已经阅读过进阶话题章节的话，你会明白为什么有经验的lisp程序员在迭代函数的最后倾向于使用破坏性函数nreverse来替代reverse了。如果你跳过了那些章节，也不必担心。
###11.7 do宏
do是lisp中最强大的迭代形式，他可以绑定任何数字到变量中，就像let一样。也可以在多音变量里以你喜欢的方式步进任何值，并且允许你定义你自己的测试来决定什么时候离开循环。因为他太过强大，所以do的语法是稍微有点复杂的。
![12.JPG](http://upload-images.jianshu.io/upload_images/46495-37636c3d51caf546.JPG)
首先每一个在do的变量列表里的变量都被赋予了一个初始值，之后测试部分就会被求值，如果结果是true，do就会对终结操作求值然后返回最后一个的值，否则do就会春旭求值函数体。函数体中可能会包括return语句啦强制返回。当do到达整个函数体的最后的时候，就会开始下一个循环的迭代。首先，变量列表里的每一个变量都会被更新表达式来更新值。（更新表达式也可以被忽略，变量就会维持原有的值）当所有变量都被更新过后，终结测试就会再一次求值，如果返回true，do就会求值终结操作，否则就会再次求值函数体。
下面叫做launch的函数是用do来写的，请注意他只是用了一个索引变量cnt，他会从n递减到0。用dotimes来写launch也是可以的，但是会有一点难看，因为dotimes所因变量的步进是在一个“错误“的方向。 
![13.JPG](http://upload-images.jianshu.io/upload_images/46495-98d95f751d94570f.JPG)
下面是一个使用do来定义的count-slices函数的实现，（count-slices在第八章介绍过）这个循环使用两个索引变量。cnt是从0开始并备用在构建结果。Z的步进是loaf的后续rest。
![14.JPG](http://upload-images.jianshu.io/upload_images/46495-7939e99fbd9253f9.JPG)
这个Do函数的函数体是空的，所有的计算都在变量列表的表达式里面结束了。假设我们求值（count-slices ‘（x x））。当我们进入Do，cnt就初始化为0z就初始化为（x x）。之后来到终结测试，z不是nil循环就不会终结，函数体是空的，所以do就开始更新变量，cnt被设置成为（+ cnt 1），也就是加上了1，Z设置成为（rest z），就是列表（x），之后do再一次计算终结测试的结果，z依然不是nil，所以再一次迭代，这一次cnt的值成了2，z成了nil，终结测试也变成了true，被求值的表达式和被返回的循环终结结果是cnt，所以返回值是2.
###11.8 隐式赋值的好处
do相比dotimes和dolist有一些优势。你可以以你喜欢的方式来步进变量，比如以递减计数而不是递增计数。do可以通知绑定多个变量，这样子在do中建立一个变量列表的结果就变得很容易。也就是说，没有必要使用let加上显式的setf来实现了。下面是一个使用do来实现的阶乘函数版本。
![15.JPG](http://upload-images.jianshu.io/upload_images/46495-32413d03d278e73b.JPG)
这个版本的fact使用递减计数而不是递增计数字，并且使用了do的平行绑定属性，当开始计算（fact 5）的时候，i被初始化为5，result初始化为1.更新变量的时候，表达式（- I 1）求值为4，（* result I）求值为5.只有在所有表达式都求值结束，变量自身才会被改变；I被设置成为4，result被设置成为5。下一次精力循环，（- I 1）求值为3，等等以此类推，剩下的如下图；
![16.JPG](http://upload-images.jianshu.io/upload_images/46495-afa5ae5a5ff816b2.JPG)
count-slices和fact的函数体都是空的，这也是使用do最重要的理由。在变量列表中的更新表达式里就可以完成所有的隐式赋值工作，所以就不需要再写一个setf或者push了。这样风格的函数被认为是非常优雅的。
有些时候最好不要把所有的工作都丢给更新表达式。特别是当更新表达式中有条件式的时候。看看这个版本的it-intersection，没有函数体。
![17.JPG](http://upload-images.jianshu.io/upload_images/46495-8bbff06e2be7f98d.JPG)
由于do想要每一次都通过循环来更新result，但是我们想要的只是在在（first x）是y的一个元素的时候，值进行改变。是一个更简单的版本可以忽略在变量列表里的表达式，而是在函数体重设置一个条件push来实现。
![18.JPG](http://upload-images.jianshu.io/upload_images/46495-8e9ae3881ca851fe.JPG)
如果你想做的事情就是迭代列表中的元素，那么dolist回事比do更精练的方法，但是do是更加一般化的方法。例如，我们使用do来同事迭代多个列表，函数会比较从两个列表中的相对应的元素，一直到有两个相等的出现为止。例如下面的函数FIND-MATCHINGELEMENTS。
![19.JPG](http://upload-images.jianshu.io/upload_images/46495-9056af1900652b17.JPG)
###11.9 宏函数 Do *
下面是一个用do来实现的find-first-odd函数，按照一般惯例，变量x作为输入的rest的步进变量。在函数体内部，我们洗衣歌（frist x）来支出输入的元素。
![20.JPG](http://upload-images.jianshu.io/upload_images/46495-c7737d9f50e79ff7.JPG)
宏函数Do * 的语法和do是一样的，但是区别在于do*是逐个创建更新变量，就像let*一样，而不是像let一样一次性创建所有。在find-first-odd函数中使用do*的一个好处就是，他允许我们去定义第二个索引变量来保存列表的后续元素，第一个所用变量保存的是后续元素的cdr。
![21.JPG](http://upload-images.jianshu.io/upload_images/46495-74f068c9f2a88ae9.JPG)
请注意，索引变量E使用的初始值和更新表达式都是（first X），这样做的原因是，如果更新值被省略的话，E的值不会在每一次进入循环的时候改变。在do*的变量列表中，e出现在x之后是很重要的，因为e的值仰赖与x的值。
###11.10 do无限循环
把nil作为终结测试的话，do循环就会永远进行下去，对需要从键盘输入内容（比如数字）的函数来说，这是一个很有用的特性。如果用户键入除了数字之外的其他对象，函数就会打印一个错误信息，然后再次等待输入。如果用户却是输入了一个数字，函数就会离开循环，使用return返回那个数字，下面是例子。
![22.jpg](http://upload-images.jianshu.io/upload_images/46495-4eb237a674279f5c.jpg)
###11.11 隐式块（implicit blocks）
在common lisp中函数体是被包括在隐式块（implicit blocks）当中的，函数名字就是块名（blocks name），一个块就是一些表达式的序列，在这个序列里，可以使用return-form特殊函数来跳出这个块。在接下来的例子中，find-first-odd的函数体就是一个叫做find-first-odd的块。return-form的参数是一个块名和一个结果表达式，块名是不被求值的，所以不必加引号。
![23.JPG](http://upload-images.jianshu.io/upload_images/46495-8d7d0a2c6cc47add.JPG)
在这个例子中，我们使用return-form来跳出find-first-odd的函数体，而不仅仅是dolist的函数体。return-form从特定名称的最近的封闭函数体中返回。循环形式，诸如，dotimes，dolist，do和do*的函数体都是封闭的隐式块，名字是nil。表达式（return x）实际上仅仅是（return-form nil x）的简写形式。所以在find-first-odd的函数体中，return-form是嵌套在一个叫做nil的块当中，这个块被包含在一个叫做find-first-odd的块当中。
下面的例子中并不包含迭代，是需要return-form。函数square-list使用mapcar来对一个列表的数字进行操作。但是，如果列表中有元素不是数字的话，square-list并不会报错，而是返回字符nope。在lambda表达式内部的return-form跳出的不仅仅是lambda表达式，还有mapcar，还有square-list本身。
![24.JPG](http://upload-images.jianshu.io/upload_images/46495-5c22ea9ca26ea9ed.JPG)
除了包含函数体的隐式块之外，块也可能通过特殊函数block来进行显式定义。这个特性只在进阶应用里有使用，所以这里我们就不展开了。
###小结
dolist和dotimes都是最简单的迭代形式，do和do*的强大是因为可以同时步进多个变量，以及使用任意的更新表达式和终结测试。但是对于最简单的问题，像搜索列表中的元素之类的，dolist还是更精练些。
所有的迭代形式都会对他们的索引变量进行隐式赋值。这是最最感性的赋值类型；你不会再需要去写setf语句了，因为循环本身已经做好了赋值。有些时候，还是在循环体重使用显式赋值来构建结果更加好一些。特别是在像itinsection函数这种有条件式赋值的时候。
函数名也被用作隐式块的名字，我们因此可以使用return-form在函数体的任何地方跳出函数。
###本章涉及函数
迭代宏函数: DOTIMES, DOLIST, DO, DO*.
用于块结构的特殊函数: BLOCK, RETURN-FROM.
对已有匿名块使用的普通函数: RETURN.
###Lisp Toolkit: TIME
宏函数time告诉你需要多少时间来对表达式求值。也可能会告诉你在求值时候占用了多少内存，或者其他有用的信息。time具体提供什么以及用什么样的形式展现，根据lisp实现的不同而不同。在评估程序的小笼包的时候time是很有用的，例如，比较一个问题的两个解决方案，那个更快一些，或者看看一个函数在接收到一个大型输入的时候运行多慢。
![25.jpg](http://upload-images.jianshu.io/upload_images/46495-fe3bfba938ba5905.jpg)
###第十一章进阶话题
###11.12 PROG1, PROG2, 和PROGN
PROG1, PROG2, 和PROGN是三个非常简单的函数，他们都接受任意数量的表达式作为输入，然后一次性计算所有表达式。prog1返回的是第一个表达式的值，prog2返回的是子二个表达式的值，progn返回的是最后一个表达式的值，
![26.jpg](http://upload-images.jianshu.io/upload_images/46495-f55bce6090d835ac.jpg)
这些语句在今天已经不是很常用了，他们在早期的lisp版本中是很重要的，那时候，一个函数的函数体可以包含至多一个表达式，一个cond语句最多包含一个序列。
progn还有用的一个地方在于是在于一个if的真值部分和假值部分。如果你想要对一些真值部分或者假值部分的多个表达式求值，那么就必须用progn，block或者let将他们组合在一起。
prog1和prog2的效果是很容易用let来实现的，例如，（pop x）就等同于下面两个表达式。
![27.JPG](http://upload-images.jianshu.io/upload_images/46495-c64e55ef4ce33617.JPG)
时至今日，第二种一般被认为是更容易理解的形式。
###11.13 可选参数（optional argument）
common lisp函数可以被定义成接受可选参数的形式，关键字参数或者任意数量的参数，只要在参数列表中放置叫做lambda列表关键（lambda-list keyword）字的特殊字符。例如，在lambda列表字符串后面的变量就被称作可选变量。下面的函数接受一个参数x和一个可选的参数y。如果一个可选变量没有被提供的话，默认就是nil。
![28.jpg](http://upload-images.jianshu.io/upload_images/46495-2a44d95500660947.jpg)
可选参数没有被提供的话，不是一定要用nil作为默认值的。在lambda列表中，使用这样的形式（变量名 值），就可以用自己的定义替换默认值。下面的函数divide-check，divisor的默认值是2。（rem，由divide-check调用，是一个内建函数，返回一个数被另一个数除了之后额余数）。
![29.JPG](http://upload-images.jianshu.io/upload_images/46495-cae26b98a4483a76.JPG)
###11.14 REST参数
下面，列表关键字&restlambda之后的参数将会被绑定在一个函数的参数的列表上。它允许函数接受无限数量的参数，就像+和format那样。下面的函数接受无限数量的参数并返回他们的平均值。
![30.jpg](http://upload-images.jianshu.io/upload_images/46495-c4661fa5a3215a2a.jpg)
在使用&rest参数的时候，需要特别小心的地方是递归函数。在首次调用的时候，函数的参数是会被收集聚合成一个列表的，如果函数接下来递归调用自身，市容那个列表的cdr作为输入，就会出现一个列表的列表，而不是一个原始的列表，下面的例子，函数faulty-square-all就想要返回所有的参数的平方的列表。
![31.JPG](http://upload-images.jianshu.io/upload_images/46495-40aaacbf08615434.JPG)
我们可以使用apply来进行递归调用以修正这个问题。使用apply，（cdr args）的值就会被看做参数的列表来调用，而不是一个单独的参数。
![32.JPG](http://upload-images.jianshu.io/upload_images/46495-7f18f95e666301ad.JPG)
PROG1, PROG2, 和PROGN函数也可以使用&restlambda列表关键字来轻松定义。
![33.jpg](http://upload-images.jianshu.io/upload_images/46495-ac38ec8ad177ee41.jpg)
内建版本的PROG1, PROG2, 和PROGN函数杜宇穿件参数的列表没有干扰，因为他们只是需要返回一个值罢了。
###11.15 关键字参数
在之前章节的进阶话题中我们已经见过一些函数是接受关键字参数的了。例如member和find-if，例如，当你想要member使用equal作为等于断言的时候，你会这样写：
![34.JPG](http://upload-images.jianshu.io/upload_images/46495-330ab9e44f352740.JPG)
关键字参数在一个函数接受大量的可选参数的时候是很有用的。通过使用关键字，我们避免了去记忆那些可选参数的命令。我们要记住的只是他们的名字。你也可以通过用lambda列表关键字&key来创造你自己的接受关键字参数的函数，比如&optional，就可以替换默认值。下面的函数make-sundae就接受超过6个关键字参数。
![35.jpg](http://upload-images.jianshu.io/upload_images/46495-b0ead7735f0f0e90.jpg)
像：cherries这种关键字都是求值为自身的，这也是为什么他们没有引号。请注意我们调用make-sundae的时候，使用关键字：cherries，但是在参数列表中还有makesundae的函数体中，我们只是使用了普通字符cherries。这是一个很重要的区别。在makesundae内部，cherries仅仅是另一个变量。唯一一件特殊的事情是他得到这个值的方式。只有&rest变量会被特殊对待，用&key定义的变量都会用一个特别方式得到值；当调用make-sundae的时候，我们通过使用：cherries关键字接续上一个值来给cherries定义一个值。
###11.16 辅助变量（auxiliary variables）
lambda列表关键字&aux被用于定义辅助本地变量。你可以仅仅定义一个变量名，初始值回事nil，或者你可以使用一个列表的形式（变量 表达式）。在后面的表达式会被求值，然后结果会作为变量的初始值。下面例子中的辅助变量len就用来保存列表的长度。
![36.JPG](http://upload-images.jianshu.io/upload_images/46495-ab88e30aeec7ce3a.JPG)
关键字&aux完成的事情是和特殊函数let*相同的；都是使用逐个绑定来创造变量。选择使用哪个纯粹是个人口味问题。
###进阶话题涉及函数
PROG1, PROG2, PROGN
Lambda列表关键字: &OPTIONAL, &REST, &KEY, &AUX.
