#第六章 列表数据结构
###6.1 导语
本章会展示更多列表处理函数和列表如何被应用在其他数据结构中，比如集合，表格和树。Common Lisp相比于其他语言的强处之一就是，提供了很多支持这些数据结构的内建函数。Lisp程序员就哭了一很快的聚焦在他想解决的问题上。Pascal或者C程序员在解决实际问题之前必须先跳出来，先去实现一个类似于Lisp提供的系统，例如连接列表的原语，字符数据结构，存储的分配等等。
在本章我们会比第二章的时候处理列表更加复杂一些。不止会塔伦一些列表处理函数的行为，而且还会看到他们内部的工作。我们先预先准备一下，复习2.17小节学习到的点对表达式。如果你还没有阅读先前的进阶话题单元，现在去读读吧。
###6.2 括号表达式VS内存单元表达式
用括号表达式来表示列表是很方便的，但是也会产生误导。使用括号表达式表示的列表是对称的：由一个左括号开始并结束于一个右括号。因此，cons函数处理参数也是对称的，cons函数在一个列表前加上一个元素，如下：
>######>(cons ’w ’(x y z)) → (w x y z)

为什么我们不能在一个列表的尾部加上一个元素呢？初学者这样尝试之后会被结果惊呆了：
>######>(cons ’(a b c) ’d) → ((a b c) . d)

如果我们在括号表达式中，访问一个列表的左端和右端的区别没有理由这么巨大。但是切换到内存单元表达式来看，区别就非常巨大了。列表是由指针构成的单向链条。对于列表来说我们很容易在列表前面添加一个项目是因为，我们实际上做的是创造一个新的单元并且白cdr指向现有的列表，只是这样。如果将W和（X Y Z）输入cons函数，结果就是一个新的内存单元，它的car指向w，cdr指向原有的列表，如下所示。虽然我们把结果写成这样（W X Y Z），但是实际上也可以用点对表达式来表示（W . （X Y Z））。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1eleiazvk33j21350bh74v.jpg)
列表（A B C）和D输入cons函数的时候，新内存单元的car是指向原有列表（A B C），cdr是指向符号D。结果就是（（A B C） . D），用括号表达式看上去是很奇怪。这个点的存在是绝对必须的，因为这个列表是由其他元符号而不是nil来结尾。用内存单元表达式看起来是这样：
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1eleic1ab4vj20xi0bd74s.jpg)
由于原先的列表cdr已经指向了nil，所以通过新建内存单元的的方法来给列表尾部加上一个元素的方法是不可行的。更加复杂的技术将会被引用，其中一个方法在下一个小节会被介绍。
###6.3 append函数
append函数接受两个列表作为输入，返回一个包括所有元素的列表，第一个列表的元素之后是第二个列表的元素。
>######> (append ’(friends romans) ’(and countrymen))
>######(FRIENDS ROMANS AND COUNTRYMEN)
>######> (append ’(l m n o) ’(p q r))
>######(L M N O P Q R)

如果append函数的输入列表是空列表，那么结果就等于其他输入，给一个列表追加空列表就相当于在一个数字上加上0。
>######> (append ’(april showers) nil)
>######(APRIL SHOWERS)
>######> (append nil ’(bring may flowers))
>######(BRING MAY FLOWERS)
>######> (append nil nil)
>######NIL

append函数对于嵌套列表也是起作用的，他只着眼于顶层括号，所以不必在意输入是不是嵌套列表。
>######> (append ’((a 1) (b 2)) ’((c 3) (d 4)))
>######((A 1) (B 2) (C 3) (D 4))

append函数不会改变任何变量或者现有内存单元的值，所以也被称为非破坏性函数。
>######> (setf who ’(only the good))
>######(ONLY THE GOOD)
>######> (append who ’(die young))
>######(ONLY THE GOOD DIE YOUNG)
>######> who
>######(ONLY THE GOOD) The value of WHO is unchanged.
>######（吐槽：作者这是满满的恶意么？好人死得早？搜索一下才发现是一首77年老歌的名字）

append函数的两个输入看上去好像是对称的，但这只是括号表达式带来的一个假象。append函数对待两个输入是完全不同的。当往列表（D E）后面追加列表（A B C）的时候，append赋值第一个输入但是不复制第二个。第一个输入的最后一个内存单元的cdr指向了第二个输入，返回一个复制的列表的指针。如图6-1所示。
对append函数的工作原理描述过后，就解释了为什么在第一个输入不是列表的时候会报错，但是第二个输入不是列表的时候就ok。
>######>(append ’a ’(b c d)) → Error! A is not a list.
>######>(append ’(w x y) ’z) → (W X Y . Z)

append想要复制第一个输入的内存单元，但由于第一个输入A并不是一个列表，所以报错了。但是当往列表（W X Y）上追加Z的时候，append可以复制他的第一个输入然后最后一个内存单元的cdr会指向第二个输入，所以不会报错。因为第二个输入不是一个列表，所以结果看上去还是有点奇怪。
现在我们需要解决的问题就是，把一个元素加载一个列表的后面，如果我们先把元素做成一个列表，那么就可以使用append来解决这个问题了。
>######>(append ’(a b c) ’(d)) → (A B C D)

