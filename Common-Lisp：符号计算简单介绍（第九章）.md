#输入/输出
###9.1 导语
输入/输出，或者说i/o，是计算机和世界交流的一种方式。lisp的read-eval-print循环就提供了一种简单的i/o。也就是从键盘读入表达式，在显示屏上输出结果。有时候我们想要做更多的事情，使用本章介绍的i/o函数，你可以创造你喜欢的程序打印信息。你甚至可以打印出问题，等待用户输入回答。
i/0函数的另一种使用就是从磁盘文件中读入数据，或者写入一个文件以便你某一天在使用读出。使用common lisp来做这些要比其它语言方便很多。
历史上来说，输入输出是在lisp系统中最具争议的一部分。甚至今天也没有一个标准的窗口桌面系统，例如。还没有一个标准的鼠标控制或者图形设计。每一个lisp供应商提供他们自己的工具，幸运的是，最基础的i/o工具最终被标准化了。本书会专注在基础部分。
###9.2 字符串
为了让计算机在显示屏上打印有用的信息，我们必须首先学习字符串（character strings），字符串（string）是一类序列的类型。在某些方面和列表时相似的，他是一个向量的子类型（将在第十三章讨论），但是他们有一个不同的原始操作集合。
字符串求值为自身，就像数字一样。请注意接下来的例子中，字符串不会转换成全大写。字符串（string）不是字符(symbol),stringp断言来判断输入是不是一个字符串。
![1.JPG](http://upload-images.jianshu.io/upload_images/46495-387a5108b8342602.JPG)
如你所见，字符串必须由双引号括起来，和单引号的引用字符和列表是不同的。两个单引号并不等于一个双引号，这里必须使用双引号。
###9.3 format函数
正常情况下，format函数会返回nil，但是它引起的副作用就是会把一些信息显示在显示屏上或者写入文件中。如果我们想要输出在显示屏上，format的第一个参数应该是字符T。（写入磁盘的时候会使用不同的字符）。第二个参数必须是字符串，叫做格式控制字符串。format函数输出一个没有引号的字符串，然后返回nil。
![2.JPG](http://upload-images.jianshu.io/upload_images/46495-26e45c8d4615779c.JPG)
格式控制字符串也可以包括特殊格式指令，是由一个曲线开始的~,例如"~%"指令就是让format函数重新开一行。两个~%指令就可以在输出中加一个空白行。
![3.JPG](http://upload-images.jianshu.io/upload_images/46495-b3570a1514a629bf.JPG)
~&指令的作用是新开一行，除了已经是新一行的情况下就不新开了。所以两个或者三个连续的~&指令和一个~&指令的效果其实是相同的，这个指令的用处在于，在函数调用的时候，我们不是一直清楚光标所处的位置在哪里。举个例子，一些common lisp实现要求用户在结束输入的时候必须输入一个换行，其他的实现回去并不要求。所以在format被调用的时候，光标会处在一个不同的位置，这个位置取决于用户是不是一定要换行。
在输出多行的程序中，每一个格式控制字符串的前面都加上~&指令是一个好的习惯。这样光标就肯定是在一个新行开始打印信息。
![4.jpg](http://upload-images.jianshu.io/upload_images/46495-cfdfc82f262904b3.jpg)
另一个很重要的格式化命令就是~S，用处是在lisp对象的打印输出中插入格式化信息。对于每一个在格式控制字符串中~S出现的地方。format需要一个额外的参数，在接下来的例子中，第一个~S指令被符号boston所替代，第二个~s指令被列表（new york）所替代，第三个~s由数字55替代。
![5.JPG](http://upload-images.jianshu.io/upload_images/46495-9901d1e07dd1e8d3.JPG)
下面还有另一个例子，函数square-talk接受一个数字作为输入并且回答这个数字的平方。他其实并不返回平方，返回的是nil因为他是format的返回值。
![6.JPG](http://upload-images.jianshu.io/upload_images/46495-6b2defc7356f0b38.JPG)
mapcar返回的结果是一个nil的列表，因为每一个square-talk的返回值是nil。
~A指令打印一个没有使用转义字符的对象，这是最简单的比较~A和~S指令的方式，~S包括了引号，~A没有包括，一个引号就是一种转义字符。
![7.JPG](http://upload-images.jianshu.io/upload_images/46495-e907e118acd74963.JPG)
###9.4 read函数
read函数是从键盘读取一个lisp对象的函数（一个数字，符号，列表或者其他），然后返回那个对象的值。对象不是一定要被引用因为他不会被求值。通过在函数内部调用一个read函数，我们可以使得计算机在程序控制下从键盘读取数据。下面的一些模板，回应read函数的用户输入是加上下划线的。
![8.JPG](http://upload-images.jianshu.io/upload_images/46495-2b7234ec94031aa9.JPG)
###9.5 yes-or-no-p函数
yes-or-no-p函数接受一个格式控制字符串作为输入，并要求用户回答一个是还是否的问题。用户必须键入yes或者no来回应，相对的返回T或者nil。
![9.JPG](http://upload-images.jianshu.io/upload_images/46495-ab28e89900e4d36d.JPG)
这个函数有一个更简短的版本，叫做y-or-n-p，只需要用户输入y或者n作为回答。
###9.6 使用with-open-file来读取文件
with-open-file宏函数提供了一个方便的方式来访问文件。它的语法是这样的：
![10.JPG](http://upload-images.jianshu.io/upload_images/46495-05c412d62cca96fc.JPG)
with-open-file像let一样，创建本地变量，然后设置成一个流对象来表示和那个文件的连接。流对象（stream object）是个特殊的lisp数据类型，专门用来描述和文件的连接。如果你想要看看效果，请看全局变量“terminal-io”的值。它的值是lisp从键盘读入和输出到显示屏的流对象。
![11.JPG](http://upload-images.jianshu.io/upload_images/46495-acbde9a78ff90571.JPG)
在with-open-file函数体内部，流对象可以作为一个read函数的可选参数，从文件输入而不是键盘输入。在离开with-open-file函数后，文件的连接会自动关闭。
我们来看一个从文件读取数据的例子。假设文件“timer.bat”在目录/usr/dst下面，内容如下：
![12.JPG](http://upload-images.jianshu.io/upload_images/46495-36fd2360c7a81816.JPG)
我们可以用下面的函数读取数据：
![13.jpg](http://upload-images.jianshu.io/upload_images/46495-dafebcc09212496a.jpg)
###9.7 使用with-open-file来写入文件
我们也可以用特殊关键字来调用with-open-file来打开文件输出，关键字参数就是：direction和：output。with-open-file创造的流可以再format函数的参数位置使用。
![14.JPG](http://upload-images.jianshu.io/upload_images/46495-1386cfc7ea22a316.JPG)
如果我们只是使用~S指令来向文件写入数据，我们确保可以再一次读出来。当然写入文件的信息可以使任意的，包括特殊标点，不成对的括号或者任何字符，但是我们将不可以用Lisp的read将他们读回来了。这样的文件仍然是有用的，因为他可以被人所读懂。
###小结
format函数接受两个或者更多的参数，第一个参数是T的话就会打印到屏幕上，第二个必须是一个格式控制字符串。余下的蚕食是用来填补字符串中的指令~S的。指令~%的作用是开一个新行。~&指令是没新行开新行，有新行不开。
read函数从终端读取一个lisp对象并返回那个对象。这个对象不是一定要背引用因为他不会被求值。yes-or-no-p和y-o-n-p打印问题（使用格式控制字符串）然后返回T或者nil来判断输入的答案。
with-open-file打开一个文件来进行输入或者输出，并且绑定一个变量到流对象来表现和文件的连接。这个流对象可以被传送到read或者format来进行文件i/o。
###Lisp Toolkit:  DRIBBLE
dribble函数将一个lisp过程的一部分记录在文件中，如果你想要吧交互过程打印出来进行展示，那这个工具会很有用。将一个文件名作为参数，dribble打开文件输出然后开始记录。如果调用不含参数，他会在记录过后关闭文件。
![15.jpg](http://upload-images.jianshu.io/upload_images/46495-20ef8e3a7e6881b0.jpg)
###第九章进阶话题
###9.8 format指令的参数
一些format指令接受前缀参数来更加详细定义他们的行为。前缀参数出现在波浪号~和指令之间。例如，指令~S接受一个宽度参数，来详细定义打印宽度，像这样~10S，我们就可以进行分栏输出。
![16.jpg](http://upload-images.jianshu.io/upload_images/46495-8bac187d0e4c14a2.jpg)
###9.9 附加的format指令
指令~D的作用是以十进制方式打印整数。当然打印成其他进制也是可以的，甚至可以打印成罗马数字，但是这里我们不讨论这个。指令~F是用来打印浮点数，格式是固定包括一个小数点。所有这些指令都接受前缀参数，我们第一个使用的参数是用来定义输出的字符宽度：输出应该占到多少个字符宽度。（Lisp胡勇空格来填满不够的地方）。我们会接触另一个前缀参数，和~F指令一起使用，第二个参数定义了在小数点后面具体打印多少位。例如，~7,5F就是定义打印有7个字符宽，小数点后面是5位。
![17.JPG](http://upload-images.jianshu.io/upload_images/46495-81595034d9586325.JPG)
###Lisp1.5中的输出原始函数
原始的输入输出函数如TERPRI, PRIN1, PRINC, 和PRINT都定义在Lisp1.5中（所有现代Lisp系统的祖先）并且今天仍然可以在Common Lisp中使用。他们作为一个历史记录出现在进阶话题中；你可以用format函数实现相同的效果。terpri是终端输出terminal print的缩写。他将光标移动到新行输出，prin1使用任何需要的转义字符来打印对象，以确保可以被read函数读取。princ函数打印没有转义字符的对象。基本上，~S格式化指令的幸会类似于prin1，~A的行为类似于princ。prin1和orinc都返回他们的第一个参数。
![18.JPG](http://upload-images.jianshu.io/upload_images/46495-b7a9fdf04a246d40.JPG)
print函数是先前三个函数的组合，他会使用terpri开始一个新行，用prin1打印它的参数，然后用princ打印空格，一个简单版本的print定义如下。
![19.JPG](http://upload-images.jianshu.io/upload_images/46495-3f22b1394cd63627.JPG)
terpri,prin1和princ都接受一个可选的流参数，这就允许他们可以在文件i/o上使用。
###9.11 处理end-of-file条件
有时候读取一个不知道包含了多少对象的文件是很必要的，当你的程序读取到文件的结束的时候，下一个read函数就会生成end-of-file错误，你可以在debugger中结束。在遇到end-of-file错误的时候，告诉read函数不要报错，而是返回一个特殊值也是可以的。我们要做的就是使read函数支持两个额外的参数：一个是NIL（意思是不要生成一个错误），还有就是你想要用来表示eof的值。我们咋选择这个值的时候必须小心。如果我们选了一个比较常用的，比如FOO，那程序中如果包含这个值，那程序就会以为已经到了文件末尾了。因此，一个好的eof表示字符是一个新生成的内存单元，我们会使用eq而不是equal来确保内存单元被返回。
下面的程序实例是读取任意lisp对象的文件，告诉我们有多少单元被读取，并返回一个列表。它使用的是内存单元（$eof$）作为特殊的end-of-file值，任何新生成的内存单元都可以做这个标记，重要的是哪个标记的地址，而不是标记的内容。
![20.JPG](http://upload-images.jianshu.io/upload_images/46495-f139ac4e323413a4.JPG)
输入的数据如下：
![21.JPG](http://upload-images.jianshu.io/upload_images/46495-888a9ac4f5d85a38.JPG)
最终结果如下：
![22.JPG](http://upload-images.jianshu.io/upload_images/46495-dba98ee8fd624af4.JPG)
###9.12 用点式标记打印
点式标记是一个内存单元表达式的变种，在点式标记中每一个内存单元被表示成一个左括号，car部分，一个点，cdr部分，和一个右括号。car和cdr部分，如果是列表的话那么他们本身回事点式标记，使之成为一个地规定义。例如，列表（A）被表示成一个内存单元，单独的car字符A和cdr，nil。在点式标记中这个列表被写成（A . NIL）。下面是更多例子：
![23.JPG](http://upload-images.jianshu.io/upload_images/46495-61269d0499bdb18d.JPG)
###9.13 混合标记
lisp正常情况下用列表标记打印，而不是点式标记。但是我们也发现，一个内存单元格式，如（A . B）是不能够用没有点的标记来表示的。lisp的原则就是只在需要的时候打印点。除非内存单元链条的最后是一个非nil的接轨，不然点是不打印的。输出是点式打印和纯列表打印的混合的话，就成为混合标记。下面的例子来区别。
![24.JPG](http://upload-images.jianshu.io/upload_images/46495-f271a777f8dff91f.JPG)
###进阶话题涉及函数
Lisp1.5原始输出函数：TERPRI, PRIN1, PRINC, PRINT
