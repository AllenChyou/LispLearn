# 第二章 列表（LISTS）
### 2.1 列表是最丰富多彩的数据类型
Lisp这个名称是list Processor的缩写。虽然Lisp已经发展成熟很多年，但是列表依然是核心数据类型。列表的重要在于，他几乎可以表现为任何形式：集合，表格，图表，甚至英语句子。列表也可以用来表现函数，但是这个话题我们留到后续章节介绍。
### 2.2 列表看上去是什么样子的？
每一个列表都有两个形式： 打印形式和内部的存储形式。打印形式因为简洁易懂的形式更容易被人理解。内部形式则是列表在计算机内存中真实存在的样子。我们将会用一种图形表达方法来表示内部的存储形式。
在打印形式中，一个列表由一连串项目组成，以圆括号包裹形成。这些项目被统称为列表的元素。下面有一些用括号表达式来标示的例子：
>(RED GREEN BLUE)
>(AARDVARK)
>(2 3 5 7 11 13 17)
>(3 FRENCH HENS 2 TURTLE DOVES 1 PARTRIDGE 1 PEAR TREE)

列表的内部形式并不包括圆括号，在内存中，列表被组织称为一连串的内存单位（Cons Cells），下同中的矩形框所表示。而这些内存单位是由指针（内存地址）连接在一起，下图中的箭头表示。每一个内存单位有两个指针，其中一个指向列表的元素，比如下图中的RED，另一个指向里标的下一个内存单位。当我们谈论到“列表可能包括字符串或者数字作为元素。”的时候我们实际上是指内存单位可能包括指向字符串或者数字的指针，当然也包括指向其他单元的指针。列表(RED GREEN BLUE)在计算机内部的表现如下图：
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekwktvn56uj20eq02yt8n.jpg)
看到这张图的最右边，你或许注意到，列表链的最后是由NIL来作为结束，这是Lisp当中的惯例。这也许违背了某些环境的规定，但是在大部分时候列表总是由NIL来结尾。在括号表达式中，在列表最后的NIL往往省略处理，这也是惯例。
在计算机内部，实际上每一个内存单位都是一小段内存，然后人为的分成了两部分，只要足够达到储存数据指针的大小，事实上的的真实数据存储在其他地方，单元中存储的只是数据的指针（内存地址）。一般来说，大部分计算机的指针式4个字节，所以每一个内存单位是8个字节。
######练习题
2.1用盒装图画出列表(TO BE OR NOT TO BE)在计算机内部的存储图示。
### 2.3 单元素列表
一个字符串和一个只有一个元素的列表不是一样的。下图所示的列表(AARDVARK)，使用一个内存单位来体现。一种一个单元的指针指向字符串AARDVARK，另一个指针指向NIL。所以显而易见列表(AARDVARK)和字符串AARDVARK是完全不同的对象。前者的是一个内存单元，他的指针指向后者。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekwlmkodswj205302wweb.jpg)
### 2.4嵌套列表
一个列表可能包含其他列表作为自己的元素。给出下面三个列表：
>(BLUE SKY)
>(GREEN GRASS)
>(BROWN EARTH)

我们可以用另一对圆括号来把这三个列表聚合成一个列表。结果如下：（这里提示一下注意层次的重要，这是单个列表组成的列表，不是六个字符串组成的列表）。
>((BLUE SKY) (GREEN GRASS) (BROWN EARTH))

我们愿意的话可以把上例中的水平表示转换成垂直表示。空格和缩进并不影响列表的意思，只要圆括号和元素没有改变的话。上例中的列表也能写成这样：
>((BLUE SKY)
            (GREEN GRASS)
                    (BROWN EARTH))