![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1eleiekkqm3j211f0pot9y.jpg)
>######(defun add-to-end (x e)
>######"Adds element E to the end of list X."
>######(append x (list e)))
>######(add-to-end ’(a b c) ’d) → (A B C D)

###比较cons，list和append
一开始Lisp程序员会在cons，list和append函数之间的区分上有所困惑，因为这三个函数都有构造列表数据结构的功能。这里是一个对三个函数的简单回顾：
- cons函数创造一个新的内存单元，经常被使用在列表前面需要增加一个元素的时候。
- list函数通过接受任意数量的输入来创造一个新的列表，而且在最后一个元素的内存单元以nil结束。每一个元素的car指向对应的输入。
- append函数将列表合成在一起。通过复制第一个输入，然后使第一个输入的最后一个单元的cdr指向第二个输入。如果第一个输入不是列表的话将会出现错误。

现在我们拉进一步比较他们。首先考虑一种，第一个输入是符号，第二个输入是列表的情况：
>######> (cons ’rice ’(and beans))
>######(RICE AND BEANS)
>######> (list ’rice ’(and beans))
>######(RICE (AND BEANS))
>######> (append ’rice ’(and beans))
>######Error: RICE is not a list.

然后我们看看两个输入都是列表的情况：
>######> (cons ’(here today) ’(gone tomorrow))
>######((HERE TODAY) GONE TOMORROW)
>######> (list ’(here today) ’(gone tomorrow))
>######((HERE TODAY) (GONE TOMORROW))
>######> (append ’(here today) ’(gone tomorrow))
>######(HERE TODAY GONE TOMORROW)

最后我们来看看第一个输入是列表，第二个输入是符号的情况。这是最让人困惑的情况，你必须用内存单元表达式来再想想。
>######> (cons ’(eat at) ’joes)
>######((EAT AT) . JOES)
>######> (list ’(eat at) ’joes)
>######((EAT AT) JOES)
>######> (append ’(eat at) ’joes)
>######(EAT AT . JOES)

为了更多的而开发你对这三个函数的直觉，尝试一下使用sdraw工具来验证上面的例子，lisp toolkit中的sdraw工具是用来画内存单元表达式图的。
###6.5 更多关于列表的函数
lisp提供了很多用来操作列表的简单函数，我们已经讨论过的有cons，list，append和length。他们之中有些必须复制它的输入，有些则不需要。接下来请看这样设置的理由。
###6.5.1 reverse(反转)
reverse函数提供一个列表的反转。
>######> (reverse ’(one two three four five))
>######(FIVE FOUR THREE TWO ONE)
>######> (reverse ’(l i v e))
>######(E V I L)
>######> (reverse ’live)
>######Error: Wrong type input.
>######> (reverse ’((my oversight)
>######(your blunder)
>######(his negligence)))
>######((HIS NEGLIGENCE) (YOUR BLUNDER) (MY OVERSIGHT))

请注意reverse函数只是反转顶层括号的元素。嵌套列表里的作为元素的列表不会被操作。关于reverse函数的另一点就是他不支持符号的操作。列表（L I V E）的反转是列表（E V I L），但是符号live的反转会返回一个类型输入错误。
就像append一样，reverse函数也是非破坏性的。他会复制输入而不是改变输入。
>######> (setf vow ’(to have and to hold))
>######(TO HAVE AND TO HOLD)
>###### (reverse vow)
>######(HOLD TO AND HAVE TO)
>###### vow
>######(TO HAVE AND TO HOLD)

我们可以像下面一样使用reverse函数来给一个列表的最后加上一个元素。假设，我们想要给列表（A B C）加上元素D。列表（A B C）的反转就是（C B A）。把符号D加到这个反转列表的前面，再一次反转，就得到了列表（A B C D）。
>######(defun add-to-end (x y)
>######(reverse (cons y (reverse x))))
>######(add-to-end ’(a b c) ’d) → (a b c d)

现在你知道了两种在列表后面加上元素的方案。append方案和双reverse方案，相比之下，append方案会更好一些，因为双reverse方案会进行两次复制，append方案会更有效率一些。在本章的结尾，我们会在进阶话题中进一步讨论效率问题。
###6.5.2 nth和nthcdr
nthcdr函数返回的是列表的连续第n个的cdr。当然了我们输入0个的话，就会返回原来的列表，如果要的个数多过列表的长度，就会返回NIL。
>######(nthcdr 0 ’(a b c)) → (a b c)
>######(nthcdr 1 ’(a b c)) → (b c)
>######(nthcdr 2 ’(a b c)) → (c)
>######(nthcdr 3 ’(a b c)) → nil

使用大于3的数字输入并不会引发错误；我们会得到和输入3相同的结果。的脚的结论之一就是nil的cdr就是nil。
>######(nthcdr 4 ’(a b c)) → nil
>######(nthcdr 5 ’(a b c)) → nil

