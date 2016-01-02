#第十四章 宏和编译（Macros and Compilation）
###14.1 导语
宏函数，缩略，宏。是扩展lisp语法的一种方式。在本章我们会使用求值回溯图和一些叫做ppmx的小工具（定义在lisp toolkit章节）来看看宏是如何工作的。会有一些材料需要参考前面的进阶话题章节。如果你还没有看过的话，我会标明去哪里看的。
在本章的后半部分我们会学习一下编译（compilation）。如果你觉得你的程序中有一部分运行的太慢，你可以将他们编译一下来让他快一些。编译器将lisp程序翻译成机器语言程序，这样他的执行速度会有10倍到100倍的提升。
###14.2 宏就是缩写法
在计算机中的宏实际上就是缩写法，任何你想要写成所写的东西都可以用更加详细的语言来表示，只是会更长一些。相似的Common Lisp宏不会让你写出任何用一般函数不能表示的功能。但是他确实会帮你把功能写的更加精炼。incf函数就是一个好例子，（incf a）肯定比（setf a （+ A 1））写起来要精炼的多吧。
有一些宏是非常精妙的，特别是像setf和incf这种一般赋值宏，他可以将任意的复杂位置描述解释成一般的变量引用。
![1.JPG](http://upload-images.jianshu.io/upload_images/46495-90ae69d4ac609556.JPG)
当上面表达式里的位置描述被解析出来的时候，你会惊叹incf的强大能力。
宏可以用简单的指令生成复杂的程序。比如destruct宏，可以讲starship的结构定义转化成一个指令流来支持starship的数据类型。这个指令流包括了make-starshiphe starship-p的函数定义，还有所有组件的访问函数。当然所有的工作并不只是包括函数定义，defstruct的输出取决于具体实现的定义。例如，starship整合进Common Lisp类型继承机构的方式就是每一个实现都是不同的。defstruct包含的函数和变量都不是Common Lisp标准的一部分，甚至不会被lisp供应商写进文档里。defstruct宏允许lisp供应商提供一个协议来对消费者隐藏具体细节，定义结构体的标准方式取决于具体的Common Lisp实现。
###14.3 宏展开
如果你用缩略法写了一些东西的话，为了便于理解和使用，你终归是要把他展开的。lisp会把宏调用自动展开，一个宏实际上就是一种特殊的缩略法，展开宏函数的时候并不会对它的参数求值。展开的工作时寻找它的参数还有生成一个lisp可以求值的表达式。在表达式（incf a）中，宏incf被调用，参数就是A（不求值）。他会构建一个表达式，（setq a （+ A 1）），之后返回。incf准确构建的表达式取决于具体实现，但是看上去应该是setq。lisp之后会对表达式求值，然后对A的值加上1。
回忆一下我们在10.10章节讲的特殊函数setq，当setf给一个普通变量赋值的时候，实际上是在内部调用setq函数。这个setf宏函数实际上是展开成了一个setq函数的调用。
在求值回溯图中，宏展开使用一条虚线来表示，宏返回的表达式会被正常求值。
![2.JPG](http://upload-images.jianshu.io/upload_images/46495-b7f7ca80dee63fec.JPG)
如果你想要看一下计算机内部的宏展开，你可以使用一个叫做ppmx的小工具，定义在本章节的lisptoolkit单元。ppmx这个名字是pretty print macro expansion（吐槽：漂亮滴打印展开啊喂！）的缩写，一些lisp实现也会有打印展开的工具。
![3.JPG](http://upload-images.jianshu.io/upload_images/46495-f03b38d8430aec54.JPG)
在一些lisp实现中，incf的宏展开是不一样的，例如，incf可能会展开成一个let表达式来创造一个本地变量保存（+ A 1）的值，然后再重新传递给A。看上去这好像并不是一个直接的方法来给a加1.但是请记住，incf的目的是更加复杂的结构也能进行增值操作，在哪些情况下，let或许是一个更必要的选择。
![4.JPG](http://upload-images.jianshu.io/upload_images/46495-393f4223bf8587f3.JPG)
在上面的例子中，#:G0144是一个内部符号，叫做内生变量（gensym（查了一下，不是英语单词，是造的词，专家系统的意思）），是由incf函数自动生成的变量，作为一个本地变量名使用。内生变量确保不会和你已有的任何变量名产生冲突。出于我们不需要了解的理由，#:G0144和符号G0144是不同的，你不能从键盘输入这样的符号，所以他也不可能和你的变量名冲突，甚至你特意选择G0144作为名字。
###14.4 定义一个宏
宏是由defmarco来定义的，它的语法和defun类似。我们来定义一个给普通变量加上1的incf的简单版本。我们红会接受一个变量名作为输入，冰鞋构造一个增量表达式。
![5.JPG](http://upload-images.jianshu.io/upload_images/46495-a1b8be56fb919e9e.JPG)
另一个看simple-incf如何工作的方法就是用dtrace来追踪他的行为。（如果你使用的标准的trace而不是dtrace的话骂你可能不能追踪宏。）
![6.JPG](http://upload-images.jianshu.io/upload_images/46495-62f72b5b0964e047.JPG)
追踪你自己写的宏是没有问题的，但是在一些lisp实现当中最好不要追踪那写重要的内建函数，比如setf。如果追踪这些函数出现了问题，换成ppmx试下。使用ppmx的好处之一就是宏展开的结果只是被打印，但是不会被求值。ppmx允许你使用任意表达式来尝试宏展开而不用担心会发生错误。
现在我们来修改一下simple-incf，可以接受第二个参数，定义增值的幅度。我们使用lambda列表关键字&optional来实现。（可选参数在章节11.13里有解释过）对变量默认的增值数字是1。
![7.JPG](http://upload-images.jianshu.io/upload_images/46495-6b7314e6577e726a.JPG)
宏不会对它的参数进行求值，所以simple-incf的输入是字符b和列表（* 3 A），不是数字2和15.求值回溯图展示了simple-incf如何计算出宏展开，之后lisp才求值。
![8.JPG](http://upload-images.jianshu.io/upload_images/46495-35da4ed33119e1d6.JPG)
现在我们来思考一下，为什么incf一定要是一个宏而不是一个函数呢？假设我们顶一个函数incf，用defun定义，就叫他faulty-incf好了：
![9.JPG](http://upload-images.jianshu.io/upload_images/46495-51bc7470da293564.JPG)
既然faulty-incf是一个函数，那么他就会对它的参数求值，并且也没有返回一个表达式给lisp求值的打算，仅仅是自己把事情给办了。但是它的参数已经被求值了之后，就会出现一个问题。
![10.JPG](http://upload-images.jianshu.io/upload_images/46495-fb42c6d6671ff7e3.JPG)
faulty-incf的输入是7，他创造了一个叫var的本地变量，然后将输入存储进去，之后再将var的值加上1.他对变量A根本就没有概念，因为在函数被输入之前，参数就应被求值了。
![11.JPG](http://upload-images.jianshu.io/upload_images/46495-0fc0692cc036bf23.JPG)
我们可能尝试在变量a被传给faulty-incf的时候给他加上一个引号，当然必须是要更改faulty-incf的定义，因为输入不再是一个数字。但是因为某些理由（会在进阶话题里解释），这也是不起作用的。simple-incf必须被写成一个宏，这并不和我们先前说的宏是缩略法的说法冲突。我们任然可以自由使用setq表达式来代替simple-incf。setq是一个特殊函数，不是一个宏，他们之间的区别会在下一节解释。
###14.5 宏是语法扩展
既然宏的目的是扩展语言本身的语法，lisp就不会像对待普通调用那样对待宏调用。在普通调用和宏调用之间有三个重要区别。
1. 普通函数的参数请示被求值的，宏函数的参数不被求值。
2. 普通函数的结果可以使任何东西，宏函数返回的结果肯定是一个合法的lisp表达式。
3. 宏函数返回一个表达式之后，表达式是马上被求值的，普通函数返回的结果是不被求值的。

除了宏之外，common lisp还包括了一部分特殊函数，比如说setq，if，let，和block。特殊函数是构建Common Lisp的最底层模块，他们主要负责像赋值，作用域，基本控制结构，块和循环。类似于宏，特殊函数并不对他们的参数求值，但是他们也不返回被求值的表达式。他们是做很特殊的事情的原始函数。你不能写一个新的特殊函数，只有Lisp实现者才可以。
回到我们关于宏就是缩略法的讨论。我们会说任何宏可以做到的事情，没有了宏只用普通函数和特殊函数等等的组合也可以做的到。
###14.6 反引号字符
函数simple-incf通过两个list调用，还有一些引号字符，加上变量var和amoun的值构造了一个lisp表达式。在表达式规模比较小的时候，这个方法是可以的，但是当宏函数必须生成庞大复杂的表达式的时候，一个个构造就显得很麻烦了。我们所需要的是一个替代的方法来实现写一个表达式宏的模板。止呕所有的宏都必须由空格填满，反引号就是起那个作用。
反引号字符（`）和引号是相似的，他们都被用在引号列表中。然而，在一个反引号列表中,前导逗号的存在作为表达式没有引用的标记，意思就是表达式的值而不是表达式会被使用。
![12.JPG](http://upload-images.jianshu.io/upload_images/46495-ddb8cb49f62302a5.JPG)
我们可以使用反引号表达式来写一个simpleincf的更加精细的版本。
![13.JPG](http://upload-images.jianshu.io/upload_images/46495-53f1d71c8b7136ee.JPG)
宏的一个很普遍的用法就是去避免引用参数。宏使用需要的参数的引用版本来展开成一个普通函数调用。你可以使用反引号去生成表达式，并且在其间夹杂引号，如下面的模板。
![14.JPG](http://upload-images.jianshu.io/upload_images/46495-3f5f1d33efd60267.JPG)
在下面的例子中，two-from-one宏函数接受一个函数名和另一个对象作为参数；他会展开成一个双参数的函数调用，两个对象都是被引用的。
![15.JPG](http://upload-images.jianshu.io/upload_images/46495-96fb552a9f7ad888.JPG)
我们在object 之前放上一个逗号的原因是，我们希望变量的值被插入进反引号生成的列表里。如果我们拿掉引号，宏展开就会变成(CONS AARDVARK AARDVARK)，这会引起一个未赋值变量错误，除非AARDVARK本身具有一个值。如果我们拿掉逗号，宏就会展开成(CONS ’OBJECT ’OBJECT).
我们来尝试一个更加发杂的反引号模板，我们会写一个宏showvar来展示变量的值。
![16.JPG](http://upload-images.jianshu.io/upload_images/46495-6c4aa7723cece425.JPG)
showvar必须定义成宏的原因是，他必须知道要展示变量的名字，不仅仅是知道变量的值。让我们来把问题细化一下。关于X的值的信息会在接下来的表达式中被打印出来，请注意只由X的第一个实例被引用了。
![17.JPG](http://upload-images.jianshu.io/upload_images/46495-5d344ef37c1d8982.JPG)
我们现在可以简单的将showvar宏需要的部分抽象出来。引号和逗号的组合看上去是有点奇怪，但是你可以看看在接下来的例子中引号的位置在哪里。
![18.JPG](http://upload-images.jianshu.io/upload_images/46495-ef407df1ab39db6d.JPG)
###14.7 用反引号连接
反引号的另一个特性就是如果一个模板元素的前面是一个逗号（,）和一个at符号（@），这个元素的值就会被连接到反引号构造的结果中，而不是插入进去。（元素的值必须是一个列表）。如果只有一个逗号被使用的话，这个元素会作为一个单个对象被插入，导致一个附加的括号层次。
![19.JPG](http://upload-images.jianshu.io/upload_images/46495-c5019d36cc88d0c9.JPG)
下面表示的在哪里连接的例子是很有用的，宏函数set-zero，接受任意数量的变量作为输入，他会展开成一个表达式来设置每一个变量的值，为0，然后返回一个消息作为结果。因为宏必须导致一些行为但是也可以只是返回一个值，他用progn将一些行为组合进单个表达式里面。
![20.jpg](http://upload-images.jianshu.io/upload_images/46495-d31c8d36cbc16612.jpg)
这就是set-zero的定义，它使用mapcar来给每一个参数列表中变量构建一个setf表达式。setf表达式之后就会连接在progn函数的后面。还有，在progn函数体中的最后一个表达式是一个用连接构造的引用的列表。如果只是一个逗号，而不是一个逗号个at符号的组合的haunted，结果就是(ZEROED (A B C))。
![21.JPG](http://upload-images.jianshu.io/upload_images/46495-888123eda9607742.JPG)
###14.8 编译器
编译器将lisp程序翻译成机器语言。这会使得程序运行的更快，一般是快10到100倍。作为一个初学者你可能不会写一个很大规模的程序，所以速度并不是首要的考虑因素。然而，当你着手处理更加高层次的问题，你就会开始关心程序的速度和内存占用率等等了。编译就可以优化这两个因素。
有两种方式可以使用编译器，你可以用compile函数编译一个函数，挥着使用compile-file来编译整个文件。很多面向lisp的编辑器都提供只用一次或者两次击键的方式来调用编译器，所以你可能永远都不会有机会显式调用这些函数了。
我们来看一下compile函数在运行时编译一个简单函数的结果。函数返回大于输入的平方最小整数。这个函数以一种非常冗长的方式计算出了结果，但是编译会让速度优化很多。
![22.JPG](http://upload-images.jianshu.io/upload_images/46495-4bff34fb7c5095a5.JPG)
我们看到五百万的平方根是在2236到2237之间。我们也看到tedious-sqrt的解释版本花费了大约0.95秒钟来运行。你可能想要选一个更加小的参数，如果你的机器是比较慢的话。现在，让我们尝试编译tedious-sqrt来看看编译版本有多快。
![23.JPG](http://upload-images.jianshu.io/upload_images/46495-010e8ab9e48daada.JPG)
编译版本滑了0.3125秒运行时间，比解释版本快30倍。解释版本占用了查过50K字节，但是编译版本只用了32字节。在特定的实现中，占用是因为&rest在收集参数的时候对*函数的使用。每次生成一个列表这个函数都会被调用。编译器会把对*函数的调用编译成机器指令，之后就抵消了函数调用所造成的损耗。
###14.9 编译和宏展开
common lisp标准允许宏调用被他们的宏展开在任何时候替换。在一些lisp实现中defunct函数会进行宏展开，在其他实现中第一次替换是会被求值的。在简单的实现中一个宏调用可能一直不会被结果展开给代替。而宏会每一次再展开然后求值。
既然宏展开会在任何时候发生，你不应该写一个产生副作用的宏，比如赋值或者i/o。但是宏展开成产生副作用的表达式是可以的。
![24.JPG](http://upload-images.jianshu.io/upload_images/46495-d997706430d57c3b.JPG)
在上面的例子中，宏被展开成编译say-hi函数的一部分过程，所以编译器会说“hi，mom”，宏的计算结果是nil，效果是被编译到say-hi函数体中的表达式，没有任何表示是因为宏已经被表达式替换了。问题可以通过制造返回format的表达式来解决。
![25.JPG](http://upload-images.jianshu.io/upload_images/46495-9528ff8d95a83049.JPG)
###14.10 编译整个程序
当你编译整个程序，他会被存储在一个文件里面。你可以适应compile-file函数操作这个文件。一些lisp编辑器允许你使用编辑器命令来做这件事。他们可能也允许你去编译一个编辑器缓冲的内容而不需要他是一个文件。请查阅你的用户手册。
由于编译器的工作方式，你可能需要在阻止你的程序的时候遵守一些简单的规矩。如果你不注意这些规则的话，编译器可能会爆出错误信息然后不再正确编译你的程序。
首先，如果你的程序使用了任何全局变量，编译器可能会爆出一个警告信息，说变量是“假定为特殊变量（assumed to special）”。特殊变量将会在进阶话题中解释，你可以忽视警告信息，使用defvar重新声明变量。
第二，如果你的程序包含了宏，宏定义必须是直接存在与文件中。如果函数foo调用一个宏bar，lisp可能无法认识到当编译foo的时候需要将bar的调用看做一个宏调用来展开。如果foo已经被不正确的编译了，发现bar是一个宏的时候，大部分编译器将会报出警告。
第三，如果你的程序冲定义了任何内建函数，编译器不会正确编译他们。确保使用和内建函数不冲突的名字。在线文档会帮助你来判断。
###14.11 案例学习：有限状态机
有限状态机（Finite state machine FSM）是一种来自理论计算机技术的，用来描述一种简单的机器，比如售货机或者交通指示灯工作状态的技术。在这一节我们会写一个有限状态机模拟器来显示真实的lisp程序是如何被开发出来的。为了讨论的更加具体一些，我们会聚焦在一个特定的机器来进行模拟，但是我们的虚拟机将会为任何有限状态机工作。
假设一种售货机只出售两种商品：口香糖和薄荷糖。口香糖要15美分，薄荷糖要20美分。任何5分和10分的组合可能被用来操作这个机器；他会自动改变组合。如果提供了足够的钱，按下相应键会出现相应的商品，任何时候按下退款键就会返回剩下的钱。
这个机器一开始初始化成一个状态叫做start，如果输入的是字符nickel，他会打印“clunk”并且转移到叫做have-5的状态。如果实在have-5的状态并且取得了dime作为输入，就会打印“clink”,然后转入状态have-15。在have-15状态如果输入gum-button，就会输出一包口香糖（gum）,然后进入状态end。
状态机总共有六个状态：start,have-5,have-10,have-15,和have-20，还有end。（之所以被称作有限状态机的原因就是他的状态时有限的），每一个状态都表现为一个节点，从一个状态到另一个状态之间的转换都是一个箭头。状态都需要标记成一个一个转换的机器动作，例如，从have-10到have-15的转换就被标记为nickel。
![26.JPG](http://upload-images.jianshu.io/upload_images/46495-3981a72155ebb965.JPG)
在售货机的全部定义中，defnode宏和defarc宏提供了简单的语法来定义有限状态机一部分。下面有限状态机模拟器中你可以看到工作的模式和目标。
![27.JPG](http://upload-images.jianshu.io/upload_images/46495-78c8ba83d24f63fc.JPG)
从定义节点和状态的结构体开始，我们来构造我们虚拟机。，诶一个节点都有自己的名字，自己状态输入的列表，还有输出状态的列表。每一个状态都有一个from节点和一个to节点，一个标记和一个动作。我们也定义了打印函数。
![28.JPG](http://upload-images.jianshu.io/upload_images/46495-f389ac7999b15366.JPG)
现在我们需要一个全局变量nodes来保存包含机器节点的列表，还有全局变量arc来保存状态的列表。另一个变量，“current-node”，来保存机器状态的记录。用defvar来声明这些变量，哦我忙呢会在进阶话题里讨论这个。函数initilize将这些变量设置成nil。
![29.JPG](http://upload-images.jianshu.io/upload_images/46495-864ae3f5e900118a.JPG)
宏defnode是用来定义新节点的状态包裹（syntactic sugar）。他在参数前面加上一个引号来调用addnode函数。
![30.JPG](http://upload-images.jianshu.io/upload_images/46495-4a485910d373a951.JPG)
add-node构造一个新的节点，给这个节点一个名字，把这个节点加入到全局变量nodes中。因为它使用nconc（append的破坏性版本）所以可以再列表的最后加上一个节点。这确保了在nodes中的几点会以defnode定义的顺序出现。而不是相反的顺序。add-node也会返回新建的节点。
![31.JPG](http://upload-images.jianshu.io/upload_images/46495-09e7ffeff92e5c11.JPG)
FIND-NODE函数接受一个节点的名字作为输入，然后返回一个对应的节点如果对应节点不存在，那么就会报错 。
![32.jpg](http://upload-images.jianshu.io/upload_images/46495-20323e85528a6b01.jpg)
宏defarc提供了一个方便的定义状态的语法，并且函数add-arc做一些实质上的工作。当一个状态呗创建，他被加入node-ooutputs列表还有node-inputs列表。也不会被加入保存全局变量arcs的列表。
![33.JPG](http://upload-images.jianshu.io/upload_images/46495-496f3e26dc3d02a0.JPG)
现在我们可以写一个顶层函数fsm。他会接受一个可选的输入来定义状态机的初始状态。默认的初始状态是start。fsm反复调用函数one-transtation来转移到下一个状态。当状态机到达一个没有输出的状态（比如end），就会停止，请注意do是没有一个空的变量列表的。
![34.JPG](http://upload-images.jianshu.io/upload_images/46495-8ca5c427fd0647d1.JPG)
最后，我们来写一个one-transition函数。他会提示输入并且通过改变current-node的值来创建合适的状态。如果从输入无法给出一个合法的状态的话，就会打印一个错误消息并且提示再一次输入。
![35.JPG](http://upload-images.jianshu.io/upload_images/46495-442f6a217998346a.JPG)
我们的模拟器不仅仅限制在模拟售货机。任何可以被描述为有限的状态，而且状态可以互相转换的设备都可以用这个程序模拟。
###小结
宏是一种很有用处的lisp缩略法。它允许程序员来定义lisp的语法扩展，并且将功能实现的更加精炼。他也会帮助供应商向消费者隐藏大量细节的实现定义。宏不会对参数求值；他们返回会被lisp求值的表达式。新的宏可以被defmarco定义。
就像宏一样，特殊函数不会对输入求值。但是和宏不同的是，他们不会返回lisp表达式。特殊函数提供lisp创建的原始函数，比如赋值，条件是和块结构。
反引号字符从模板中构造一个列表。如果模板元素的前面是一个逗号，他就会被求值。也就是会被插入进列表的值。前导逗号和at符号的元素会被连接在列表的后面。反引号在宏中式特别有用的，可以在模板中加入空格来构造特别复杂的表达式。
###本章涉及函数
宏定义：DEFMACRO.
编译器: COMPILE, COMPILE-FILE.
###Lisp Toolkit: PPMX
ppmx是pretty print marco expansion的缩写。他的宏展开第一个参数（不求值）并且打印其余的参数。ppmx不仅对学习内建宏，诸如setf十分有用，也会对调试自己写的宏有帮助，如果在展开的时候有什么问题的话。
![36.JPG](http://upload-images.jianshu.io/upload_images/46495-ad702e743dbe66b3.JPG)
如果一个宏展开成另一个宏调用，ppmx会展示两者的结果，第一个表达式和最后一个表达式推导的，当所哟的表达式都被展开的时候。例如，宏length-incf会展开成一个setf宏的调用。setf再展开成一个setq的特殊函数。
![37.JPG](http://upload-images.jianshu.io/upload_images/46495-96dc80865f5543a2.JPG)
在一些实现中，dotimes宏展开成一个do宏的调用。在下面的例子中，do依次展开成一个更加复杂的表达式，包括block，let，tagbody和go。在本书中，我们不会讨论tag的函数体和go。
![38.JPG](http://upload-images.jianshu.io/upload_images/46495-4ebf798d803c9689.JPG)
###第十四章进阶话题
###14.12 lambda列表关键字&BODY 
人们写宏的理由之一是他可以给lisp加上新的语法。例如，我们可以写一个while宏来提供和在其他语言中一样的控制结构。
![39.JPG](http://upload-images.jianshu.io/upload_images/46495-a743f3503cc3fe6c.JPG)
while宏接受一个测试表达式作为第一个参数，如果测试为真，他之后的函数体表达式会被求值。函数体表达式会被关键字&rest手机，但是common lisp包含一个特殊的关键字，&body，用在剩下的参数是从一些控制结构中提取的宏。一些lisp编辑器将注意力放在关键字切分调用成宏的阶段。&body关键字的使用也是意味着宏定义的读者可已经剩下的参数作为lisp代码的函数体。
函数NEXT-POWER-OF-TWO使用while循环来反复加倍变量I的值，从1开始，知道第一个两倍数大于输入的值n。
![40.JPG](http://upload-images.jianshu.io/upload_images/46495-03405300fc05d001.JPG)
在最好的风格中，特定的额问题应该使用do而不是while来解决，这是要避免显式的setf调用。
###14.13 破坏lambda列表
宏MIX-AND-MATCH接受连个对偶（pair）作为输入然后返回生成四对的表达式。
![41.JPG](http://upload-images.jianshu.io/upload_images/46495-bec12c775a5d0662.JPG)
在上面的例子中，我们手动输入了(FRED WILMA)和(BARNEY BETTY)。但是既然宏是不对自己的参数气质的，他们就可以将输入的表达式自动作为列表结构的一部分。这就是我们已知的破坏性。你可以定义如何破坏一个表达式，通过使用另一个整个参数列表替换宏参数列表的变量来实现。例如，我们可以用（x1 y1）替换MIX-AND-MATCH中的p ，还有（x2 y2）来替换。之后的版本就是使用破坏性的。
![42.JPG](http://upload-images.jianshu.io/upload_images/46495-e4397e47d579ee33.JPG)
破坏性值在宏之中可以使用，在普通函数中不可以。对于宏来说，定义新的复杂语法的控制结构的视乎，破坏性就和有用了。宏dovector接下来就是模仿dotimes和dlist了。后续的步骤就是步进向量中的元素了。宏使用破滑行的手段来提取向量中的索引变量，向量表达式和结果形式。
![43.JPG](http://upload-images.jianshu.io/upload_images/46495-6a43ae092f076090.JPG)
你可以从dovector的展开中看出，为什么这个宏是一种很有用的缩略法。
![44.JPG](http://upload-images.jianshu.io/upload_images/46495-667c947f3047dd76.JPG)
dovector使用本地变量vec-dov（储存向量）和len-dov（储存长度），还有索引变量i-dov来展开成一个do表达式。选择这些名字是因为他们不会与用户的任何变量产生冲突。如果我们使用了vec，len和i，作为变量名，可以防止用户访问一些他们自己的变量。展开在do*的函数体中也会包含变量x的显式赋值，更深层次的lisp展开是由block，lettagbody和go组成的，这个dovector表达式对于人类阅读来说更加合适一些。
###14.14 宏和常量作用域（lexically scoping）
我们回到对于faulty-incf的观察，一个incf的实现尝试，作为一个函数而不是一个宏。假设我们在传给函数之前就引用变量，(FAULTY-INCF ’A)。faulty-incf需要做两件事，必须找出变量当前的值，还有就是替换原来的值。
对于全局变量也是可以的，回忆一下钻具变量在字符的值单元的命名方式。我们可以使用内建函数symbol-value来访问值单元，我们用setf或者内建的set函数可以存储值。 
![45.JPG](http://upload-images.jianshu.io/upload_images/46495-aac0c178e66d5ade.JPG)
函数看上去工作是很正确的，但是也只是对全局变量有效。如果我们尝试使用在本地变量上，就会失败。simple-incf不论是本地变量或者全局变量都可以使用。
![46.JPG](http://upload-images.jianshu.io/upload_images/46495-662301b4fb07d041.JPG)
在test-simple中宏simple-incf被展开成一个时候求值的表达式，在test-simple的常量内容当中。所以本地变量turnp是一个常量，这是没有问题的。
![47.JPG](http://upload-images.jianshu.io/upload_images/46495-4b72742ad7da7384.JPG)
在faulty-incf求值回溯图中我们可以看到bug。在faulty-incf的函数体重，只有本地变量var是可见的。粗实线包括的内容是显示了上层的turnip的常量内容。没有值被赋予全局变量trunip是不可访问的。所以当symbol-value找到值单元的时候会有个未赋值错误。
![48.JPG](http://upload-images.jianshu.io/upload_images/46495-b3d89bfd02229e80.JPG)
###14.15 宏的历史意义
相比普通函数和特殊函数，宏的特性之一就是语法是可定义的。这回是的程序员可以对自己的语法扩展定义有清晰地认识。使用扩展的人说明他们是定义的而不是内建的使用者。相比之下，像pascal这种语言的话是不可以加一个新的语句类型的。只有新的过程可以加。扩展pascal语法的唯一方式是写一个预处理器或者修改编译器。两个方法都是不可操作的。
common lisp的很多特性在早期的方言中都是有程序员以宏包的方式定义的。例如setf和destruct，还有with-open-file宏。甚至defmarco是原始的扩展。（虽然lisp在一开始已经有宏了，在defmarco出现前，还是使用一个和累赘的方法实现的。）
在许许多多的人的聪明才智和辛勤辅助下，lisp已经持续演化了超过三十年了。这个进化也包括了宏。除了扩展lisp，宏也可以被用在定义一种新的语言上。用于人工智能的很多高级语言都是建立在lisp的基础上。本书中，我们用common lisp宏创造了一个特殊图形语言实现。
###14.16 动态作用域
纵观本书，我们已经在所有变量上使用了语法作用域。语法作用与以为这对于函数foo就可以访问变量x，foo的定义必须出现在x定义的范围内。如果foo定义在顶层的额haunted，只能访问全局变量（加上任何定义的本地变量），但是如果函数定义是一个lambda表达式的话，在另一个函数bar的函数体中出现的话，就可以访问abr的本地变量还有自己的。函数定义在bar外面就不能访问了。
可以选择语法作用域的特定叫做动态作用域，在common lisp之前，动态作用域是lisp的常态配置。语法作用域现在只在两种方言中存在scheme和T。
动态作用域变量也可以诶特殊函数调用，当一个变量名被声明为特殊的时候，变量就不会是任何函数的本地变量，他的值可以再任何地方被访问。相比之下，常量作用域变量是指在定义的函数体内才可以访问的。声明一个变量为特殊的方法是使用defvar宏。
![49.JPG](http://upload-images.jianshu.io/upload_images/46495-6a66b24fe21366c7.JPG)
我们来比较变量的两种作用域的效果。我们生命bird是一个动态作用域。我们会使用fish作为一个常量作用域变量，所以不应该使用defvar来定义。每一个变量都会被赋值初始化，之后再写一个函数来引用每一个变量的值。
![50.JPG](http://upload-images.jianshu.io/upload_images/46495-575bca0cc1853f9a.JPG)
现在我们来看一下另种作用域的规则区别，我们会写一个佳作fish和birds的函数，首先我们看熟悉的，常量作用域的情况fish。
![51.JPG](http://upload-images.jianshu.io/upload_images/46495-e9304c8a0bcf832b.JPG)
在test-lexical中，表达式fish指向本地变量fish。本地变量对ref-fish是不可见的。在ref-fish函数体中的字符fish仍然指向全局变量fish。在求值回溯图中你可以看到ref-fish被实现包裹，显示了上层语法环境是全局环境。既然reffish不会创造一个自己的本地变量fish，任何出现fish的地方都是指向全局变量。值是（salmon tuna）。
![52.JPG](http://upload-images.jianshu.io/upload_images/46495-a1e043ddf4faf473.JPG)
在动态作用域的情况下，使用birds，测试函数讯早前一个的定义，但是行为不同。区别在与defvar的效果是将birds定义为特殊的。
![53.JPG](http://upload-images.jianshu.io/upload_images/46495-3b1672dd3311463e.JPG)
当我们进入test-dynamic的函数体中，一个新的动态变量birds就会被创建。从现在直到我们离开函数体，每一次使用birds就会指向这个变量，甚至是在其他函数的函数体中。全局变量birds在新的动态变量存在的情况下就是不可访问的。当test-dynamic返回的时候，动态变量birds就会消失，还有同名的birds会再次绑定全局变量。
对于动态变量，没有特殊的求值回溯标记来表示。你可以简单记住一些已经被defvar定义的名字，一旦这样这样做，所有的变量birds都会指向动态作用域。值就是（eagle vulture）。
![54.JPG](http://upload-images.jianshu.io/upload_images/46495-87a9c55fd46eecae.JPG)
动态作用域的求值规则是，如果遇到了粗实线不是指向全局变量的作用域，而是直接传递，继续寻找创建的变量名。如果只想爱你个了全局环境，意味着没有函数现在有一个这个名字的额全局变量，我们只用全局的值。
术语动态绑定的意思就是指在ref-birds里的变量birds没有永久绑定在一个变量上，fish在ref-fish中关联在一个全局变量上。也就是说，名字和真是变量的列检是动态的。当ref-birds在test-dynamic内部被调用，字符birds指向由test-dynamic建立的动态变量。当ref-birds在顶层被调用，同样的字符birds是被解释为全局变量的引用。
动态作用域应该谨慎使用，在早期的lisp方言中他是默认的，这引起了很多程序bug的出现，一个程序意外修改了一个另一个程序创建的动态变量。语法作用域保护一个函数的本地变量不会被其他无关函数修改。但是也有一些环境，动态作用是不二之选。
###14.17 DEFVAR, DEFPARAMETER, DEFCONSTANT
DEFVAR, DEFPARAMETER, 和DEFCONSTANT都是声明名字是特殊的函数。defvar被用来声明变量的值会在程序的正常操作中改变，他会接受一个可循啊的初始值，和一个文档字符串。
![55.JPG](http://upload-images.jianshu.io/upload_images/46495-5915bd1d438018ce.JPG)
一个有意思的事实是defvar是不会改变已经由值的变量的。只会赋值那些没有值的变量。
![56.JPG](http://upload-images.jianshu.io/upload_images/46495-ebe8f642f1c2edc5.JPG)
DEFPARAMETER和DEFVAR的语法相同,但是会被用在声明值不会被程序运行时改变的变量上。他们有参数设定的能力，也就是告诉程序如何行为。另一个区别就是。DEFPARAMETER会给已经有值的变量赋值。
![57.JPG](http://upload-images.jianshu.io/upload_images/46495-6a63013f13b41c3a.JPG)
备用咋定义常量，也就是值绝对不会被改变。在Lisp中的惯例是使用星号包裹变量名，但是这个而不是用在常量上的。尝试改变一个常量的值就会报错，或者常在一个相同名字的新的变量作为常量。PI就是一个内建的常量。
![58.jpg](http://upload-images.jianshu.io/upload_images/46495-519b2764b7eeb37c.jpg)
如果是一个变量的话，声明一个变量为常量有时候允许编译器生成更加有效率的机器语言，也会防止意外的改变变量的值。大部分实现仍然允许你故意改变值，虽然是在调试器当中。
###14.18 重绑定特殊变量
很多动态作用域时代的lisp变量术语对今天来说是过时的，由于历史原因，一些作者在谈论创建变量的时候是说绑定一个变量的。也说：“未绑定变量”，在说”未赋值变量“的时候。绑定不是一定指赋值，这是lisp术语系统中的一大迷惑的地方。非全局变量总是有值的，但是全局变量和特殊变量也有可能没有值。在本书中，我们不会谈论很晦涩的细节。
到现在为止我们已经避免了因为使用绑定带来的困扰。在最后一节中我们会介绍术语重绑定来指向使用旧的名字来创造一个全新的变量。新的变量的存在的那个时候，在程序的任何地方这个名字都会指向这个变量。之前的那个变量就会不能使用。严格来说，我们不会重绑定任何变量，我们动态绑定名字，暂时指向不同的变量。
common lisp包含了很多内建的特殊变量，一些事输入输出的时候使用的。例如，变量print-base就是format函数和其他函数决定那个数值来打印的变量。既然已经定义为特殊，我们就不是一定要使用defvar。为了重绑定他，我们仅仅包括在我们函数的参数列表中。
![59.JPG](http://upload-images.jianshu.io/upload_images/46495-2367e11549ede6ab.JPG)
我们也可以使用let来重绑定变量。当一个特殊变量被重绑定，人格赋值，无论在程序的何处，将会影响新的变量而不是旧的那个。在接下来的例子中，当bump-foo在let的函数体中被调用，他的增值的是let中建立的动态变量foo。当他在let外部被调用，他增值的就是全局变量foo。如果foo还没有被声明为特殊，bump-over总是可以访问全局变量foo。
![60.jpg](http://upload-images.jianshu.io/upload_images/46495-52728a26a9f0c02a.jpg)
当大型程序的不同部分需要互相交换信息的时候，特殊变量的重绑定是最有用的，并且想要通过附加参数给喊出传递变量是不可行的。写一个真实的大型程序需要很多技能，比这本书所说的多得多，这是一个进阶lisp课程的很棒的主题。
###进阶话题涉及函数
DEFMACRO: lambda列表关键字&BODY 
声明: DEFVAR, DEFPARAMETER, DEFCONSTANT
