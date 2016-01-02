#第十章 赋值（Assignment）
###10.1 导语
我们在第五章学习的宏函数setf改变了变量的值；这个行为被称作赋值。在本书中我们已经尽量避免赋值的使用，仅仅是用来在顶层循环设置全局变量。我们还没有学到如何在函数内部使用setf。
初学编程的时候为什么要避免使用赋值呢？因为赋值是很容易被误用的，从而导致函数很难被理解和调试。如果你一开始学编程的时候用的语言是basic，pascal，modula或者C，他们都十分仰仗赋值，你可能对于lisp不怎么用赋值感到很惊讶。相比其他语言，lisp提供了丰富的控制结构集合（比如let，还有函数式操作）这使得赋值不那么紧要了。
然而在某些场合下，lisp中使用赋值就是很合适的。本章将会介绍一些使用赋值编程的标准技术，还有一些除了setf之外的内建赋值格式。赋值被经常用在迭代控制结构的组合上，我们将在后续章节去讨论。
###10.2 更新一个全局变量
假设我们现在正在经营一家柠檬水小摊，我们想要追踪到现在为止已经卖掉多少瓶。将整个销量存储在一个全局变量中，“total-glasses”，初始化是0.
![1.JPG](http://upload-images.jianshu.io/upload_images/46495-1df5c94498b4260f.JPG)
Common lisp中有一个惯例就是全局变量的开头和结尾都会加上星号。自然在顶层循环进行快速的计算的时候可以忽略这个惯例，就是用全局变量进行计算。但是在你想要写一个程序来处理全局变量，那就要加上星号了。
现在，每一次我们卖出一些柠檬水，就不得不更新这个变量。我们也想知道现在为止有多少被卖出。
![2.JPG](http://upload-images.jianshu.io/upload_images/46495-36d7530634691cf3.JPG)
请注意sell函数包括了两个语句，第一个是用来更新变量*TOTAL-GLASSES*。第二个语句是用来打印现在为止已经卖出多少瓶的信息。因为使用format来打印结果，所以返回值是nil。
###10.3 常规更新函数
setf可以给任何变量赋任何值。很普遍的赋值用法就是去更新一个变量，换言之，变量的旧值被用来计算变量的新值。我们的柠檬水小摊就是地A型的更新变量的例子。很多，或者说大部分赋值的使用就是这种方式。Common Lisp提供的内建宏函数，所表达的大部分更新函数都要比使用setf更加的简洁。我们来考虑这两种情况，通过加数或者减数来更新一个计数器，还有通过在前面增加或者删除元素来更新一个列表。
###10.3.1 宏函数incf和decf
给一个数字变量加上数，可以这么写（setf A （+ A 5）），你也可以这么写（incf A 5），incf和decf都是为了加数或者减数而定义的特殊赋值宏函数。如果加减的数字被省略，那么默认是1.
![3.JPG](http://upload-images.jianshu.io/upload_images/46495-9394a1b91994029b.JPG)
###10.3.2 pushhe和pop宏函数
通过在最前面组合上元素的方式，可以再列表上加上一个元素，比如(SETF X (CONS ’FOO X))，你也可以更加优雅地表现你的意图(PUSH ’FOO X)。push，是源自于经典计算机术语，pushdown stacks（压栈），或者说进栈。栈就像自助餐厅里，放盘子的那个带弹簧的器具，当以放入一个盘子到栈里面，他就会成为最顶上的那个元素，当你把这个元素从站里面拿出来，下面的盘子就会成为最上面的元素，我们来尝试一下使用push来建立一个盘子的栈。
![4.JPG](http://upload-images.jianshu.io/upload_images/46495-c8f167a590fca9ad.JPG)
dish3现在就是栈最顶上的元素，（从左向右阅读一个列表就是从顶部到底部阅读一个栈），每一次调用push都会实行一个赋值，变量mystack总是会被更新加上一个内存单元。当我们把盘子从栈中拿出来的时候，最顶上的盘子就是dish3，lisp提供了一个pop宏函数来更新一个变量，方法是设置指针指向这个原始列表的rest。
![5.JPG](http://upload-images.jianshu.io/upload_images/46495-665ece30cef6a522.JPG)
请注意pop返回的结果是之前栈中最上层的元素，这个元素被弹出来其实是一个副作用，下面两个语句是相等的。
![6.JPG](http://upload-images.jianshu.io/upload_images/46495-db0b4dde7418b218.JPG)
let表达式首先记住的栈顶的元素，本地变量top-element。之后再函数体内通过设置mystack成为（rest mystack）而实现弹栈。最后返回值topelement。
为了和其他的赋值语句一致，push和pop实际上应该被称作pushf和popf。他们的名字不是以f结尾时因为历史原因。他们在setf出现之前就被使用了，也就是在这个f惯例出现之前。顺便说一句，setf就是set field的缩写，设置域。
![7.JPG](http://upload-images.jianshu.io/upload_images/46495-554855c7b42e05f9.JPG)
###10.3.3 更新本地变量
赋值不应该被胡乱的使用，例如，改变本地变量一般是被认为不优雅的做法，只是应该使用let绑定本地变量就好了。（当然也有例外）。一个更加不优雅的做法就是改变出现在函数参数列表里的变量的值，这样会使得函数难以理解，看看接下来的写的很烂的代码
![8.JPG](http://upload-images.jianshu.io/upload_images/46495-700f7c5d86eda91c.JPG)
这段代码可以通过引入一些变量和使用let* 函数来改善。 当所有的赋值都被移除，我们可以确保变量的值一旦被创造出来就不会被改变。使用无赋值风格的程序是很容易理解的，也很优雅。
![9.JPG](http://upload-images.jianshu.io/upload_images/46495-774b8b7f816cc6a8.JPG)
有一些时候是使用赋值来代替let绑定是更加方便的做法。接下来就是例子，请注意每一个变量初始值都是nil，然后会一次性赋一个新的值。这种有纪律性的赋值并不是一个坏的风格；与出现在前面例子中的赋值是不一样的。
![10.JPG](http://upload-images.jianshu.io/upload_images/46495-127f60b7ac033128.JPG)
###10.4 when和unless
when和unless都是需要求值超过一个表达式的时候使用的，它的语法是这样的：
![11.JPG](http://upload-images.jianshu.io/upload_images/46495-84f39cdbc18273b5.JPG)
when函数首先对测试部分语句求值，如果返回值是nil，when就仅仅返回nil。如果结果是非nil，when会对他的函数体内的语句求值然后返回最后一个值。unless是相似的，除了对测试部分求值为false的时候才继续计算之外。对于这两个条件式来说，都是先对测试部分求值，然后范湖i最后一个语句的值。最后一个语句之前的语句都只是起到副作用，比如i/o和赋值。
when和unless只有在文体上比cond要好一些，他们的语法更加简单一些，也更加平易近人，因为他们的只是被切分成两个部分。举个例子，假设我们想要写一个函数来接受两个数字作为输入，并使他们相乘。假设这个函数需要第一个输入的数字是奇数，第二个输入的数字是偶数。如果输入除了一点纰漏，那么程序可以通过加1或者减1的方式来修正输入，并且打印出一个合适的警告信息。
![12.JPG](http://upload-images.jianshu.io/upload_images/46495-fe55a7312a9e1a61.JPG)
###10.5 虚拟变量
一个虚拟变量就是指指针可能被存储的任何地方。一个像X或者N的普通变量包含了一个指向它的值的指针。但是指针也可以被存储在其他地方，比如一个内存单元的car和cdr。赋值的意思其实是将一个指针替换成另一个指针，所以当我们说变量N的值是3的时候，说的其实是一个叫做n的变量包含了一个指向数字3的指针。一个表达式（incf n）就是将原来的指针替换为一个指向4的指针。
本章介绍的赋值宏函数可以给虚拟变量赋值，也就是说他们可以在很多不同的地方存储指针。SETF, INCF, DECF, PUSH, 或者POP的第一个参数就是一个位置描述，请看例子；
![13.JPG](http://upload-images.jianshu.io/upload_images/46495-7a4bac7d73b95cd1.JPG)
如你所见，setf和相关的语句可以接受位置描述，如（fourth x），然后在那些地方存储新的指针，举个例子，表达式（fourth x）定义的指针就是列表x的第四的内存单元的car。这个为止也被称为x的cdddr的car，如下所示。
![14.JPG](http://upload-images.jianshu.io/upload_images/46495-8ab74e7225798a6a.JPG)
###10.6 样例学习：井字游戏
在本节我们会写我们的第一个大程序：不止是玩井字游戏，还要解释每一步背后的意思。在面对这样复杂度的程序设计的时候，我们需要首先互点时间想一想整个的设计，特别是将会使用的数据结构设计。我们先在画板上进行设计：
![15.JPG](http://upload-images.jianshu.io/upload_images/46495-c751864eb03fc3bb.JPG)
我们怎么来描述一个井号画板呢？有字符board带头的一个列表，后面是十个数字，每一个数字描述的是每一个位置的内容。如果对应位置的数字是1，那么就表示该位置的内容是O，如果对应位置的数字是10，就表示对应位置的内容是X。函数make-board创造一个新的井字游戏画板。
![16.JPG](http://upload-images.jianshu.io/upload_images/46495-ae38e19ff8a52aa6.JPG)
请注意如果B是一个存储井字游戏画板的变量，滑板的位置1可以写入，（nth 1 B），位置2可以写入（nth 2 B），依次类推。（nth 0 B）就是返回一个字符画板。
现在，让我们来写一个函数打印画板，convert-to-letter函数是将0,1,10等分别转换成空格，O或者X的函数。print-row是打印画板一行的函数，print-row在print-board函数中逐次调用。
![17.jpg](http://upload-images.jianshu.io/upload_images/46495-1d4d9343cf60f4c3.jpg)
我们能通过改变列表里面任意位置的数字来实现玩家走一步的效果，只要把对应位置的数字从0改成1或者10就好。在make-move函数中的变量player不是1就是10，这取决于是谁走出这一步。
![18.JPG](http://upload-images.jianshu.io/upload_images/46495-6c38421efc8708b6.JPG)
在继续之前，我们先做一个模板来测试一下几个函数，我们会定义变量 * computer * 和 * opponent * 来存储10和1的值（分别就是X和O），因为这样子显得很清楚。 
![19.JPG](http://upload-images.jianshu.io/upload_images/46495-59433d667f346e4e.JPG)
为了让程序容易表现，设置画板的表现方式是很重要的，对于井字游戏来说还是比较简单的，因为三个数字成一行的话只有8种配置存在。横的三个，竖的三个，还有对角线两个。我们可以说每一种组合都是一个triplet（三联体）。我们会把所有的三联体存储在一个全局变量中 * triplets * 。
![20.JPG](http://upload-images.jianshu.io/upload_images/46495-881b7ded3d7956d4.JPG)
现在，我们可以写一个函数sum-triplet来计算画板中由三联体定义的位置的值的和。例如，右对角线的三联体是（3 5 7）。三个元素的的位置的值的和是11，表示有一个10和一个1还有一个空格（以某一个顺序排布）在那个对角线上。如果和是21，那么就表示有两个X和一个O存在。如果和是12，就表示有一个X和两个O存在。
![21.JPG](http://upload-images.jianshu.io/upload_images/46495-3c5f51793154825d.JPG)
想要完全分析出一个画板，我们需要浏览所有的和，函数compute-sums的作用就是返回一个所有8个和的列表。
![22.JPG](http://upload-images.jianshu.io/upload_images/46495-037282aac42c383e.JPG)
请注意玩家O如果在一行达到了三个O，其中一个和就会是3，类似的如果玩家X达到三个一线的话，其中一个和就是30，我们可以写一个断言来检查这个条件。
![23.JPG](http://upload-images.jianshu.io/upload_images/46495-c890b23a98c1c234.JPG)
我们等会儿回过头在看画板分析这个问题。现在我们先来看看这个游戏的基本框架。函数play-one-game给用户提供了先出手的机会，传递一个新的，空的画板作为输入。
![24.JPG](http://upload-images.jianshu.io/upload_images/46495-4adbd26cf9d8f4a9.JPG)
函数opponent-move的作用是让对手走一步，并且判断这一步是不是合法。然后更新画板，调用computer-move。首先，如果对手的一步让三个符号连成一线，对手会赢游戏结束。第二，如果在画板上已经没有空白的地方存在了，游戏就会陷入和局而结束。我们假设对手是O，计算机是X。
![25.JPG](http://upload-images.jianshu.io/upload_images/46495-5ac30356eb6305bb.JPG)
合法的一部的意思是输入1到9之间的一个整数，这个证书对应了画板上的空位。函数read-a-legal-mobe读取一个lisp对象并且检查他是不是合法的一步。如果不是，函数就会调用他自身然后读取另一步。请注意，第一个两个cond语句每个都包含测试部分和结果部分，最后一个值（递归调用）会被返回。
![26.JPG](http://upload-images.jianshu.io/upload_images/46495-1ff9ce4ea1ef5738.JPG)
board-full-p断言会被opponent-move调用来判断在画板上还有没有更多的空白位置。
![27.JPG](http://upload-images.jianshu.io/upload_images/46495-e338c08b8608715c.JPG)
函数computer-move类似于oppnent-move，除了晚间是X而不是O之外，而且驶入不是从键盘的来，而是会调用choose-best-move函数。这个函数会返回一个双元素的列表，第一个元素师X摆放的位置，第二个元素是一个字符串来解释每一步之后的策略。
![28.JPG](http://upload-images.jianshu.io/upload_images/46495-9646bdc85e1c7f65.JPG)
现在我们机会已经准备好玩我们的第一个游戏了。我们的第一个版本中，choose-best-move只有一个策略，随机选一个合法的位置。函数random-move-strategy返回一个列表，列表的第一个元素就是移动的位置，第二个元素是一个字符串，来解释走这一步的策略。函数pick-random-empty-position从1到9之间选择一个随机数，如果那个位置为空，那么就是用，否则他就会递归调用自身来尝试另一个随机数。
![29.JPG](http://upload-images.jianshu.io/upload_images/46495-36bb44505111f24e.JPG)
你可以先尝试和电脑玩一下游戏来看看感觉如何，很快你就会感觉到，随机选择策略对于计算机端来说不是一个很好地选择；有些时候会让计算机下出很蠢的棋来：
![30.JPG](http://upload-images.jianshu.io/upload_images/46495-9e31b61bc6b2335d.JPG)
计算机很明显在已经有两个X连成一线的情况下，在本可以赢的时候在位置3下了一步。随机选择一步，在位置4放一个X对于全局没有任何好处，因为在垂直方向上那条路已经被O给封死了。
为了使我们的程序更加聪明，我们可以编程找到两个连成一线的情况，如果有两个X连成一线，计算机应该填上第三个来赢得游戏，与之相反，如果有两个O连成一线，就应该在第三个位置放一个X来阻止对手胜利。
![31.JPG](http://upload-images.jianshu.io/upload_images/46495-66973385b622643c.JPG)
如果不能满足他们各自的的策略，make-three-in-a-row和block-opponent都会返回nil。现在我们需要去修改choose-best-move函数来使用更加好的策略。我们引入一个or到函数体当中这样就可以一个个评判具体策略，直到有一个不是nil。
![32.JPG](http://upload-images.jianshu.io/upload_images/46495-8c517b62108d92fa.JPG)
新的策略使得游戏更加有趣了，计算机会在对手明显要赢得时候进行防守，也会在合适的时候利用机会取得胜利、
![33.jpg](http://upload-images.jianshu.io/upload_images/46495-4e86bd9d32058cc6.jpg)
###小结
setf宏可以给变量赋任何值。更新一个变量意味着基于它的旧值来计算一个新的值。两个常规格式的更新语句是给一个数字变量加上或者减去（如incf和decf的操作），或者是给一个列表前面加上或者减去一个元素（如push和pop的操作）。大部分更新操作是用在全局变量上。改变本地变量的值一般被认为是不好的编程风格，相比之下，使用let函数来绑定新变量会更好些。
一个虚拟变量就是一个指针会被存储的任何地方。本章讨论的所有肤质宏函数都可以操作虚拟变量，不仅仅是针对普通变量。
赋值操作的使用在lisp编程里是很保守的。let，函数式操作，还有尾递归函数，这些其他语言所欠缺的，是的赋值在很对哦情况下变得不是很紧要了。没有赋值的程序一般被认为是很优雅的。
######本章涉及函数
赋值宏函数:  SETF, INCF, DECF, PUSH, POP.
条件式: WHEN, UNLESS.
###Lisp Toolkit: BREAK and ERROR
break和error函数对于调试是很有用的，也会使得函数对于bug更有抗性。break在第八章的工具小结被介绍，但是没有展开他的全貌，break和error都接受一个格式控制字符串作为一个参数，附加的参数，格式控制指令也会出现在控制字符串里。
break打印的是由格式控制字符串生成的信息，并且会调用lisp进入调试器。在调试器使用结束之后，通过使用一些调试器命令，比如go，proceed和restart，你可以从断点开始继续执行你的程序。（调试器的实现是独立的，所以你的调试器的命令形式取决于你的lisp实现）。
下面的例子是使用break来调试一个函数，这个函数假设接受一个售价和一个佣金率作为输入，计算出佣金，打印信息，然后根据佣金是不是大于100美元，返回rich或者poor。有些时候，他会返回nil，这就是一个bug。
![34.JPG](http://upload-images.jianshu.io/upload_images/46495-a33f2688b5638bb2.JPG)
为了调试这个程序，我们开始在函数体中插入break调用，然后我们可以使用调试器来检查控制栈和本地变量的值。
![35.JPG](http://upload-images.jianshu.io/upload_images/46495-3133092e639d6b1b.JPG)
现在错误的原因就非常明显了，当佣金刚好等于100美元的时候，cond语句都不是一个真值，所以cond会fanhuinil。解决方法就是将第二个测试表达式替换为T。
error函数接受的参数和break相同，第一个参数是格式化字符串，之后是一些附件参数。error和break之间的一个区别是error从不返回。你不能从error中继续。第二，error仅仅是报告错误然后终止程序，没有进入调试器的打算，虽然打不粉实现是会进的。
通过插入状态检查（sanity checks），程序会变得更加健壮。状态检查就是一些确保都是正常，有错误就报错的表达式。例如，这个版本的average函数就会检查他的输入是不是都是数字。
![36.JPG](http://upload-images.jianshu.io/upload_images/46495-5917c616338bff00.JPG)
Common Lisp还提供了一些其他的函数来报告错误。warn函数打印一个警告信息但是不会终止运行中的程序。cerror表示“continual error”，用户会被告知出错然后会有继续执行的选项。这些函数，还有新的Common Lisp条件系统都允许你标记和设置任意的错误条件，这个不会在本书介绍。请看你的用户参考手册来获取细节。
###第十章进阶话题
###10.7 Do-It-Yoursef List Surgery
你可以通过使用虚拟变量调用setf来直接操作指针。例如，假设我们想要把一个三个内存单元的列表转换成一个两个内存单元的列表，把中间的那个内存单元拿掉。换句话说。我们想要把第一个内存单元的cdr直接指向第三个内存单元。
![37.JPG](http://upload-images.jianshu.io/upload_images/46495-f4890550abcb3a80.JPG)
请注意B的值是不会被snip给改变的。只有第一个单元的cdr被改变了。
![38.jpg](http://upload-images.jianshu.io/upload_images/46495-3cc07851a4e11aa7.jpg)
我们可以使用setf来创造下面的循环结构。
![39.JPG](http://upload-images.jianshu.io/upload_images/46495-308439002be3b4bc.JPG)
循环列表circ看起来就像这样：
![40.JPG](http://upload-images.jianshu.io/upload_images/46495-feba8e1bdb699dbf.JPG)
直接更改内存单元的指针来修改列表的方法被称作list surgery（是在不知道怎么翻译，再深入学习一下之后回来补上，暂且可以理解为列表操作，有朋友知道的话请告知）。列表操作在面对大型的复杂的列表的时候是非常有用的，因为改变一些指针要比建立全新的列表快的多。这也会减少程序的内存要求（或者说更少的使用垃圾回收机制）。进阶的common lisp编程包括了很多列表操作（list surgery），但是对于初学者就不是很必要了。最常用的列表操作已经内建在common lisp中，我们会在下一节见到。
###10.8 破坏性操作列表
破坏性列表操作是指那些改变了内存单元内容的操作。这些操作是很危险的，因为他们能够创造循环结构，变得很难打印出来，而且还有共享结构可能会很淡判断。但是破坏性函数也是很强大而且有效地工具。根据惯例，大部分破坏性函数的名字都有一个前导N（基本上就是意外的历史遗留吧因为）。
###10.8.1 NCONC
nconc（由concatenate而来）是一个破坏性版本的append。append函数创造一个新的列表作为结果，nconc是物理上的改变第一个输入的最后一个内存单元指向第二个输入。
![41.JPG](http://upload-images.jianshu.io/upload_images/46495-13346ddbf53e14bb.JPG)
如果第一个输入是nil，就会值返回第二个输入，因此，也不应该事先就假设(NCONC X Y)就爱一定会改变x的值。如果x是nil，它的值不会被改变。所以要在setf的函数体内，使用nconc来给x赋值才会可以。
![42.jpg](http://upload-images.jianshu.io/upload_images/46495-c6d2f12d055805e0.jpg)
nconc函数实际上接受任意数量的输入，并且暴力连接所有的输入成为一个内存单元链条。我们也可以写一个自己版本的nconc来接受两个列表。技能点：如果第一个输入是nil，那么就简单append第二个输入然后返回。
![43.JPG](http://upload-images.jianshu.io/upload_images/46495-c7fb119d9dc77658.JPG)
###10.8.2 NSUBST
nsubst是subst的一个破坏性版本。他通过改变一些内存单元的car指针来修改列表。
![44.JPG](http://upload-images.jianshu.io/upload_images/46495-f22e712fc35a6d6d.JPG)
在最后一个例子中，既然我们在列表中搜索（a i），我们告诉nsubst使用equal作为等于断言，原先默认的也不会起作用了。
###10.8.3 其他破坏性函数
很多其他的Common Lisp内建函数也有对应的破坏性版本。例如有nreverse，nunion，nintersection和nset-difference。对于前缀n的命名惯例也只有两个例外。
append确实是第一个拥有破坏性副本的lisp函数，它的破坏性版本叫做nconc，（也有一个函数叫做conc，但是因为使用的含混不清在后续的方言中就消失了）很多年之后nconc才导致了n前缀来表示破坏性函数的惯例，这也是为什么没有nappend的原因。另一个n前缀惯例的例外情况就是remove。它的破坏性副本叫做delete，再一次是优于历史原因，（delete在nconc之后才发明，但是却是在n惯例形成之前，所以没有一个nremove的版本）。ni原版本认为是noncopying或者nonconsing的缩写。
###10.9 使用破坏性操作编程
一个破坏性函数特别有用的地方在于给复杂的列表结构做出细微的改变，比如在井字游戏中的make-move函数。还有另一个例子，假设我们使得接下来的表格存储在全局变量 * things * 中。
![45.JPG](http://upload-images.jianshu.io/upload_images/46495-a3af91008ba5ebbf.JPG)
我们如何将字符object1改成frob呢？表达式(ASSOC ’OBJECT1 *THINGS*)将会返回列表(OBJECT1 LARGE GREEN SHINY CUBE)我们可以使用setf来改变第一个内存单元的的car部分存储的指针，既然这是一个列表的破坏性操作，那么列表的值也将会被改变。我们就来写一个一般的重命名函数：
![46.JPG](http://upload-images.jianshu.io/upload_images/46495-5c42d73e2127ce0f.JPG)
我们可以使用nconc，另一个破坏性操作，来给列表中的对象加上一个新的属性。
![47.JPG](http://upload-images.jianshu.io/upload_images/46495-8d1c86381c98d5f1.JPG)
###10.10 SETQ和SET
在早起的Lisp方言中，setf和虚拟变量是不可获得的，赋值函数叫做setq，setq特殊函数今天仍然存在。它的语法和宏函数setf相同，也可以被用在给一般变量赋值（但虚拟变量不行）。
![48.JPG](http://upload-images.jianshu.io/upload_images/46495-2b35e28b39dbb456.JPG)
如果你阅读比较古老的lisp书籍，你会注意到他们的赋值使用setq而不是setf完成的。现代Common Lisp程序员使用setf作为赋值语句，不论是普通变量还是虚拟变量。setq在今日被认为是陈旧的。在内部，仍然，大部分lisp实现使用setq来完成普通变量的赋值，所以你还可以在调试器输出中看到setq。
set函数，类似于setf，来自于最早的lisp方言，lisp1.5，set会对两个参数都进行求值，第一个参数必须求值为一个字符，因为Common Lisp使用语法作用域，而lisp1.5则不是，set函数的意义也就改变了。在common lisp中，set在字符的值单元里存储一个值，及时是本地变量有同名的变量存在。symbol-value函数返回的是一个字符值单元里的呢荣，这里是一个使用set和symbol-value的例子。
![49.JPG](http://upload-images.jianshu.io/upload_images/46495-14cfa92fda78a578.JPG)