但如果列表的结尾不是nil而是一个元字符的话，输入的数字超过长度就会引发错误。
>######(nthcdr 2 ’(a b c . d)) → (c . d)
>######(nthcdr 3 ’(a b c . d)) → d
>######(nthcdr 4 ’(a b c . d)) → Error! D is not a list

函数nth是返回一个列表的第n个元素。
>######(defun nth (n x)
>######"Returns the Nth element of the list X,
>######counting from 0."
>######(car (nthcdr n x)))

既然(NTHCDR 0 x)得到的是列表，(NTH 0 x)得到的是第一个元素，以此类推，(NTH 1 x)就是得到第二个元素。
>######(nth 0 ’(a b c)) → a
>######(nth 1 ’(a b c)) → b
>######(nth 2 ’(a b c)) → c
>######(nth 3 ’(a b c)) → nil

数数字的时候是从0开始而不是从1开始是Common Lisp一直遵循的惯例。在我们讨论数组的时候会在此接触到这一点。
###6.5.3 last函数
last函数返回的是一个列表的最后一个内存单元，这个内存单元的car是列表的最后一个元素。根据定义，这个单元的cdr是一个元字符。否则就不是列表的最后一个单元了。如果列表为空，last会返回nil。
>######(last ’(all is forgiven)) → (forgiven)
>######(last nil) → nil
>######(last ’(a b c . d)) → (c . d)
>######(last ’nevermore) → Error! NEVERMORE is not a list.

###6.5.4 remove函数
remove函数会删除列表中的一个项目。正常情况下回删除所有适合条件的元素，也是有方法删除其中一部分的（在本章进阶话题中）。remove返回的结果是一个删除了相关元素的新的列表。
>######(remove ’a ’(b a n a n a)) → (b n n)
>######(remove 1 ’(3 1 4 1 5 9)) → (3 4 5 9)

remove函数是一个非破坏性函数，当从列表中删除变量的时候，他不会改变任何变量和内存单元。remove得到的结果是一个原来列表的部分的拷贝。
>######> (setf spell ’(a b r a c a d a b r a))
>######(A B R A C A D A B R A)
>######> (remove ’a spell)
>######(B R C D B R)
>######> spell
>######(A B R A C A D A B R A)

下面的表格会帮助你记忆，那些函数拷贝输入进行处理，哪些不拷贝。append，reverse，remove返回一个新的不包括输入的内存单元链，所以他们是要拷贝他们的输入的。像nthcdr，nth和last函数会返回一个指针指向输入的部分组件。他们不需要拷贝任何对象，因为他们所需要的对象都已经存在了。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1eleig4wq1rj20l008xwf6.jpg)
###6.6 列表构成集合
集合是对象的无序集合。每一个对象只在该集合中出现一次。一些典型的集合就是，一个星期的天数，整数的集合（一个无限集合），还有住在王家屯晚上吃了麻辣烫的集合。
集合毫无疑问是有列表组成的一个更加有用的数据结构。一些基本的集合操作比如说测试一个元素是不是在这个集合当中，获取两个集合的并集，交集，差集（也叫做集合减法），还有测试一个集合是不是另一个集合的子集合。这些所有操作的Lisp函数都会在下面介绍。
###6.6.1 成员（member）
member断言的作用是检查一个元素是不是列表的成员。如果是列表的成员，那么有这个元素开始的字列表将会被返回，不是的话，会返回nil。member绝不会返回T，但是传统意义上这是一个断言，因为如果元素术语这个列表就会返回非NIL。
>######> (setf ducks ’(huey dewey louie)) Create a set of ducks.
>######(HUEY DEWEY LOUIE)
>######> (member ’huey ducks)   Is Huey a duck?
>######(HUEY DEWEY LOUIE)       Non-NIL result:  yes.
>######> (member ’dewey ducks)  Is Dewey a duck?
>######(DEWEY LOUIE)            Non-NIL result:  yes.
>######> (member ’louie ducks)  Is Louie a duck?
>######(LOUIE)                  Non-NIL result:  yes.
>######> (member ’mickey ducks) Is Mickey a duck?
>######NIL                      NIL: no

在Lisp的第一个方言中，member函数值返回T或者NIL。但是热么决定使得member函数返回这个元素开始子列表，成为了一个非常有用的功能。这个扩展一如既往的伴随member作为一个断言，因为没有元素的子列表就是false。
这里有一个例子说明为什么member返回子列表是非常有用的。断言beforep，在列表l中，x比y先出现的话，返回真。
>######(defun beforep (x y l)
>######"Returns true if X appears before Y in L"
>######(member y (member x l)))
>######> (beforep ’not ’whom
>######’(ask not for whom the bell tolls))
>######(WHOM THE BELL TOLLS)
>######> (beforep ’thee ’tolls ’(it tolls for thee))
>######NIL