第一个元素仍然是(BLUE SKY)。用盒子图形表示的话就会是下图的样子，列表的三个元素就是第一层次的三个内存单元。每一个元素都是一个含有两个字符串的列表，每一个顶层单元指向一个低层次的双单元列表。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekwp6lole2j20m104tdg4.jpg)
任何书写出来的括号表达式在计算机内部都有一个内存单元结构对应（如果括号匹配不错）。加入括号匹配错误。比如这个列表“‘‘(RED (GREEN BLUE”，计算机不能正确生成一个相对应的内存结构，那就会返回一个错误消息。

######练习
2.2下列列表哪些是格式正确的？也就是说，哪一个是括号疲惫正确的？
>(A B (C)
>((A) (B))
>A B )(C D)
>(A (B (C))
>(A (B (C)))
>(((A) (B)) (C))

 2.3 画出列表(PLEASE (BE MY) VALENTINE)的盒装图。
2.4 下列内存结构的括号表达式是什么？
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekwprp1wvtj20gi057mxb.jpg)

### 2.5 列表的长度
列表的长度就是列表中元素的个数，例如，列表(HI MOM)的长度就是2。那有列表组成的列表长度高如何计算？一个括号表达式写就的列表，他的元素就是在第一层次的项目。例如列表 (A (B C) D)中的元素就是A，列表(B C)和D。字符B,C都仅仅是元素列表(B C)的组成部分。
需要注意的是，在计算机内部是不使用括号的。从计算机的角度来看，列表(A (B C) D)包括三个元素，因为在内部的存储的形式就只有三个内存单元，如图所示：
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekwprp1wvtj20gi057mxb.jpg)
由此可见，一个列表的长度和他元素的复杂成都是没有关系的。一下列表都确实只有三个元素，虽然一些列表的元素本身是列表。三个元素已经有下划线表示出来。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekwqlq4ywzj20g9066jrn.jpg)
原始函数LENGTH就是用来计算列表长度的函数。如果给定的输入并不是列表，而是字符串或者数字，那就会报错。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekwqppviftj20eg0bhzkf.jpg)
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekwqqu9587j20bs03wq2s.jpg)
######练习题
2.5下列每一个列表分别含有多少个元素？

长度|列表
----|----
|(OPEN THE POD BAY DOORS HAL)
|((OPEN) (THE POD BAY DOORS) HAL)
|((1 2 3) (4 5 6) (7 8 9) (10 11 12))
|((ONE) FOR ALL (AND (TWO (FOR ME))))
|((Q SPADES) (7 HEARTS) (6 CLUBS) (5 DIAMONDS) (2 DIAMONDS))
|((PENNSYLVANIA (THE KEYSTONE STATE)) (NEW-JERSEY (THE GARDEN STATE)) (MASSACHUSETTS (THE BAY STATE)) (FLORIDA (THE SUNSHINE STATE)) (NEW-YORK (THE EMPIRE STATE)) (INDIANA (THE HOOSIER STATE)))

