#第十二章 结构体和类型系统（Structures and The Type System）
###12.1 导语
Common Lisp包含了很多内建的数据类型，他们一起形成了一个类型系统。我们到现在为止学到的类型有数字（包括一些变种），字符，组合（conses），字符串，函数对象还有流对象。这些都是基本数据类型，但是还有更多。
Common Lisp类型系统有两个重要的属性，第一，类型是可见的，他们由lisp数据结构来描述（字符和列表），并且还有内建参数来测试对象的类型，返回对象的类型描述。第二，类型系统是可扩展的，程序员可以再任何时候创造新的类型。
结构体（structures）是一个程序员定义数据类型的模板。本章在介绍数据类型系统的基础之后，会解释新的结构体类型是如何被定义，结构体又是如何被创造和修改的。
Common Lisp对象系统（object system）（CLOS）提供了一个高级的程序员定义的数据类型容器来支持面向对象的编程风格。我们不会在本书中介绍CLOS，以我们的墓地来说，结构体已经足够了。
###12.2 TYPEP和TYPE-OF
断言typep，假如一个对象是特定类型的对象之一，就会返回true。类型定义可能回事一个非常复杂的表述，这里我们只看简单的情况。
![1.JPG](http://upload-images.jianshu.io/upload_images/46495-d37b71973a699921.JPG)
函数type-of返回一个对象的类型定义。因为对象可以属于多个类型，3是一个数字number，又是一个整数integer。nil既是一个字符优势一个列表。所以具体返回的结果是根据不同的lisp实现而决定的。
![2.JPG](http://upload-images.jianshu.io/upload_images/46495-290294d7956c17cf.JPG)
类型定义(SIMPLE-STRING 6)描述的意思是一个固定长度的字符串，有6个元素。一些lisp实现可能返回的仅仅是simple-string或者string或者（vector string-char）之类的信息。字符串（strings）和向量（vector）之间的关系我们会在第十三章解释。
###12.3 定义结构体
结构体是由任意数量的具名组件组成的程序员自定义lisp对象。结构体类型自动成为lisp类型继承体系的一部分。宏defstruct定义结构体和制定名称还有设定组件的默认值。例如，我们可以定义一个叫做starship的结构体：
![3.JPG](http://upload-images.jianshu.io/upload_images/46495-a57f0475400db2be.JPG)
defstruct语句定义了一个新的类型的额对象叫做starship，它的组件名是name，speed，condition，和shields。starship成为类型继承系统的一部分并且可以被函数typep和type-of引用。
宏函数defstruct也做了一些其他的事情，他定义了一个佳作make-starship的后遭函数来创造新的这个类型的结构体。当一个新的starship被创造出来的时候，具名组件将会默认为nil，speed是0，condition是green，shields回事down。
![4.JPG](http://upload-images.jianshu.io/upload_images/46495-a2ea6c0ef36e8027.JPG)
标记#s是在common lisp中表示结构体的标准方式。在#S标记之后的列表包含结构体的类型的可变组件的名字和值。不要误解了#S标记的括号表达式。对普通列表操作的函数读结构体是不管用的。
![5.JPG](http://upload-images.jianshu.io/upload_images/46495-60a64af2f350fe3e.JPG)
虽然一般来说实例是由调用的构造函数创造出来的，但是使用#S标记，直接在顶层循环输入starship对象也是可以的。请注意结构体必须加上引号，以避免被求值。
![6.JPG](http://upload-images.jianshu.io/upload_images/46495-238a30347b77ca7c.JPG)
###12.4 针对结构体的type断言
defstruct的另一个副作用就是为结构体创造了一个类型断言。基于结构体的名字，断言叫做starship-p。
![7.JPG](http://upload-images.jianshu.io/upload_images/46495-fcdd9bf547c3d1eb.JPG)
既然类型名字starship已经完全被整合近了类型系统中，那么他就可以被应用在断言typep中和函数type-of中。
![8.JPG](http://upload-images.jianshu.io/upload_images/46495-bcea2cb810f17d0b.JPG)
###12.5 访问和修改结构体
一个新的结构体被定义的时候，defstruct也为每一个组件创造了访问函数，例如，他创造了一个starship-speed访问函数来取回starship的组件speed。
![9.JPG](http://upload-images.jianshu.io/upload_images/46495-de0a25652d541bbf.JPG)
访问函数也可以用在setf替换相应位置描述上，也可以用在一般的赋值操作中。
![10.jpg](http://upload-images.jianshu.io/upload_images/46495-17d91e1534f656e3.jpg)
使用访问函数，我们就可以方便的定义我们自己的函数来操作结构体了。比如，下面的函数alert可以更改starship的shield和condition。
![11.JPG](http://upload-images.jianshu.io/upload_images/46495-bfcb4ac4df313bef.JPG)
一个有经验的lisp程序员或许会倾向于使用一个比X更具描述性的参数名。既然alert需要一个starship类型的输入，那为什么直接使用这个名字作为参数名呢？
![12.JPG](http://upload-images.jianshu.io/upload_images/46495-d179203e9e7e4225.JPG)
换个角度看，也有一些程序猿觉得这样写会让人很困惑，因为starship及时一个本地变量名，又是一个类型名。如果你也觉得这样和难理解，你也许会喜欢用一个缩写的变量名，比如strship。
###12.6 构造函数的关键字参数
在新的结构体实例被创造的时候，并不是一定要使用组件的默认值。我们可以在构造函数被调用的时候通过关键字函数来定义不同的值。，下面是一个使用make-starship构造函数的例子。
![13.JPG](http://upload-images.jianshu.io/upload_images/46495-dd3fd0414e4ebdfe.JPG)
###12.7 改变结构体定义
如果你想使用defstruct重新定义一个结构体类型，改变组件的名字或者顺序的话，你应该吧就的结构体的类型整个丢掉，访问函数也不会再起作用，也可能会出现其他问题。例如，已经存储在starship类型的s3中的名字Reliant，如果我们重新定义starship，那么s3的值会成为一个古怪的对象，所有的域都会搅在一起。
![14.JPG](http://upload-images.jianshu.io/upload_images/46495-550c359b10c1cd1b.JPG)
为了修正这个问题，我们需要重定义构造函数make-starship，然后重构结构体。
![15.jpg](http://upload-images.jianshu.io/upload_images/46495-91a4853b6284d3f7.jpg)
###小结
Common Lisp包括很多内建数据类型，本书只讨论一些基础的类型。Common Lisp类型系统都是可见的而且可扩展的。用户可以通过自定义新的数据类型来扩展类型系统。
defstruct定义结构体类型，结构体定义包括所有组件的名字，还有可选的默认值的定义。如果没有定义默认值的话，nil回座位默认值，defstruct也会为类型自动定义一个构造函数（makestarship），一集类型断言（starship-p）。
###本章涉及函数
结构体定义宏: DEFSTRUCT.
类型系统函数: TYPEP and TYPE-OF.
###Lisp Toolkit: DESCRIBE and INSPECT
describe是一个接受任何类型lisp对象作为输入的函数，然后打印这个类型的描述性信息。很多lisp系统的在线文档都是以这种方式提供的。describe也是一个很好的方式来看Lisp系统的内部工作方式，因为你可以用来看像cons，nil，defun等的描述来学习一些有趣的东西。
由describe生成的准确输出根据lisp实现的不同而不同，这里是一些典型的例子。作为一个lisp初学者你也许不明白上面的全部信息，但是使用帮助手册（用describe也可以）来排解困惑也是一件很有意思的事情。
![16.JPG](http://upload-images.jianshu.io/upload_images/46495-3b2c9f878f81b72e.JPG)
describe在展现结构体上是特别有用的。在大部分Common Lisp实现中，describe以一种比#S标记更具可读性的方式来展现结构体的域。
![17.JPG](http://upload-images.jianshu.io/upload_images/46495-2e9144f25acbcedd.JPG)
另一个值得一试的工具是inspect，如果你的计算机有一个鼠标和一个窗口系统的话，inspect或许可以使你用鼠标指向来检查对象的组件。尝试定义一个像half那样的简单哈数，然后用表达式（inspect ‘half）来看看函数定义在内部是怎么存储的。
不同的lisp实现提供不同的inspect程序，你会需要看看你使用的特定lisp手册来学习一下使用inspect。
###第十二章进阶话题
###12.8 打印结构体的函数
发明一种专门用来打印结构体的标记是很方便的。例如，在打印结构体的时候，我们也许不想要看所有对象中的域。只是想看名字罢了。在Common lisp中，打印缩略结构描述的惯例是编造一个标记，由“#<”开始，由“>”结束，中间包括了结构体类型和任何需要的定义信息。
![18.JPG](http://upload-images.jianshu.io/upload_images/46495-e85bc3189bcac9c2.JPG)
写我们自己的打印函数，第一步是定制starship对象的打印方式，。他必须接受三个输入：将要被打印的对象，打印的流向，还有一个数字（叫做深度），也就是Common lisp用来限制打印的深度，控制复杂结构的打印粒度。我们会在本书中忽视深度参数，但是我们的函数必须接受三个参数来正常工作。
![19.JPG](http://upload-images.jianshu.io/upload_images/46495-31bfff4f15749f83.JPG)
我们来测试一下，调用这个函数，用starship作为第一个输入，用T作为第二个输入（T指向默认打印输出流，也就是控制台），还有深度，0。
![20.JPG](http://upload-images.jianshu.io/upload_images/46495-333e2fa9d460da5f.JPG)
现在我们来把这个函数包括近defstruct的一个选项里。
![21.jpg](http://upload-images.jianshu.io/upload_images/46495-bae2e13e9c1a5f73.jpg)
当一个结构体包含其他结构体作为组件或者我们想要限制大部分信息的时候，打印函数是特别有用的。当一个结构体重出现循环指针的时候，大隐函数几乎是白哦准配置。举个例子，每一艘船都有一个船长，每一个船长都有一艘船。如果结构体中船长和船的指针互相指向的话，打印其中一个就会产生无限循环，或者被迫使用那个特别没有美感的#1#标记来标识循环指针结构。但是如果打印函数只是打印名字字段的话，那么循环指针的问题也就没有那么突出了，打印的方式也比较优雅了。
###12.9 结构体的相等
函数equal不会认为两个结构体相等，及时他们拥有相同的字段。
![22.JPG](http://upload-images.jianshu.io/upload_images/46495-f04287361cc16048.JPG)
然而，eqaulp函数就会认为他们是相等的，如果他们的组件类型是一样的而且值也是相同的话。
![23.JPG](http://upload-images.jianshu.io/upload_images/46495-9133541287b6afd4.JPG)
equalp也是不同于equal的，在比较两个字符串的时候会忽略大小写。
![24.JPG](http://upload-images.jianshu.io/upload_images/46495-f098d8b5748cc7b5.JPG)
###12.10 从其他结构体继承
结构体类型可以使用defstruct的选项：include来被组织进一个层次体系里面。例如，我们可以定义一个结构体类型ship，它的组件是name，captain还有crew-size。之后我们可以定义starship作为一种类型的ship，加上组件weapons和shields，还有supply-ship作为一种ship的附加类型，加上cargo。
![25.JPG](http://upload-images.jianshu.io/upload_images/46495-b9a0cb3e61a98fb2.JPG)
starship结构体包含了ship结构体的所有组件。因此，当我们创造一个starship，它的首先三个组件就是name，captainhecrew-size。supply-ship类型也是一样。
![26.JPG](http://upload-images.jianshu.io/upload_images/46495-97cac333a882ccdf.JPG)
Enterprise同事是一个ship类型和starship类型，所以对于两个类型的断言，他都会返回true。
![27.JPG](http://upload-images.jianshu.io/upload_images/46495-ec9f04a1cd1f3807.JPG)
最后，请注意，ship的访问函数对于starship和supply-ship这些子类型也是管用的。因此我们可以使用ship-captain函数或者starshipcaptain函数访问Enterprise的captain字段，但是supply-ship-captain不可以使用。
![28.JPG](http://upload-images.jianshu.io/upload_images/46495-e5765dee199ec3d4.JPG)