###6.6.2 交集（intersection）
intersection函数是去获取两个列表的交集返回一个两个列表的共有元素的列表。在结果中，元素出现的顺序是未定义的，可能每一个Lisp实现都不相同。另外在集合中顺序是不重要的，只有元素本身是重要的。
>######> (intersection ’(fred john mary)
>######’(sue mary fred))
>######(FRED MARY)
>######> (intersection ’(a s d f g)
>######’(v w s r a))
>######(A S)
>######> (intersection ’(foo bar baz)
>######’(xam gorp bletch))
>######NIL

如果一个列表中出现了多个一样的元素，那就不是一个真的集合。Common Lisp的集合函数，比如intersection和union都可以处理不是集合的列表，但是结果是不是包括重复元素就没有准确定义了，们多事情可能还是要看具体实现。
###6.6.3 并集（union）
union函数返回两个集合的并集，换言之，由两个列表的所有元素组成的不重复列表，一个元素在两个列表中都出现了的话，那么在结果中就只存在一份。对集合来说，结果的元素出现顺序并不重要（也没有定义）。
>######> (union ’(finger hand arm)
>######’(toe finger foot leg))
>######(FINGER HAND ARM TOE FOOT LEG)
>######> (union ’(fred john mary)
>######’(sue mary fred))
>######(FRED JOHN MARY SUE)
>######> (union ’(a s d f g)
>######’(v w s r a))
>######(A S D F G V W R)

###6.6.4 差集（set-difference）
set-difference函数的功能室差集。返回的是第一个集合去除和第二个集合重复的元素的结果。再次说明，元素出现的顺序没有定义。
>######> (set-difference ’(alpha bravo charlie delta)
>######’(bravo charlie))
>######(ALPHA DELTA)
>######> (set-difference ’(alpha bravo charlie delta)
>######’(echo alpha foxtrot))
>######(BRAVO CHARLIE DELTA)
>######> (set-difference ’(alpha bravo) ’(bravo alpha))
>######NIL

不像union和intersection，set-difference函数不是一个对称函数。调换第一个和第二和输入会导致结果的不同。
>######(setf line1 ’(all things in moderation))
>######(setf line2 ’(moderation in the defense of liberty
>######is no virtue))
>######> (set-difference line1 line2)
>######(ALL THINGS)
>######> (set-difference line2 line1)
>######(THE DEFENSE OF LIBERTY IS NO VIRTUE)

###6.6.5 substep断言
如果一个集合包括了另一个，断言substep返回T，换句话说，第一个集合每一个元素都是第二个集合的元素。
>######(subsetp ’(a i) ’(a e i o u)) → t
>######(subsetp ’(a x) ’(a e i o u)) → nil

###6.7 用集合编程
这里的一个例子是说如何使用集合解决一个编程问题。这个问题是给一个名字的前面加上一个称呼，吧john doe变成mr john doe或者吧jane doe变成ms jane doe。如果一个名字已经有称呼了，那么标题应该被保留，但是如果没有的话，我们来尝试根据第一个名字来决定性别然后加上合适的称呼。
为了解决这样的问题，我们必须把他分解成小问题，让我们开始在这个首先名字前面有没有称呼这个小问题上。这里有定义一个函数来解决这个问题。
>######(defun titledp (name)
>######(member (first name) ’(mr ms miss mrs)))
>######> (titledp ’(jane doe)) ‘‘Jane’’ is not a title.
>######NIL
>######> (titledp ’(ms jane doe)) ‘‘Ms.’’ is in the set of titles.
>######(MS MISS MRS)

下一个步骤就是找出这个单词是一个男性名字还是女性名字。我们会使用简化一些的样本来是的我们的例子保持简洁。
>######(setf male-first-names
>######’(john kim richard fred george))
>######(setf female-first-names
>######’(jane mary wanda barbara kim))
>######(defun malep (name)
>######(and (member name male-first-names)
>######(not (member name female-first-names))))
>######(defun femalep (name)
>######(and (member name female-first-names)
>######(not (member name male-first-names))))
>######> (malep ’richard) ‘‘Richard’’ is in the set of males.
>######T
>######> (malep ’barbara) ‘‘Barbara’’ is not a male name.
>######NIL
>######> (femalep ’barbara) ‘‘Barbara’’ is a female name.
>######T
>######> (malep ’kim) ‘‘Kim’’ can be either male or female,
>######NIL so it’s not exclusively male.

现在我们可以写一个give-title函数来给名字加上称呼。当然，我们只给没有称呼的名字加上去。如果名字既不属于男性，也不属于女性那就加上mr or ms。
>######(defun give-title (name)
>######"Returns a name with an appropriate title on
>######the front."
>######	(cond ((titledp name) name)
>######		((malep (first name)) (cons ’mr name))
>######		((femalep (first name)) (cons ’ms name))
>######		(t (append ’(mr or ms) name))))
>######> (give-title ’(miss jane adams)) Already has a title.
>######(MISS JANE ADAMS)
>######> (give-title ’(john q public)) Untitled male name.
>######(MR JOHN Q PUBLIC)
>######> (give-title ’(barbara smith)) Untitled female name.
>######(MS BARBARA SMITH)
>######> (give-title ’(kim johnson)) Untitled, and gender
>######(MR OR MS KIM JOHNSON) is ambiguous