### 2.6 空列表
没有元素的列表叫做空列表。没有内存单位，写作一对空的圆括号。
（）
在计算机内部，空列表是用NIL来表示。这里是一个难点：字符串NIL就是空列表，那又为什么把NIL作为一个列表链的结尾呢？
既然空列表和NIL是完全相同的，那么可以很自然地把NIL和（）互相置换。比如列表（A NIL B）也可以写成（A （） B）。这两者在使用上没有任何区别因为在计算机内部是完全相同的两个对象。
空列表的长度是0，虽然NIL是一个字符串，但是因为NIL也是一个列表，所以作为LENGTH的输入时合法的。NIL也是唯一一个既是列表又是字符串的对象。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekwreigyphj20ae07u0sp.jpg)
######练习题
2.6既然NIL和（）是可以互相替换的，那么请将左侧一列和右侧一列一一对应起来。（请注意括号的层次）
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekwrjsxbhcj20d606mmx4.jpg)
### 2.7 列表之间的相等
如果相对应的每一个元素都是相等的，那么两个列表就被认为是相等的。看一下下面的两个列表（A （B C） D）和（A B （C D））。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekwrsxqi3pj20fv0ajmxf.jpg)
这两个列表由同样的元素数量（都是三个）。办事他们不是相等的。第一个列表的第二个元素是（B C），但是第二个列表的第二个元素是B。而且这两个列表都不等于（A B C D），因为他是四个元素的。加入两个列表有着不同的元素数量，则是绝不可能相等的。
###2.8 FIRST,SECOND,THIRD和REST
Lisp提供了从列表中提取元素的原始函数。函数FIRST,SECOND,和THIRD分别返回输入的列表的第一第二和第三个元素。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekwsllsxgpj20cf03ma9w.jpg)
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekwsm86n69j20bz07pjrd.jpg)
如果输入不是列表，就会报错。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekwsx8etcfj20cw03ia9x.jpg)
REST函数是FIRST函数的补集，REST函数会返回除了第一个元素的所有元素。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekwt12g4ooj20gn07jaa4.jpg)
使用FIRST函数和重复使用REST函数就可以构造出我们自己的SECOND,THIRD,FOURTH等等。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekwt3y10x0j20e406rdfx.jpg)
假如MY-SECOND的输入是(PENGUINS LOVE ITALIAN ICES)，那REST函数将会输出列表(LOVE ITALIAN ICES),，这个列表的首个首个元素就是LOVE。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekwtb83sraj20ic03h3yg.jpg)
######练习题
2.7当给出输入(HONK IF YOU LIKE GEESE)的时候，MY-SECOND函数的内部是如何运行的？
2.8怎样使用一个FIRST函数和两个REST函数写一个MY-THIRD函数？
2.9怎样使用SECOND函数写一个MY-THIRD函数？
### 2.9函数操作指针
当我们说一个对象（比如列表或者字符串）成为一个函数的输入的时候，其实这是不准确的。在计算机内部，每一个操作都是围绕指针来进行的，所以真实的输入不是对象本身，而是指向对象的指针。与之相同的，函数的返回值实际上也是一个指针。
假设(THE BIG BOPPER)作为REST的输入，REST函数实际上接受的是第一个内存单位的一个指针。这个指针在下图中由波浪线表示，用波浪线来表示是因为这个指针不能明确的表示出来，换言之，这个指针并不存在于内存单元的内部，他存储在计算机的其他地方，计算机科学家们称作在一个寄存器或者在堆栈中。这些细节方面的事情我们现在还不用考虑。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekwtb83sraj20ic03h3yg.jpg)
REST函数返回的结果是第二个内存单元的指针，也就是列表(BIG BOPPER)的第一个元素。那这个指针式从何而来？REST函数所做的事情就是把第一个元素的右半部分的指针提取出来，并作为结果返回。REST函数返回的是同一个内存单元链条的指针，并没有新的列表被REST函数创造出来。所有的操作仅仅是提取并返回一个函数。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekyav81bd4j20fn04zaa2.jpg)
NOTE:上图中战士的指向字符串THE的指针的意思是为了强调REST函数返回的结果是同一个列表的一部分。但是指向字符串THE的内存单元并不是REST 函数返回结果的一部分。
###2.10 CAR和CDR
到现在为止已经明白，每一个内存单元的一半都储存着一个指针，并且指向某个对象。每一半都有一个名字，左半部分的名字是CAR，后半部分叫做CDR。这些名字是早期计算的历史遗留。当Lisp在IBM704上第一次运行的时候，704太原始以至于还没用晶体管还在使用真空管。他的每一个寄存器都被分割成一些独立的组件，每两个组成地址部分和减量部分。那时候，CAR就是寄存器地址部分内容（Contents of Address portion of Register）的缩写，CDR就是寄存器减量部分内容（Contents of Decrement portion of Register）的缩写，虽然这些术语并没有应用在现代计算机的硬件上，但是Common Lisp部分由于历史原因任然使用CAR和CDR术语来指代内存单元。部分也是由于长格式名称的组成，比如CADR和CDDAR。
CAR和CDR除了作为命名之外，也是内建Lisp函数的名字，作用是分别返回存单元的左半部分和右半部分指针。再看一下列表(THE BIG
BOPPER)，当作为函数CAR的输入的时候，函数接受的并不是列表本身，而是第一个内存单元的指针。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekybu200h0j20k704wgln.jpg)
输入CAR函数的内存单元，提取内存单元指针的CAR部分。所以CAR函数返回的是字符串THE的指针。那同样列表作为输入的时候CDR函数会输出什么呢？
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekyc3t6tubj20fw07jgln.jpg)
输入CDR函数的内存单元，提取内存单元指针的CDR部分。所以CAR函数返回的是列表(BIG BOPPER)的指针。从此例中可以看出，CAR和FIRST,CDR和REST的功能是相同的。Lisp程序员经常如此表述：FIRST返回列表的CAR,REST返回列表的CDR。
#### 2.10.1 一个单元素列表的CDR
 之前看到的列表（AARDVARK）和字符串AARDVARK不一样、列表是这样：
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekwlmkodswj205302wweb.jpg)
既然一个长度为1的列表在计算机内部表现为一个内存单元，那么这个长度为1的CDR就是一个长度为0的列表，NIL。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekych506aaj20dm07s0sr.jpg)

