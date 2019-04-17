1. private修饰的方法可以通过反射访问，那么private的意义是什么
private设计为了表达封装而不是安全性，为了在常规java使用中提供访问约束。反射是一种hack技术。
2. java类初始化顺序
基类静态代码块和静态成员变量，子类静态代码块和静态成员变量，基类普通代码块和成员变量，基类构造函数。子类普通代码块和成员变量。子类构造函数。
代码块和成员变量的初始化顺序根据声明顺序。静态代码块和成员变量只会执行一次
3. 说一下方法区和永久区的关系
方法区是jvm规范中的定义，永久区是方法区的一种实现，比如jdk8中引入的元数据区
4. 一个java文件有三个类编译以后有几个类
三个类
5. 局部变量不不赋值使用会报编译错误，为什么这样设计。
局部变量没有赋值直接使用可能是程序员的疏忽，这样设计可以避免错误。
6. readwritwlock读写之间互斥吗？
读与读这间不互斥，只要有线程在写就互斥
7. semaphore拿到执行权的线程之间是否互斥
不互斥
8. b树和b+树有什么区别，分别解决什么问题，如何演化而来
b树和b+树都是数据库索引的实现方式。二叉树有退化现象，演化成平衡二叉树，为了减少遍历深度，演化为m叉平衡树。为了每一个节点多放key，进一步减少遍历深度所以引入b+树。
9. 写一个生产者消费者模式
lock+condition，java的blockingqueue就是典型的生产者和消费者模式
10. 写一个死锁
线程一拿到锁a后申请锁b，线程二拿到锁b后申请锁a
11. 如何定位cpu100%
使用visualvm将jvm的threaddump输出
12. string a=“ab” string b=“a”+“b” a和b是否相等
相等，用字面量形式创建的字符串会搜索字符串常量池，如果没找到会创建一个新的。
如果使用new方法则总是会新创建一个对象。可以使用intern方法手动将其放入字符串常量池。此优化的前提条件是java字符串的不可变性，不会被错误篡改。字符串常量池内部实现是hashmap，会消耗cpu。字符串常量池在永久代中，存的是弱引用，不会影响gc。
13. 接上一题，总共会创建几个对象
一个对象，编译期间会处理字符串拼接。
14. int a=1是否是原子操作
是
15. 给线程池新加一个任务，线程池会如何处理。
首先尝试让corethreads处理，失败则进队列，队列满则创建新的工作线程，再失败则交给饱和策略处理
16. aqs和cas原理
aqs使用cas加volatile加双向队列实现。具体实现依赖异步算法
cas在写操作前会比较当前值和预期值是否相等，如果不相等则进行重试。实现依赖CPU指令是原子操作。
17. synchronize的实现原理
通过monitorenter和monitorexit指令来获取和释放对象的monitor锁
monitor锁信息报存在对象头的markword中markword默认保存对象的hashcode以及分代年龄。当线程持有锁后，markword的lock record指针会指向线程的monitor record。monitor record是线程私有的数据，保存owner 线程id nest自旋计数 候选线程号等信息。偏向锁和轻量锁使用cas竞争锁，重量级锁会阻塞线程。偏向锁存在竞争会膨胀为轻量锁，轻量锁自旋一定次数会转换为重量级锁。
18. volalite关键字的所用
保证变量可见性，禁止重排序
19. aop和ioc原理
aop使用代理模式实现 ，代理分为静态代理和动态代理。ioc，将对象的控制权交给spring。程序直接使用即可，无需创建。
20. 使用final关键字修饰一个变量时，是引用不能变，还是引用的对象不能变？
引用不能变，实际对象的值还是能变的
21. string能被继承吗
不能 string对象是final的
22. string stringbuilder stringbuffer的区别
string不可变。stringbuffer线程安全  stringbuilder线程不安全
23. arraylist和linklist的区别
array list底层是数组，元素访问时间为o1 linklist访问为on。linklist插入操作比较快 只需更新指针，arraylist需要扩容和更新索引。linklist更加消耗内存，因为要多存两个指针。
24. 用过哪些 Map 类，都有什么区别，HashMap 是线程安全的吗,并发下使用的 Map 是什么，他们内部原理分别是什么，比如存储方式， hashcode，扩容， 默认容量等。
hashtable是遗留类并发使用concurrrenthashmap。hashmap使用数组加链表加红黑树实现。新增元素会使用高位参与的mod算法计算出桶索引，同时为了优化mod算法的速度，桶的长度必须是二的n次方大小。因为对二的n次方的取余操作可以用速度更快的位与操作代替。当桶内发生hash碰撞时，桶内元素会转换成链表。如果链表过长会转换成红黑树。读写操作时间复杂度分别为o1on和olgn。hashmap的扩容操作发生在put中触发条件由负载率参数控制。扩容后元素会重新分布，过程也收益与桶大小是2的n次方。
25. Spring框架中的单例bean是线程安全的吗
不安全
26. sleep和wait的区别
sleep是线程方法，挂起线程给其他线程执行时间，sleep不会释放线程拥有的对象锁 wait是object的方法，会释放对象锁锁 wait必须使用notify或者notify all来恢复运行
27. tcp如何进行流量控制的
tcp使用滑动窗口来进行流量控制。接收端使用win字段通知发送端最多可以发送多少未经确认的字节。发送端会维护一个窗口结构
。窗口结构分为四部分，分别问已发送已确认，已发送未确认，未发送已就绪和不能发送。当收到接收端的新ack时，如果win大小没有变化，此时窗口就会向右滑动。接受端也会维护一个窗口结构，保存已接收已确认和可以接受的最大序列号。当发送端收到一个零窗口通知时，会启动窗口探测机制，间歇性地去询问可用窗口情况。窗口探测机制是为了防止窗口更新包丢失导致的长时间等待。
28. java int和integer的区别
integer是int的包装类，目标是可以向操作java普通类型那样操作原始类型。java1.5开始支持自动拆箱和装箱操作。在将string转换成整型时应该根据目标类型选择正确的方法。valueof返回integer类型，parseint返回int类型
29. 什么是Callable和Future?
callable是带有返回值的runnable。可以通过future异步得获取callable的运行结果
30. java守护线程和本地线程的区别
守护线程在所用用户线程退出时也会退出。javagc线程就是守护线程
31. 什么是多线程cpu切换
当线程数量大于cpu数量时，为了使所有线程都获取执行时间，cpu会在线程间进行切换。
32. 为什么使用线程池
提供线程管理功能，能够重复利用线程，节省线程创建的消耗。提供门面，简化多线程编程。
33. Object类的equal和hashCode方法重写，为什么？
保证基于hash的集合类能够正常工作，比如在hashmap中判断两个对象相等的方法是hash和equals同时相等
34. 父类的静态方法能否被子类重写
不能，子类只能新加一个同名的方法。static方法程序开始运行后就被写到内存中
35. ==号和equals区别
==号比较原始对象的值，对于object比较的是引用不是对象本事。equals是object的方法，默认使用==比较，可以重写来进行对象比较，例如string
36. 什么是aop
面向切面是一种编程范式。在不影响原有代码的前提下进行代码织入。
如果spring的ExceptionHandler注释，拦截指定的异常进行处理。比如junitTest的beforeclass和afterclass注解。
37. restapi的意义
38. java如何判断对象可回收
引用计数器和可达性分析
39. 如何确定gc root
一般来讲根据虚拟机栈中的对象，方法区中的静态变量和常量，jni栈中的对象。gc root的确定和gc方式有关，比如g1做混合gc的时候会将survive中的对象当作roots
40. 什么是java
面向对象的高级编程语言。有跨平台的特性，编译成bytecode在jvm上运行而不用关心底层操作系统。有自动内存管理机制。
41. 抽象类的意义
抽象类提供接口方法的默认实现。抽象类也可以定义构造方法，其行为和普通类继承时一致。实例就是adapter模式。
提取公共方法。
42. utf-8编码中的中文占几个字节；int型几个字节？
utf-8是基于unicode字符集的一种变长的编码方式，最大表示31个bit的字符。使用前缀来实现变长。中文占三个字节，所有ascii码包括数字都可以用一个字节表示。
43. 说一下泛型原理，并举例说明
java的泛型有两种使用方式，跟在类型后方或者加在方法的修饰符里。一个方法是不是泛型方法和这个类是不是泛型的没有关系。java的泛型使用类型擦除机制实现，编译器会将泛型全部转换成上界类型。在runtime时java是不区分泛型的，比如对比两个参数类型不同的List的class，结果是相等的，使用javap反编译class文件也可以证实这一点。泛型的作用相比object能提供类型安全校验，避免写繁琐的类型转换代码，此外也能增强可读性。实战中经常利用泛型构造容器类。
44. String为什么要设计成不可变的？
常量池的实现中，保存的是string的引用。
类加载会用到string作为参数，使用不可变string更加安全。
不可变意味着线程安全。
45. ConcurrentHashMap的实现原理
ConcurrentHash数据结构与HashMap类似，使用桶+链表+红黑树实现。实现并发的核心技术是volalite关键字，cas，sychronize，用复杂的并发算法做支撑。基本数据结构为Node，另外用TreeNode表示红黑树，用TreeBin对红黑树进行封装。Node的hash值有两类，大于0表示正常Node节点的hash值，小于零表示特殊节点，特殊节点有ForwardBin表示正在扩容，TreeBin表示红黑树等。这样设计的好处是只需要判断一个bit就可以知道是常规bin还是特殊bin。put操作在没有发生hash碰撞时使用cas进行插入，最有效率，否则使用synchronize锁定一个bin进行插入操作。在插入式如果正好有一个扩容动作也在进行，put操作会转而去帮助一起扩容。table的初始化操作也是在put过程中触发的，如果有两个线程同时触发了初始化动作，那么竞争失败的一方会进行自旋同时尝试yield。链表转换成红黑树的操作也是在put完以后触发的。get方法底层使用unsafe的voliate方法读取一个bin，在bin进行遍历过程中也不用担心线程安全问题，因为Node的val和next指针也都是voliate的。get方法不会加锁。构造函数主要有两个参数初始容量，负载因子，和预计并发量。初始容量会保证大于等于预计并发量。
46. java yield的用法
表示可以挂起执行更重要的任务。给线程调度器一个提示，可以执行更重要的其他任务了，线程调度器可以不予理睬。yield并不能保证停顿。
47. error和exception区别
error表示很严重的问题，难以恢复。比如NoSuchMethodError，OutOfMemeoryError，StackOverFlowError。
Exception表示正常运行不会碰到的问题，比如IndexOutOfBound， NoSuchFile， IllegalArguement，NullPointException。
48. java什么时候会发生内存泄漏
当不使用的对象没有被回收的时候。长生命周期的对象引用了短生命周期的对象。比如往一个全局的map中不停的放对象，但是又不使用。ConcurrentHashMap一旦扩容以后无法减小，所以也有可能造成内存泄漏。
49. Vector线程安全吗？
安全。使用synchronize实现的。
50. 什么是Java Timer类？如何创建一个有特定时间间隔的任务？
工具类，用来定时或者周期性执行任务。使用time schedule一个timetask。timetask是一个runnable对象。
51. threadlocal的用法和意义
















 