在这个例子中比较重要的特性有，1，将问题分解为小函数。2，逐一测试写的每一个函数，一旦写好了titlep，malep和femaiep等断言，give-title函数就很好写了。
讲一个问题分解为子问题是一个很重要的技能。有经验的程序员京城可以看到正确的方式如何讲一个问题分解成为逻辑上的子问题，但是初学者必须同过联系来建立这种直觉。
Here are a few more things we can do with these lists of names.  The
functions below take no inputs, so their argument list is NIL.
还有一件我们可以做的事情就是关于这些列表的名字。下面的函数没有输入，也就是参数列表是NIL。
>######(defun gender-ambiguous-names ()
>######(intersection male-names female-names))
>######(gender-ambiguous-names) → (kim)
>######(defun uniquely-male-names ()
>######(set-difference male-names female-names))
>######(uniquely-male-names) → (john richard fred george)

到现在为止，我们在本章见过的所有集合都只是由字符或者数字组成。操作列表组成的集合也是很简单的，但是使用像member，union，intersection等等函数的话需要一个小技巧。详情请见进阶话题的讨论。
###6.8 列表组成的表格
表格（tables）是另一个我们可以由列表构建的非常有用的数据结构。一个表格，或者联合列表，是一个列表的列表。每一个列表被称作一个入口（entry），每一个入口的car就是入口的key、下面显示的就是一个五个英文单词和他们的法语翻译组成的表格。这个二表哥包括5个入口，key就是英语单词。
>######(setf words
>######’((one un)
>######(two deux)
>######(three trois)
>######(four quatre)
>######(five cinq)))

###6.8.1 assoc函数
The ASSOC function looks up an entry in a table, given its key.  Here are
some examples.
assoc函数在表格中给出一个key，找到一个入口，下面是例子：
>######(assoc ’three words) → (three trois)
>######(assoc ’four words) → (four quatre)
>######(assoc ’six words) → nil

assoc寻找五个之中匹配的入口，然后返回整个入口。如果assoc没有找到入口，就返回nil。
Notice that when ASSOC does find an entry with the given key, the value
it returns is the entire entry.  If we want only the French word and not the
entire entry, we can take the second element of the result of ASSOC.
请注意当assoc找到对应key的入口的时候，返回的值是整个入口。如果我们只想要法语翻译的话那么就对assoc的结果进行进一步处理，得到入口第二个元素。
>######(defun translate (x)
>######(second (assoc x words)))
>######(translate ’one) → un
>######(translate ’five) → cinq
>######(translate ’six) → nil

###6.8.2 rasscoc
rassoc类似于assoc，但是是着眼于每一个表格的元素的cdr而不是car。（他的名字就是Reverse assoc的缩写）。为了使用符号作为key的rassoc，表格必须使一个点对的列表：
>######(setf sounds
>######’((cow . moo)
>######(pig . oink)
>######(cat . meow)
>######(dog . woof)
>######(bird . tweet)))
>######(rassoc ’woof sounds) ⇒ (dog . woof)
>######(assoc ’dog sounds) ⇒ (dog . woof)

assoc和rassoc都是返回他们找到的第一个符合key的入口，身下的列表就不被搜索了。
###6.9 表格编程
这里有assoc的另一个例子。我们将要创造一个对象以及他们描述的表格，这个表格会存储在全局变量things中：
>######((object1 large green shiny cube)
>######(object2 small red dull metal cube)
>######(object3 red small dull plastic cube)
>######(object4 small dull blue metal cube)
>######(object5 small shiny red four-sided pyramid)
>######(object6 large shiny green sphere))

现在我们来开发函数来告诉我们，在那个对象中有两个对象不同。我们一开始来写一个叫做description的函数来导出一个对象的描述。
>######(defun description (x)
>######(rest (assoc x things)))
>######(description ’object3) → (red small dull plastic cube)

两个对象之间的区别是有什么属性出现在了第一个对象的描述中却没有出现在第二个当中，或者在第二个对象的描述中出现但第一个没有。用技术术语来说就是异或。这是Common Lisp的内建函数。
>######(defun differences (x y)
>######(set-exclusive-or (description x)
>######(description y)))
>######(differences ’object2 ’object3) → (metal plastic)

object2是金属，但是object3是塑料，所以金属和塑料的属性是完全不同的。我们可以根据他们指向的类型的不停来分辨属性。这里是一个点对列表的表格：
>######(setf quality-table
>######’((large . size)
>######(small . size)
>######(red . color)
>######(green . color)
>######(blue . color)
>######(shiny . luster)
>######(dull . luster)
>######(metal . material)
>######(plastic . material)
>######(cube . shape)
>######(sphere . shape)
>######(pyramid . shape)
>######(four-sided . shape)))

我们可以使用这个表格作为函数的一部分来给我们指出给定属性的物质：
>######(defun quality (x)
>######(cdr (assoc x quality-table)))
>######(quality ’red) → color
>######(quality ’large) → size

