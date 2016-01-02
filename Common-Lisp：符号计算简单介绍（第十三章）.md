#第十三章 数组，哈希表和属性列表（Arrays, Hash Tables, And Property Lists)
###13.1 导语
本章我们会简单介绍三种数据类型，数组（arrays），哈希表（hash tables），还有属性列表（property lists）。数组在其他编程语言里使用的非常频繁，但是在lisp中并不是很频繁。原因是大部分语言的数据类型集合是如此贫乏，以至于数组被大规模应用在那些本该列表，结构体和哈希表更合适的地方。属性列表是本章介绍的三种数据类型中最古老的一个。他们是原始lisp方言，lisp1.5的一部分。在现在lisp编程中，属性列表已经被哈希表替代了，但是他任然是值得去思考的一个类型。
###13.2 创造一个数组
一个数组是一组比邻的块，存储的是用数字下标命名的元素。在本书中，我们值考虑一维数组，也被称作向量。长度为n的向量的组件就是被标记为0到n-1的。我们来创造第一个向量，把他存储在变量my-vec中：
![1.JPG](http://upload-images.jianshu.io/upload_images/46495-08223466b69f2720.JPG)
不要让这个标记#（）迷惑了你，然你以为数组是列表。一个列表是内存单元的链条，数组不是一个链条，他是一个连续的存储块。向量#（tuning violin 440 A）在内存中是这样子的。
![2.JPG](http://upload-images.jianshu.io/upload_images/46495-0f3431c5d7c595b0.JPG)
数组的阴影部分被称作一个数组头。它包含了关于数组的有用信息，比如数组的长度和维度的数量。你可能感兴趣的事，基本的列表操作比如car和cdr在数组上是不管用的。
![3.JPG](http://upload-images.jianshu.io/upload_images/46495-293484454b894acf.JPG)
因为数组的存储是连续的，所以访问每一个元素的效率是一样的。在列表中，我们不得不跟着指针的链条从第一个内存单元到下一个，根据列表长度的不同，访问第一个和最后一个差别巨大。搞笑的访问也是数组胜过列表的主要优点。另一个优点是，在大部分的lisp实现中，同等长度的数组多用的内存是列表的一半。但是列表也有一些优势，任意长度的列表可以很方便的一个个元素建立，不用递归和迭代。但是对于数组，增加一个元素就不是那么容易了。另一个列表的优势可以在结构上共享，这对数组来说是不可能的，但是我们在这个方面不作深入讨论。
###13.3 打印数组
为了能看到数组中的元素，我们必须将全局变量 print-array的值设置成T。这确保了向量会以同样的形式#（）被打印出来。如果print-arrry的值是nil，向量和数组会以一个更加精炼的形式打印，这样他的元素就会被省略。
![4.JPG](http://upload-images.jianshu.io/upload_images/46495-1023c5447e113543.JPG)
###13.4 访问和修改数组
我们在my-vec中存储的向量由四个元素，被标记为，0,1,2,3  。函数aref被用于按照数字下标访问一个数组的元素，就像nth函数访问列表那样。
![5.JPG](http://upload-images.jianshu.io/upload_images/46495-31382dd56473da13.JPG)
aref函数也被认为是setf的一个地址导向，在数组中如何存储一个新的值到特定元素中，我们来制作一个新的数组，并存储一些值进去。
![6.jpg](http://upload-images.jianshu.io/upload_images/46495-b2df6fbce8d32760.jpg)
原先我们学习的用在列表上的很多函数实际上都是用在序列（sequences）上的，序列包括了列表和向量。序列函数的模板比如length，reverse和find-if。
![7.JPG](http://upload-images.jianshu.io/upload_images/46495-ef30ee57ddff1a60.JPG)
另外，有一些函数是专门用来操作列表的。除了car和cdr，还有member函数和其他集合函数，加上subst和sublis，还有破坏性列表函数，nconc，但是想破坏性序列函数nreverse，是可以操作列表或者序列的。
###13.5 使用make-array创造数组
lisp函数make-array创造并返回一个新的数组。数组的长度是由第一个参数确定的。数组的初始化内容不确定。一些common lisp实现将数组元素的内容初始化为0，另一些是初始化为nil。为了安全，你不应该将数组初始化成特别的值，除非你已经显式定义了一个值。
make-array接受一些关键字参数。关键字：initialelement为数组的所有元素定义了一个初始值。
![8.JPG](http://upload-images.jianshu.io/upload_images/46495-93be263fe46938ed.JPG)
关键字：initial-contents定义了一系列值来初始化一个数组的每一个元素。列表的长度必须等于数组的长度。
![9.JPG](http://upload-images.jianshu.io/upload_images/46495-c36c3cc1a47c15e6.JPG)
如果你同时使用了多个关键字，那么初始值会无从判断。
###13.6 作为向量的字符串（STRINGS AS VECTORS）
字符串实际上是向量的一个特殊类型。因此，可以操作向量的函数，length，reverse和aref都可以用来操作字符串。
![10.JPG](http://upload-images.jianshu.io/upload_images/46495-4853c511b5febf5f.JPG)
字符串的元素叫做字符对象（character objects），比如，#\k就是代表一个小写的k的字符对象。字符是另一个数据类型，和符号或者数字都是不同的。字符对象由于他们是求值为自身，所以是不需要加引号的，和数字一样。
![11.JPG](http://upload-images.jianshu.io/upload_images/46495-27e6bd7fab766254.JPG)
既然setf理解了aref是一个位置代称，你就可以破坏性的修改一个字符串了。字符串里仅仅存储了字符对象才能操作，不然会报错。
![12.JPG](http://upload-images.jianshu.io/upload_images/46495-f72399e5ed5d22d5.JPG)
###13.7 哈希表（HASH TABLES）
一张哈希表提供了和联合列表基本一样的功能。你要提供一个键值（key），他可以使任何对象，lisp就会返回给你和那个键相关联的对象。哈希表的好处在于它使用特殊的哈希算法，大大提高了lisp查找的效率。他快的原因是他不是使用内存单元链来实现，而是使用向量来实现。
联合列表相比哈希表还是有一些优势的。由于是普通的列表结构，联合列表更容易创建和操作。哈希表哈希表根据lisp实现不同表现也不同，对于用户来说不是直接可见的。所以如果你想要绝对的简单，使用联合列表。如果你想要来电效率，就用哈希表。
哈希表是不能像向量那样直接从键盘输入的。只能由函数make-hash-table来创建。在哈希表的默认配置中，eql是用来比较键值是否相等的函数。使用eq和equal来创建哈希表也是可以的。哈希表的打印形式有具体的lisp实现决定，一般来说是不会显示元素的。下面是一个典型例子。
![13.JPG](http://upload-images.jianshu.io/upload_images/46495-5021c9d9750f9e4a.JPG)
函数gethash作用是在哈希表中查找一个键值。键值可以使任何类型的对象。setf会将gethash理解成一个位置定义，所以他也会被用在哈希表的存储中。
![15.jpg](http://upload-images.jianshu.io/upload_images/46495-2142d56ccb5fda46.jpg)
函数gethash返回两个值，第一个是和键值关联的那个对象，如果没有找到相应的键值就返回nil，第二个是就是键值在列表中存不存在的表示。第二个返回值这样设置是因为我们需要确定是键值对应的对象不存在还是对象本身的内容就是nil。你可以完全不用理会第二个返回值，在本书中我们不会对多返回值进行讨论。
describe函数会告诉你一些有关哈希表的有用信息，比如他里面哈希桶（bucket）的数量。一个哈希桶有一个子族群的入口（entry）组成。哈希桶越多，被赋值在同一个哈希桶上的入口就越少，检索也就越快，但是速度的代价就是内存占用的增加，inspect函数用来看一个哈希表有多少个入口。
（译者按：这一段有关哈希表和哈希算法的内容还有待学习，过后回来完善，请见谅。）
![16.JPG](http://upload-images.jianshu.io/upload_images/46495-a2bd188c2f277945.JPG)
###13.8 属性列表（PROPERTY LISTS）
在lisp中，每一个字符都是一个属性列表，属性列表基本上提供了联合列表和哈希表相似的功能。你可以在属性列表中使用给定的键值（一般被称作指示器（indicator））存储一个值，之后通过提供指示器的值来检索列表。属性列表是由指示器和值的列表来构成的。
![17.JPG](http://upload-images.jianshu.io/upload_images/46495-d526f3bb86fd79b7.JPG)
属性列表很古老，从lisp1.5就是lisp的一部分。我们为了完整性来讨论一下，在大部分实现中，还是用联合列表或者哈希表来代替他吧。很多lisp实现使用字符的属性列表来达到自己的目的。例如，如果你看看cond或者cons的属性列表，就会看到一些系统定义的信息。你当然可以自由更改属性列表，但是更改系统设置的信息是极端危险的。
get函数通过给出指示器（indocator）来检索一个符号的属性。setf将get理解成一个位置描述；如何将属性存储在属性列表中。我们来给符号fred机上一个属性叫做sex，值设定成male，age属性设置成23，siblings设置成（george wanda）。
![18.JPG](http://upload-images.jianshu.io/upload_images/46495-ed87733bbaef4609.JPG)
检索fred的其中一个属性是很简单的，我们只要使用get来搜索属性列表，请注意，get使用eq函数来检查相等性，所以属性指示器不能使数字。一般来说指示器是符号。
![19.JPG](http://upload-images.jianshu.io/upload_images/46495-874e56ba412e72dd.JPG)
如你所见，当指向一个不存在的属性的时候，get会返回nil。然而，get也接受第三个参数，来指定一个属性没有找到是应该返回的值。这就是一个区别到底这个属性是根本不存在还是这个属性的值是nil的方法。例如，我们可能知道mabel是唯一一个sibling属性是你了的孩子，但是clara的sibling属性就没有定义过。
![20.JPG](http://upload-images.jianshu.io/upload_images/46495-45fd92188325128d.JPG)
属性的值可以在任何时候更改，假设fred迎来了一个生日。
![21.JPG](http://upload-images.jianshu.io/upload_images/46495-8cad3781caa5e233.JPG)
函数symbol-plist返回一个字符的属性列表。我们会在进阶话题13.10中详细讨论这个话题。
![22.JPG](http://upload-images.jianshu.io/upload_images/46495-3f8f057e3f9cb9a0.JPG)
使用函数remprop，可以完全删除一个属性。remprop的返回值是具体实现定义的。如果属性存在，会返回非nil，如果属性不存在，就会返回nil。作为一个副作用，所有的属性名字和相关联的值都会从属性列表中删除。
![23.JPG](http://upload-images.jianshu.io/upload_images/46495-4632f8fe96252fb1.JPG)
###13.9 属性列表编程
假设我们要做一个关于一个故事中字符的数据库，其中一个需要记录的事情就是会面。我们可以将一个姓名的列表存储在每一个个体的has-met属性下。一个名字在集合中只会出现一次。最早的方法是写一个函数来实现，addprop，当需要在集合中增加一个元素的时候就调用他。
![24.JPG](http://upload-images.jianshu.io/upload_images/46495-0fe6da0a651c6658.JPG)
pushnew是一个一般的赋值操作，就像push一样，但是他首先会检查确保元素是不是列表的成员，这对集合操作来说是一个很有用的特性。
Using our ADDPROP function we can easily write a function to record
meetings:
使用我们的addprop函数，我们就可以轻松写一个记录会面的函数。
![25.JPG](http://upload-images.jianshu.io/upload_images/46495-5f6f128f99620c0e.JPG)
函数确保了会面这个对称关系的使用，也就是如果x遇见y，那么y也就遇见x。
![26.JPG](http://upload-images.jianshu.io/upload_images/46495-d3ffc6f738ae4b27.JPG)
###小结
数组是一种序列，也是列表。一位数组被称作向量。字符串就是字符组成的向量。数组可以使用make-array函数创建，他们的元素可以使用aref来访问。很多操作列表的函数也可以操作数组，例如length，reverse和find-if。
哈希表基本上提供了联合列表相同的功能。哈希表提供了非常高效的对象查找，因为比你更不是像assoc那样序列搜索表的。使用哈希算法来计算下标也可以被用在访问向量上。
属性列表附属于符号，并且爱一些lisp实现中被用于存储实现相关信息。在现在lisp编程中，他们经常被使用。哈希表在需要高效率的时候会更加有用一些。
###本章涉及函数
数组函数: MAKE-ARRAY, AREF
打印开关: *PRINT-ARRAY*.
哈希表函数: MAKE-HASH-TABLE, GETHASH.
属性列表函数: GET, SYMBOL-PLIST, REMPROP.
###Lisp Toolkit: ROOM
lisp系统倾向于使用大量的内存，当内存不足的时候，他就会想要获得更多的内存，获得方式有一些。首先，他会回收任何不再被使用的之前分配出去的内存，比如不再指向任何地方的内存单元。这个过程叫做垃圾回收。一些lisp实现的垃圾回收是持续不间断的，但是大部分的实现是打断现有工作，垃圾回收之后在进行继续。这种暂定一般会占用几秒钟，但如果调用垃圾回收比较频繁的话，那暂停时间就会很烦人了。
虽然所有的lisp实现都包含一个垃圾回收器，但他不是common lisp标准的一部分，所以么有一个标准的方法来更改垃圾回收器的参数或者和他交互。在很多实现中，有一个叫做gc的内建函数来手动调用垃圾回收。通常就是打印一些信息消息。
![27.JPG](http://upload-images.jianshu.io/upload_images/46495-45f87ee5bd2a25df.JPG)
另一个lisp获取内存的方式就是在不足的时候找操作系统要。你计算机上的内存越大，垃圾回收调用的频率就低，你的lisp运行也就会快一些。函数room就是打印一些现在lisp内存使用信息，所以你可以知道有多少内存已经被分配了。每一个实现管理内存的方式是不一样的，所有room的打印消息就不同。
如果你是用的虚拟机，当lisp申请内存的时候，会从从磁盘的交换空间开始，但是如果磁盘满了，最好在升级虚拟内存之前先调用一下垃圾回收机制。你可以给lisp可以使用的最大内存设定限制，但是每一个实现处理这个问题的方式都不一样。
![28.JPG](http://upload-images.jianshu.io/upload_images/46495-1aa44a0094f17be3.JPG)
###第十三章进阶话题
###13.10 属性列表单元（PROPERTY LIST CELLS）
我们回忆一下，一个符号是由5个指针组成的。到现在为止我们已经学习了其中三个，符号名，值单元，还有函数单元。属性列表单元是这个组件值得其中一个。每一个符号都有一个属性单元，也有可能值是nil。相比之下，不是每一个符号在函数单元里都有函数定义，或者在值单元里有一个值。
假设我们为符号cat-in-hat建立一个属性列表。函数symbol-plist可以被用来访问我们已经创造的属性列表。
![29.JPG](http://upload-images.jianshu.io/upload_images/46495-f8a00da6dc27070d.JPG)
符号cat-in-hat的内部结构看上去是这样的：
![30.JPG](http://upload-images.jianshu.io/upload_images/46495-17b690a0c5322018.JPG)
setf将symbol-plist理解为一个位置名，所以使用setf给符号设置一个新的属性也是可以的。把符号原有的属性替代掉是很危险的，因为有可能吧符号内置的一些属性给抹掉了。
属性列表被认为过时的原因之一是因为他是全局的数据结构，一个符号只有一个属性列表，在任何地方都可以访问。如果我们使用哈希表还存储信息，我们就可以同时选出一部分，表示成不同的元素集合。每一个哈希表都是独立的，所以改变其中一个就会影响其他的。
###13.11 更多有关序列的信息
函数coerce可以用在将一个序列从一个类型转化成另一个类型。如果我们强制将一个字符串转化成列表，我们就可以看到每一个独立的字符对象。相反的，我们可以使用coerce来讲一个字符的列表转化成一个字符串。
![31.jpg](http://upload-images.jianshu.io/upload_images/46495-5d8f5f77f001519a.jpg)
另一个创建字符串的方法是在用makearray创建向量的时候，使用：element-type关键字将每一个对象定义为string-chars对象的类型。（string-char是一个字符的子类型）。string-shar的向量就是字符串。
![32.JPG](http://upload-images.jianshu.io/upload_images/46495-9edd50d48fe67c2a.JPG)
大部分函数式操作，比如find-if和reduce，都可以操作序列类型，而不仅仅是列表。mapcar是为列表而定义的，但是还有一个更加一般的函数map，可以操作任何类型的序列。map的第一个输入定义为结果的类型，第二个输入是需要操作的函数，剩下的输入是被操作的序列。当输入序列结束的时候，map就会停止。
![33.JPG](http://upload-images.jianshu.io/upload_images/46495-836a881705c30a6a.JPG)
如果map的第一个输入是nil，他会返回nil，而不是构造一个结果的序列。如果你只是想要每一个函数操作的副作用的话，这个特性是很有用的。
![34.JPG](http://upload-images.jianshu.io/upload_images/46495-e4c4b4746faa3b28.JPG)
###进阶话题涉及函数
序列函数: MAP, COERCE.