列表((PHONE HOME))只有一个元素。请记住一个列表的元素只是最外层括号之内的对象，这些项目是由第一层次的内存单元指针指向决定的。这个列表的图示是这样的：
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekycovbs29j2089056jrb.jpg)
既然CAR和CDR是从列表的内存单元中提取出特定的指针。那么列表((PHONE HOME))的CAR部分就是(PHONE HOME)。CDR部分就是NIL。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekyctszkasj20g207kt8s.jpg)
######练习题
2.10 画出列表(((PHONE HOME)))的内存形式图，他有几层括号，CAR，CDR是什么？
2.11 画出列表(A (TOLL) ((CALL)))的内存形式图。
###2.10.2 CAR和CDR的组合
观察这样一个列表（FEE FIE FOE FUM），第一个元素是FEE。提取第二个元素的操作是将列表输入FIRST函数，之后再输入REST，或者以我们新的术语，先CDR之后CAR。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekz2lvbjlhj20d202twed.jpg)
如果把函数名从左往右读出来，会先读出CDR然后读出CAR。既然CAR函数的输入就是CDR的输出，用英语说就是列表的CDR的CAR。在Lisp中CDR的CAR会被缩写成为CADR。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekz2lvbjlhj20d202twed.jpg)
如果把A和D调换一下位置会发生什么？函数CADR是提取列表的CAR的CDR，列表(FEE FIE
FOE FUM)的CAR就是FEE，假如我们想要获得这个列表的CDR，那就会返回错误。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekz3e8zq1dj20h807vmx9.jpg)
CDDAR函数返回列表的第三个元素。（如果对新的缩略词由困惑的话，请参考下面的发音指导）这些函数的名字体系那了这个函数的工作方式：CADDR就是提取列表的CDR的CDR的CAR。
![](http://ww3.sinaimg.cn/mw690/8d6a2535jw1ekz3qslok9j20di03it8l.jpg)
怎样理解CADDR是如何工作，你可以葱油往左读出A和D的数目。列表(FEE FIE FOE FUM)作为输入，首先首先是CDR,输出就是(FIE FOE FUM)。然后再一次提取CDR，给出结果(FOE FUM)。最后再提取CAR，就得出了FOE。
也有另一个角度来看待CADDR，首先是CDDR也就是提取CDR的CDR，或者说是REST的REST。列表(FEE FIE FOE FUM)的CDDR是(FOE FUM)。然后在提取CAR就是FOE。CADDR其实就是CDDR的CAR。
Common Lisp提供了很多有关CAR，CDR内建函数，包括了四位AD的所有组合情况。CAADDR是内建的，但是CAADDAR就不是内建函数了。Lisp也提供了从FIRST到TENTH的内建函数定义。
######练习题
2.12 什么样的C...R组合会返回列表的第四个元素？怎样读这样一个函数？
###2.10.3 嵌套函数的CAR和CDR
CAR和CDR也可以像使用在普通列表上一样应用在嵌套列表上。让我们看看如何获取列表((BLUE
CUBE) (RED PYRAMID))内部的组件。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekz4hzaartj20fb058dfx.jpg)
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekz4jh3n3oj20iu0glq3u.jpg)
列表的CAR就是(BLUE CUBE)。为了提取BLUE
，我们必须提取CAR的CAR.这个CAAR函数的作用就是这个。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekz4mfnr64j20gc03s3yf.jpg)
也有另一个角度来看待这个过程。嵌套列表的第一个元素是(BLUE CUBE)，所以CUBE就是列表的FIRST的SECOND。也就是CAR的CADR,等同于CADAR。
现在我们来尝试获取字符串RED，RED是列表的SECOND的FIRST,也就是CADR的CAR。把两个名字融合到一起就是CAADR。具体的过程如下：
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekz4xyzswmj20dx05eaa6.jpg)
######练习题
2.13 画出列表(((FUN)) (IN THE) (SUN))的到每一个字符串的具体过程，利用上图表示的倒三角形式。
2.14 使用列表((A B) (C D) (E F))作为输入，填空

Function|Result
---|---
CAR|（A B）
CDDR|
CADR|
CDAR|
|B
CDDAR|
|A
CDADDR|
|F
2.16 CAAR以列表（FRED NIL）作为输入时如何处理的？
###2.10.4 关于NIL的CAR和CDR
关于NIL有一个有趣的现实：NIL的CAR和CDR是被定义为NIL的。关于这一点为什么真么做可能会有一些让人疑惑。在Lisp的一些早期方言版本中，把NIL作为CAR和CDR的输入实际上会返回错误。但是经验显示，把NIL的CAR和CDR定义为NIL有一个很有用的结果，在后续章节中我们会有所介绍。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1ekz7sre0vuj209d07ua9z.jpg)
既然函数FIRST，SECOND，THIRD等等内部都是有CAR和CDR组成所定义的，单一需要解析的一个列表比较短的时候，那么返回的结果就是NIL。比如返回列表(DING ALING)的第三个元素，但是这个列表没有第三个元素，结果就是NIL。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekz7ys9kq7j20bz0433yd.jpg)
######练习题
2.17 完成下列计算过程
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1ekz807ceuoj20cr0ffaab.jpg)
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1ekz810toipj20dq0fg3yp.jpg)