Using DIFFERENCES and QUALITY, we can write a function to tell us
one quality that is different between a pair of objects.
使用differences和quality，我们可以写一个函数来告诉我们一个物质在两个对象之间有什么不同。
>######(defun quality-difference (x y)
>######(quality (first (differences x y))))
>######(quality-difference ’object2 ’object3) → material
>######(quality-difference ’object1 ’object6) → shape
>######(quality-difference ’object2 ’object4) → color

如果我想要一个所有物质区别的列表而不只是一个怎么办？我们需要一个方法来从区别的列表(RED
BLUE METAL PLASTIC) 到对应物质的列表转换。然后我们也不得不评价多个元素。第一部分可以依靠sublis完成，这个函数我们将在进阶话题中讨论。
>######(differences ’object3 ’object4) → (red blue metal plastic)
>######> (sublis quality-table
>######(differences ’object3 ’object4))
>######(COLOR COLOR MATERIAL MATERIAL)

现在我们不得不做的是在结果中评价多个入口。Common Lisp提供了一个函数佳作remove-duplicates来达到这个目的。
>######(defun contrast (x y)
>######(remove-duplicates
>######(sublis quality-table (differences x y))))
>######(contrast ’object3 ’object4) → (color material)

###小结
列表在自己应用范围是一种重要的数据结构。但是在lisp中他更重要的地方在于他能够用来构成其他数据结构，集合和表格。
如所见，解决任何不简单的问题的方法及时将问题分解成小问题，一个个可控的片段。一些简单的函数可以一个个测试并组合成主要问题的解决方案。

###本章涉及函数
列表函数: APPEND, REVERSE, NTH, NTHCDR, LAST, REMOVE.
集合函数: UNION, INTERSECTION, SET-DIFFERENCE, SETEXCLUSIVE-OR, MEMBER, SUBSETP, REMOVE-DUPLICATES.
表格函数: ASSOC, RASSOC

###Lisp Toolkit:sdraw
sdarw是一个用来画列表对应的内存单元表达式的工具。他并不是Common Lisp标准中的一部分；他被定义在附录A当中。在完整的移植版本中会运行任何Common Lisp实现，使用普通字符来画出内存单元表示：
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1eleiiwvrcrj20lh09t0t3.jpg)
还有一个图形版本，可以从发行商那里的软盘获得，是使用图形来表示内存单元和箭头的函数。那个图形表示看上去更好看一些。一个图形版本是使用CLX，Common Lisp对应X Window System的接口。如果你的电脑不是运行Xwindow的话，会不能使用这个版本。但是要是你的Lisp支持其他图形实例，也会很容易使用sdraw。
另一个有用的工具像函数sdraw-loop，就像一个read-eval-print循环一样工作的画图函数。sdraw-loop的提示符就是字符串S>。
![](http://ww2.sinaimg.cn/mw690/8d6a2535jw1eleijuccawj20k00nogmo.jpg)

###第六章进阶话题
###6.10 树结构（trees）
树是一个嵌套列表结构。到现在为止所有的函数都只是对于列表的顶层进行操作，他们并不进行更深入结构的操作。Lisp也包括一些新的函数来操作整个列表。其中的两个就是subst和sublis。在第八章中我们会写很多这样操作树结构的函数。
###6.10.1 subst
subst函数经列表中的一个项目替换（substitute）为另一个项目，不论这个元素出现在列表的何处。这是一个三输入的函数，输入的顺序是，用x替换z中的y。
>######> (subst ’fred ’bill
>######’(bill jones sent me an itemized
>######bill for the tires))
>######(FRED JONES SENT ME AN ITEMIZED
>######FRED FOR THE TIRES)

如果要被替换的字符没有出现在该列表中，则返回原来的列表。
>######> (subst ’bill ’fred ’(keep off the grass))
>######(KEEP OFF THE GRASS)
>######> (subst ’on ’off ’(keep off the grass))
>######(KEEP ON THE GRASS)

subst着眼于整个列表结构，不仅仅是顶层元素。
>######> (subst ’the ’a
>######’((a hatter) (a hare) and (a dormouse)))
>######((THE HATTER) (THE HARE) AND (THE DORMOUSE))

###6.10.2 sublis
sublis类似于subst，除了他可以同时替换多个元素。第一个输入是一个点对作为入口形成的表格。第二个输入是将要做出替换的列表。
>######> (sublis ’((roses . violets)  (red . blue))
>######’(roses are red))
>######(VIOLETS ARE BLUE)
>######(setf dotted-words
>######’((one . un)
>######(two . deux)
>######(three . trois)
>######(four . quatre)
>######(five . cinq)))
>######> (sublis dotted-words ’(three one four one five))
>######(TROIS UN QUATRE UN CINQ)

###6.11 列表操作的效率
在本章的一开始我们讨论了在括号表达式中，列表如何表现出对称性，但是实际上不是这样的。另一个方法就是在相对速度或者制定操作的效率上表现出的非对称性。例如，提取一个列表的首个元素很容易，但是提取最后一个元素却要很花力气。当要提取起一个元素的时候，我们从指向第一个内存单元的指针开始。函数first仅仅是提取了那个单元的car然后返回他。想要找到列表的最后一个元素要做的工作就要多得多。因为唯一的方法就是一直找啊找知道遇到一个元素的cdr是一个元字符的时候，我们才可以看到那个单元的car。如果原来的列表很长，那么可能要好一会才能找到最后一个单元。
计算机可以循序成百上千的单元来找到想要的单元，时间还非常短，所以一般不需要注意到first和last在速度上的差别。但是如果软件项目的规模变得很大，那么这个细微差别就会变得引人注意。
另一个会影响函数速度的事实就是有多少切实的单元存在。创造一个新的单元是要耗费时间的，最后也会占满计算机的内存。在事实上有一些会被弃用，但是所占的内存却还在。在一些Lisp实现中，内存会被没有用的单元给占满，机器必须暂时停下来然后运行垃圾回收机制。一个函数要处理的单元越多，垃圾回收机制运行的就越频繁。我们接下来来比较一下这两个版本add-to-and函数的效率：
>######(defun add-to-end-1 (x y)
>######(append x (list y)))
>######(defun add-to-end-2 (x y)
>######(reverse (cons y (reverse x))))

假设这些函数的输入是一个n元素的列表。add-to-end1使用append来拷贝第一个输入，然后将第二个输入定位到最后。因此他会创造一个完整的n+1列表。add-to-end2开始是将列表反转，这将会创造一个新的列表，然后将第二个输入加载这个列表的前面，最后反转结果，再次创造一个新的列表。所以add-toend2创造的是n+1+n+1个单元。其中n+1是结果，其他的n+1将会在创造后被抛弃，成为垃圾，很铭心啊add-to-end1是一个更加有效率的函数，因为创造了更少的单元。
###6.12 共享结构
当两个列表共享内存单元的时候，就被称为共享结构。在显示器上输出的列表是不可能体现出共享结构的，因为每一个列表看上去都是一个新列表。通过使用car，cdr和cons可以构造全新的共享列表。例如我们使得两个列表共享一些元素。
>######> (setf x ’(a b c))
>######(A B C)
>######> (setf y (cons ’d (cdr x)))
>######(D B C)

列表X的值是（A B C），Y的值是（D B C）。两个列表共享了一部分内存单元（B C）。共享结构的建立是因为Y是由（CDR X）而来的。如果我们直接简单定义Y，就不会有共享结构。
![](http://ww4.sinaimg.cn/mw690/8d6a2535jw1eleim2lpovj20mw0b6wen.jpg)
###6.13 对象的相等
在Lisp中，符号是唯一的，意味着他们在计算机内存中只能是一个具名符号。每一个内存中的对象命名位置，我们叫他们地址（address）。所以在列表(TIME AFTER TIME)中。是没有两个字符叫做time的。
![](http://ww1.sinaimg.cn/mw690/8d6a2535jw1eleiny6mq5j20ia0eewep.jpg)
列表，从另一个角度说也不是唯一的。我们可以很简单的用独立的内存单元链条来构造不同的列表（A B C），两个列表中的字符是唯一的，但是列表本身不是唯一的。这意味着equal函数是不可以通过比较两者的地址来知道他们是不是相等，因为两个（A B C）是相等的但是他们是不同的内存单元。equal函数因此事一个一个元素的比较两个列表。如果两个列表的对应元素是相等的，那么列表本身就被认为是相等的。
>######> (setf x1 (list ’a ’b ’c)) Make a fresh list (A B C).
>######(A B C)
>######> (setf x2 (list ’a ’b ’c)) Make another list (A B C).
>######(A B C)
>######> (equal x1 x2) The lists are EQUAL.
>######T

如果我们想知道两个指针是不是指向相同的对象，就必须比较他们的地址。EQ断言就是做这个事情的。列表之间如果有相同的地址，那么他们就是相等的（EQ），不会一个个比较元素。
>######> (eq x1 x2) The two lists are not EQ.
>######NIL
>######> (setf z x1) Now Z points to the same list as X1.
>######(A B C)
>######> (eq z x1) So Z and X1 are EQ.
>######T
>######> (eq z ’(a b c)) These lists have different addresses.
>######NIL
>######> (equal z ’(a b c)) But they have the same elements.
>######T

EQ函数要比eqaul快，因为eq只是比较一个个地址，equal首先要测试输入是不是列表，如果是必须每一个元素相对应的比较。由于更有效率，程序员更喜欢使用eq而不是equal，除非是想要知道内存单元是不是相同。
数字，在不同的Lisp系统中也有不同的展现。在一些实现中，每一个数字都有一个唯一的地址，然而在另一些实现中不是。因此eq不应该用来比较数字。
eql断言的行为大体上跟eq相同。他跟eql一样比较两个对象的地址，除开对于两个相同类型（例如都是整形）数字的情况下。他会比较两个数字的值。不同类型的数字在eql中不相等，即使是值相等。
>######(eql ’foo ’foo) → t
>######(eql 3 3) → t
>######(eql 3 3.0) → nil Different types.

eql是Common Lisp中的“默认”比较断言。例如member函数和assoc函数都是默认使用eql作为相等的比较函数，除非指定使用其他的。
对于比较两个完全不同类型的数字，现在也有另一个比较断言叫做=。这个断言是最常用的比较连个数字的方法。给出数字外的其他输入会报错。
>######(= 3 3.0) → t
>######(= ’foo ’foo) → Error! FOO is not a number

最后，equalp断言是相似于equal的断言，但是在一些情况下更加宽容一些。一个例子就是他会忽视字符串比较的大小写。
>######(equal "foo bar" "Foo BAR") → nil
>######(equalp "foo bar" "Foo BAR") → t

初学者经常会对Common Lisp中的大量相等比较感到困惑，我推荐的是忘记这些所有特殊的函数。只是记住两条建议。第一，使用equal，他就是你想要的。第二内建函数memeber和assoc等等在内部出于效率都是使用eql作为默认的。这意味着他们颈部会正确比较列表，除非你告诉他们用不同的相等断言。下一小节我们解释如何做到这一点：
概述一下：
- eq是最快的相等测试，他比较的是地址。专家一般是用这个来快速比较符号，测试他们是不是在内存单元中是同一个物理单元。eq不应该被用来比较数字。
- eql类似于eq，除了他可以安全的比较相同类型的数字，例如两个整形或者两个浮点数。在common Lisp中他是默认的相等测试断言。
- equal是一个初学者使用的断言。他逐个比较列表的元素，除此之外和eql的行为相同。
- equalp是更加宽容的断言，和equal比较的话，他会忽视字符大小写的诸如此类的问题。
- =是一个比较数字最有效的方式，也是比较不同类型数字的唯一方式，比如3和3.0.他只接受数字。

###6.14 关键字参数（keyword argument）
很多Common Lisp函数都支持在列表中附加的，可选的参数，叫做关键字参数。例如，remove函数可以接受一个可选的参数叫做，：count来告诉函数，到底删除多少个对象。
>######(setf text ’(b a n a n a - p a n d a))
>######> (remove ’a text) Remove all As.
>######(B N N - P N D)
>######> (remove ’a text :count 3) Remove 3 As.
>######(B N N - P A N D A)

remove也接受一个：from-end关键字。如果他的值不是nil，那么remove就会从列表的最后开始操作，而不是从开始。所以为了删除列表中的最后两个A，我们可以这样写：
>######> (remove ’a text :count 2 :from-end t)
>######(B A N A N A - P N D)

一个关键字是一个特殊类型的字符，他的名字总是在一个冒号的后面。字符count和：count不是相同的，他们是不同的对象，在eq中也是不同的。关键字总是求值为自身的，所以他们不需要被引用，尝试改变一个关键字的值会引发错误。断言keywordp，如果输入是关键字的话就会返回T。
>######:count → :count
>######(symbolp :count) → t
>######(equal :count ’count) → nil
>######(keywordp ’count) → nil
>######(keywordp :count) → t

另一个可以接受关键字参数的函数是member，正常来说，member使用eql来测试一个项目是不是出现在了一个集合里。eql对符号和数字都是有效的。但是假设我们的集合包括了列表怎么办？在那种情况下我们必须使用equal来做相等判断，否则其他的member将不会找到我们要找的项目。
>######(setf cards
>######’((3 clubs) (5 diamonds) (ace spades)))
>######(member ’(5 diamonds) cards) → nil
>######(second cards) → (five diamonds)
>######(eql (second cards) ’(5 diamonds)) → nil
>######(equal (second cards) ’(5 diamonds)) → t

关键字：test可以再member函数中使用来指定一个相等判断的函数。我们在特定的引号后面写上函数名作为一种输入。
>######> (member ’(5 diamonds) cards :test #’equal)
>######((5 DIAMONDS) (ACE SPADES))

所有的列表函数，包括相等测试等等都接受一个：test关键字参数。remove函数是另一个例子，我们不能从cards中删除（5 diamonds）除非我们告诉remove使用equal作为相等判断。
>######> (remove ’(5 diamonds) cards)
>######((3 CLUBS) (5 DIAMONDS) (ACE SPADES))
>######> (remove ’(5 diamonds) cards :test #’equal)
>######((3 CLUBS) (ACE SPADES))

另一个接受：test关键字的函数是union，intersection和set-difference，assoc，rassoc，subst和sublis。为了找出函数接受哪一个关键字，可以使用在线文档。给函数输入他不支持的关键字的话会报错。
>######> (remove ’(ace spades) cards :reason ’bad-luck)
>######Error! :REASON is an invalid keyword argument
>######to REMOVE.

######进阶话题涉及函数
树函数:  SUBST, SUBLIS.
附加的相等函数:  EQ, EQL, EQUALP, =.
关键字断言:  KEYWORDP

