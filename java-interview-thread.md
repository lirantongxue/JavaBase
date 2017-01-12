title: "Java研发复习笔记(四)--多线程"
date: 2015-10-22 11:13:00
author: "郑江龙"
description: java集合知识总结
categories: interview
tags:
    - Java
    - interview
---
## 什么是线程
线程是操作系统能够进行运算调度的最小单位，它被包含在进程之中，是进程中的实际运作单位。程序员可以通过它进行多处理器编程，你可以使用多线程对运算密集型任务提速。比如，如果一个线程完成一个任务要 100 毫秒，那么用十个线程完成改任务只需 10 毫秒。Java 在语言层面对多线程提供了卓越的支持，它也是一个很好的卖点。欲了解更多详细信息请点击这里。

## 什么叫线程安全？举例说明
多个线程访问某个类时，不管运行时环境采用何种调度方式或者这些线程将如何交替执行，并且在主调代码中不需要任何额外的同步或者协同，这个类都能表现出正确的行为，那么就称这个类是线程安全的。
比如无状态对象一定是线程安全的。Vector 是用同步方法来实现线程安全的， 而和它相似的 ArrayList 不是线程安全的

## 什么是多线程中的上下文切换？
任务从保存到再加载的过程就是一次上下文切换．
减少上下文切换的方式是:
无锁并发编程：多线程竞争锁时会引起上下文切换．
CAS算法：不需要加锁，可以减少上下文切换
使用最少线程：创建大量线程造成大量线程都处于等待期间
协程：在单线程里实现多任务的调度，并在但线程里维持多个任务间的切换.如NIO的selector

## Java 中什么是竞态条件？ 举个例子说明。
当某个正确的计算结果取决与多个线程的交替执行时序时,线程因不恰当的执行时序而导致不正确的结果,这就是竞态条件.
竞态条件会导致程序在并发情况下出现一些 bugs。多线程对一些资源的竞争的时候就会产生竞态条件，如果首先要执行的程序竞争失败排到后面执行了，那么整个程序就会出现一些不确定的 bugs。这种 bugs 很难发现而且会重复出现，因为线程间的随机竞争。

##  进程和线程的区别
调度: 线程是调度的基本单位，进程是拥有资源的基本单位。同一进程的中线程的切换不会引起进程的切换，不同进程中进行线程切换会引起进程的切换。
拥有资源：进程是拥有资源的基本单位，线程除了自身的栈外一般不拥有资源。而是和其他线程共享同一进程中的资源。
系统开销：由于创建进程或者撤销进程时，系统都要分配和回收资源，如内存空间，I/O设备等，操作系统所付出的开销远大于创建或撤销进程时的开销。

## 线程的状态
线程在运行周期里有6中不同的状态。
New 新建
RUNNABLE 运行状态,操作系统中运行与就绪两种状态统称运行中
BLOCKED 阻塞状态
WAITING	等待状态
TIME_WAITING 超时等待
TERMINATED	终止状态

## volatile的理解
**Volatile自身特性**：
Volatile 是轻量级的synchronized，它在多处理器开发过程中保证了共享变量的“可见性”，可见性是指当一个线程的某个共享变量发生改变时，另一个线程能够读取到这个修改的值。Voaltile变量修饰的变量在进行写操作时在多核处理器下首先将当前处理器缓存行的数据写回到系统内存中。为了保证一致性，其他处理器嗅探到总线上传播的数据，发现数据被修改了自己缓存地址的数据无效。
Volatile 可以禁止重排序，
Volatile 能保持单个简单volatile变量的读/写操作的具有原子性。但不能保证自增自减的原子性。	
从**内存语义**来讲:
volatile变量的写-读与锁的释放-获取具有相同语义，volatile的写与锁的释放有相同的内存语义，volatile读与锁的获取具有相同语义。
线程A写一个volatile变量，实质上是线程A向接下来要读这个volatile变量的某个线程发出消息
线程B读一个volatile变量，实质上是线程B接收了之前某个线程发出的消息。
线程A写volatile变量，随后线程B读这个变量，这个过程实质上线程A通过内存向B发送消息。
内存语义的实现，也是禁止重排序特性：
为了实现volatile内存语义，JMM限制了对volatile重排序做了限制：
1.当第二个操作是volatile写时，不管第一个操作时什么，都不能重排序。
2.当第一个操作是volatile读时，不管第二个操作是什么，都不能重排序。
3.当第一个操作是volatile写，第二个操作是volatile读时，不重排序。
为了实现volatile的内存语义，编译器生成字节码时，会在指令序列中插入内存屏障来禁止特定类型的处理器重排序。JMM采取保守策略。
1. 在每个volatile写操作前面插入一个StoreStore屏障
2. 在每个volatile写操作后面插入一个StoreLoad屏障
3. 在每个volatile读操作后面插入一个LoadLoad屏障
4. 在每个volatile读操作后面插入一个LoadStore屏障
		
## 原子性实现机制
处理器提供总线锁定和缓存锁定两种方式来保证复杂内存操作的原子性。
总线型：就是使用处理器提供一个LOCK信号，当一个处理器在总线传输信号时，其他处理器的请求将被阻塞住，那么该处理独占内存。所以总线锁定开销大。
缓存锁定：内存区域如果被缓存在缓存行中，且在在lock期间被锁定，当它执行锁操作写回内存时，处理器总线不在锁定而是通过修改内部的内存地址并使用缓存一致性制阻止同时修改保证操作的原子性。缓存一致性进制两个以上的处理器同时修改内存区域数据，其他处理器回写被锁定并且使其缓存行无效。

## Java原子性操作实现原理
使用循环CAS实现原子性操作，CAS是在操作期间先比较旧值，如果旧值没有发生改变，才交换成新值，发生了变化则不交换。这种方式会产生以下几种问题：1.ABA问题，通过加版本号解决；2.循环时间过长开销大，一般采用自旋方式实现；3. 只能保证一个共享变量的原子操作。 

## Java内存模型
　Java内存模型的主要目标是**定义程序中各个变量的访问规则，即在虚拟机中将变量存储到内存和从内存中取出变量这样底层细节。它决定了一个线程对共享变量的写入何时对另一个线程可见**。它属于语言及的内存模型，它确保在不同编译器和不同的处理平台之上，通过**禁止特定类型的编译器重排序和处理器重排序**，为程序员提供一致的内存可见性保证。
As-if-serial保证了单线程环境下结果的正确性，JMM为多线程提供以下保证。如果程序是正确同步的那么程序的执行将具有一致性。顺序一致性是具有量大特性：所有操作按顺序执行；所有线程都只能看到一个单一的操作顺序。未正确同步的程序JMM不但整体的执行顺序是无序的，而且线程看到的操作顺序是不一致的。
JMM的核心目标是找到一个好的平衡点，一方面是为**程序员**提供足够强的内存可见性保证（提供**happens-before**规则），另一方面对编译器和处理器的限制尽可能地放松（只要不改变程序结果，怎么优化都可以）。
JMM中如果一个操作执行的结果需要对另一个操作可见，那么这两个操作(同一个线程或不同线程)必须存在happens-before关系。
程序顺序规则：一个线程的每个操作happen-before与该线程的任意后续操作。
监视器锁规则：一个锁的解锁，happens-before于随后这个锁的加锁。
Volatile变量规则：对一个volatile域的写，happens-before于任意后续这个域的读。
传递性规则
Start()规则：如果线程A执行操作ThreadB.start().那么线程A中的任意操作happens-before与线程B中的任意操作。
Join()规则：如果线程A执行操作ThreadB.join()并成功返回，那么B中的任意操作Happens-before于线程A。

重排序：编译器和处理器为了优化程序性能对指令进行重新排序的一种手段。
数据依赖性：如果两个操作访问同一个变量，有一个操作为写操作，这两个操作存在数据依赖性。在单线程环境中，编译器和处理器的重排序会遵守数据依赖性。
As-if-serial: 不管怎么重排序，单线程执行的结果不能被改变。
另外JMM不保证对64位的long和double型变量的写操作具有原子性。

## Final域的内存语义
对于final域编译器和处理器要遵守两个重排序规则：
1.在构造器函数内对final域的写入，与随后把这个被构造对象的引用赋值给一个引用变量，这两个操作之间不能重排序。（保证了对象引用为任何线程可见之前，对象的final域已经被正确初始化过）
2.初次读一个包含final域的对象引用，与随后初次读这个final域这两个操作不能重排序。
为何保证其内存语义：可以为java程序员提供安全保证，只要对象是正确构造的，那么不需要使用同步就可以保证线程都能看到这个fianal域在构造函数中被初始化之后的值。

## 避免死锁的常见方法：
避免死锁最简单的方法就是阻止循环等待条件，将系统中所有的资源设置标志位、排序，规定所有的进程申请资源必须以一定的顺序（升序或降序）做操作来避免死锁.
1)避免一个线程同时获取多个锁
2)避免一个线程在锁内同时占用多个资源，尽量保证每个锁只占用一个资源
3)尝试使用定时锁，使用lock.tryLock(timeout)来替代使用内部锁机制。(破坏请求与保持的条件)
4)对数据库锁，加锁和解锁必须在一个数据库连接里，否则会出现解锁失败的情况。

## 死锁的必要条件？怎么克服？
答：产生死锁的四个必要条件：
**互斥条件**：一个资源每次只能被一个进程使用。
**请求与保持条件**：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
**不剥夺条件**:进程已获得的资源，在末使用完之前，不能强行剥夺。
**循环等待条件**:若干进程之间形成一种头尾相接的循环等待资源关系。
这四个条件是死锁的必要条件，只要系统发生死锁，这些条件必然成立，而只要上述条件之一不满足，就不会发生死锁。

死锁的解决方法:
a 撤消陷于死锁的全部进程；
b 逐个撤消陷于死锁的进程，直到死锁不存在；
c 从陷于死锁的进程中逐个强迫放弃所占用的资源，直至死锁消失。
d 从另外一些进程那里强行剥夺足够数量的资源分配给死锁进程，以解除死锁状态

##　Java 中活锁和死锁区别
活锁和死锁类似，同死锁一样，发生活锁的线程无法继续执行。然而线程并没有阻塞，处于活锁的线程或进程的状态是不断改变的，活锁可以认为是一种特殊的饥饿。这就相当于两个在走廊相遇的人：A向他自己的左边靠想让B过去，而B向他的右边靠想让A过去,相互避让。但是因为避让的方向都一样导致最后谁都不能通过走廊，简单的说就是，活锁和死锁的主要区别是前者进程的状态可以改变但是却不能继续执行。

## 如何在 Java 中实现线程
在语言层面有两种方式。java.lang.Thread 类的实例就是一个线程但是它需要调用 java.lang.Runnable 接口来执行，由于线程类本身就是调用的 Runnable 接口所以你可以继承 java.lang.Thread 类或者直接调用 Runnable 接口来重写 run ()方法实现线程。

## Java 中如何停止一个线程
Java 提供了很丰富的 API 但没有为停止线程提供 API。JDK 1.0 本来有一些像 stop ()， suspend () 和 resume ()的控制方法但是由于潜在的死锁威胁因此在后续的 JDK 版本中他们被弃用了，之后 Java API 的设计者就没有提供一个兼容且线程安全的方法来停止一个线程。当 run () 或者 call () 方法执行完的时候线程会自动结束.
1)如果要手动结束一个线程，你可以用 volatile 布尔变量来退出 run ()方法的循环或者是取消任务来中断线程。
2)另一种是采用中断机制，

## 用 Runnable 还是 Thread
这个问题是上题的后续，大家都知道我们可以通过继承 Thread 类或者调用 Runnable 接口来实现线程，问题是，那个方法更好呢？什么情况下使用它？这个问题很容易回答，如果你知道 Java 不支持类的多重继承，但允许你调用多个接口。所以如果你要继承其他类，当然是调用 Runnable 接口好了。

## Java 中 Runnable 和 Callable 有什么不同
Runnable和 Callable 都代表那些要在不同的线程中**执行的任务**。Runnable 从 JDK1.0 开始就有了，Callable 是在 JDK1.5 增加的。它们的主要区别是 Callable 的**call () 方法可以返回值和抛出异常**，而 Runnable 的 run ()方法没有这些功能。Callable 可以返回装载有计算结果的**Future**对象。

## CountDownLatch(闭锁) 与CyclicBarrier(栅栏)的区别
CountDownLatch: 允许一个或多个线程等待**其他**线程完成操作．　CyclicBarrier让一组线程达到一个屏障时被阻塞，直到最后一个线程到达时，屏障才会开门．
1.闭锁CountDownLatch做减计数，而栅栏CyclicBarrier则是加计数。
2.CountDownLatch是一次性的，CyclicBarrier可以重用。
3.CountDownLatch一个线程(或者多个)，等待另外N个线程完成某个事情之后才能执行。CyclicBarrier是N个线程相互等待，任何一个线程完成之前，所有的线程都必须等待。 
CountDownLatch 是计数器, 线程完成一个就记一个,就像报数一样, 只不过是递减的.
而CyclicBarrier更像一个水闸, 线程执行就像水流, 在水闸处都会堵住, 等到水满(线程到齐)了, 才开始泄流.

## execute 和submit的区别
Execute()用于提交不需要返回值得任务，submit()用于提交需要返回值的任务，返回Future类型的对象。

## Shutdown和shutdownNow的区别
它们的原理都是遍历线程池中的工作线程，然后逐个调用线程的Internet方法来中断线程，所以无法响应中断的任务可能永远无法终止。
ShutdownNow采用粗暴的关闭过程，它会尝试取消所有运行中的任务，并且不在启动队列中尚未完成的执行任务．
shutdown将执行平缓的关闭过程，不再接受新提交的任务，同时等待已经提交的任务执行完成(包括未开始执行的任务)．它只是将线程池设置成SHUTDOWN状态，然后中断没有正在执行任务的线程。

## 同步
同步就是协同步调，按预定的先后次序进行运行。

## sleep() 和 wait() 区别
答：sleep()方法是线程类（Thread）的静态方法，导致此线程暂停执行指定时间，将执行机会给其他线程，但是监控状态依然保持，到时后会自动恢复（线程回到就绪（ready）状态），因为调用 sleep 不会释放对象锁。wait() 是 Object 类的方法，对此对象调用 wait()方法导致本线程放弃对象锁(线程暂停执行)，进入等待此对象的等待锁定池，只有针对此对象发出 notify 方法（或 notifyAll）后本线程才进入对象锁定池准备获得对象锁进入就绪状态。

## sleep() 和 yield() 区别
① sleep() 方法给其他线程运行机会时**不考虑线程的优先级**，因此会给低优先级的线程以运行的机会；yield() 方法**只会给相同优先级或更高优先级**的线程以运行的机会；
② 线程执行 sleep() 方法后转入**阻塞**（blocked）状态，而执行 yield() 方法后转入**就绪（ready）**状态；
③ sleep() 方法声明抛出InterruptedException，而 yield() 方法没有声明任何异常；
④ sleep() 方法比 yield() 方法（跟操作系统相关）具有更好的可移植性。

## 线程同步相关的方法。
**wait()**:使一个线程处于等待（阻塞）状态，并且释放所持有的对象的锁；
**sleep()**:使一个正在运行的线程处于睡眠状态，是一个静态方法，调用此方法要捕捉InterruptedException 异常；
**notify()**:唤醒一个处于等待状态的线程，当然在调用此方法的时候，并不能确切的唤醒某一个等待状态的线程，而是由JVM确定唤醒哪个线程，而且与优先级无关；
**notityAll()**:唤醒所有处入等待状态的线程，注意并不是给所有唤醒线程一个对象的锁，而是让它们竞争；

## 什么是线程池（thread pool）
答：在面向对象编程中，创建和销毁对象是很费时间的，因为创建一个对象要获取内存资源或者其它更多资源。在 Java 中更是如此，虚拟机将试图跟踪每一个对象，以便能够在对象销毁后进行垃圾回收。所以提高服务程序效率的一个手段就是尽可能减少创建和销毁对象的次数，特别是一些很耗资源的对象创建和销毁，这就是"池化资源"技术产生的原因。线程池顾名思义就是事先创建若干个可执行的线程放入一个池（容器）中，需要的时候从池中获取线程不用自行创建，使用完毕不需要销毁线程而是放回池中，从而减少创建和销毁线程对象的开销。

## ConcurrentHashMap实现原理
ConcurrentHashMap和Hashtable主要区别就是围绕着锁的粒度以及如何锁。
Hashtabl在竞争激烈的环境下表现效率低下的原因是一把锁锁住整张表，导致所有线程同时竞争一个锁。ConcurrentHashMap采用分段锁，每把锁锁住容器中的一个Segment。那么多线程访问容器里不同的Segment的数据时线程就不会存在竞争，从而有效提高并发访问效率。首先是将数据分层多个Segment存储，并为每个Segment分配一把锁，当一个线程范围其中一段数据时，其他线程可以访问其他段的数据。

数据结构：
ConcurrentHashMap内部是有Segment数组和HashEntry数组组成。一个ConcurrentHashMapMap里包含一个Segment数组，而Segment的结构和HashMap一样，里面是由一个数组和链表结构组成，所以一个Segment内部包含一个HashEntry数组。每个HashEntry是一个链表结构，对于HashEntry数组进行修改时首先需要获取与它对应的Segment锁。默认情况下有16个Segment

Segment的定位:
使用Wang/Jenkins hash变种算法对元素的hashCode进行一次再散列，目的是为了减少散列冲突。

ConcurrentHashMap的操作:
1.) get
get操作实现非常简单高效。先经过一次在散列，然后用这个散列值通过散列运算定位到Segment，再通过散列算法定位到元素。get之所以高效是因为整个get过程不需要加锁，除非读到空值才会加锁重读。实现该技术的技术保证是保证HashEntry是不可变的。
第一步是访问count变量，这是一个volatile变量，由于所有的修改操作在进行结构修改时都会在最后一步写count 变量，通过这种机制保证get操作能够得到几乎最新的结构更新。对于非结构更新，也就是结点值的改变，由于**HashEntry的value变量是 volatile的，也能保证读取到最新的值**。接下来就是根据hash和key对hash链进行遍历找到要获取的结点，如果没有找到，直接访回null。
对hash链进行遍历不需要加锁的原因在于链指针**next是final**的。但是头指针却不是final的，这是通过getFirst(hash)方法返回，也就是存在 table数组中的值。这使得getFirst(hash)可能返回**过时的头结点**，例如，当执行get方法时，刚执行完getFirst(hash)之后，另一个线程执行了删除操作并更新头结点，这就导致get方法中返回的头结点不是最新的。这是可以允许，通过对count变量的协调机制，get能读取到几乎最新的数据，虽然可能不是最新的。要得到最新的数据，只有采用完全的同步。
2.) put
该方法也是在持有**段锁**(锁定当前segment)的情况下执行的，这当然是为了并发的安全，修改数据是不能并发进行的，必须得有个判断是否超限的语句以确保容量不足时能够rehash。首先根据计算得到的散列值定位到segment及该segment中的散列桶中。接着判断是否存在同样一个key的结点，如果存在就直接替换这个结点的值。**否则创建一个新的结点并添加到hash链的头部**，**这时一定要修改modCount和count的值**，同样修改count的值一定要放在最后一步。
3）remove
**HashEntry中除了value不是final的，其它值都是final的**，这意味着**不能从hash链的中间或尾部添加或删除节点**，因为这需要修改next 引用值，所有的节点的**修改只能从头部开始**。对于put操作，可以一律添加到Hash链的头部。但是对于remove操作，可能需要从中间删除一个节点，这就需要将要删除节点的前面所有节点整个复制一遍，最后一个节点指向要删除结点的下一个结点。
首先定位到要删除的节点e。如果不存在这个节点就直接返回null，否则就要将e前面的结点复制一遍，尾结点指向e的下一个结点。e后面的结点不需要复制，它们可以重用。
4) size()
每个Segment都有一个count变量，是一个volatile变量。当调用size方法时，首先先尝试2次通过不锁住segment的方式统计各个Segment的count值得总和，如果两次值不同则将锁住整个ConcurrentHashMap然后进行计算。
参见《java并发编程的艺术》P156
http://www.cnblogs.com/ITtangtang/p/3948786.html

hashMap 多线程情况下put方法会导致死循环, 
## Java 中 notify 和 notifyAll 区别
notify()方法能够唤醒一个正在等待该对象的monitor的线程，当有多个线程都在等待该对象的monitor的话，则只能唤醒其中一个线程，具体唤醒哪个线程则不得而知。而 notifyAll ()唤醒所有线程并允许他们争夺锁确保了至少有一个线程能继续运行。

## 为什么 wait, notify 和 notifyAll 这些方法不在 thread 类里面？
这是个设计相关的问题，它考察的是面试者对现有系统和一些普遍存在但看起来不合理的事物的看法。回答这些问题的时候，你要说明为什么把这些方法放在 Object 类里是有意义的，还有不把它放在 Thread 类里的原因。一个很明显的原因是 JAVA 提供的**锁是对象级的而不是线程级的**，每个对象都有锁，通过线程获得。如果线程需要等待某些锁那么调用对象中的 wait ()方法就有意义了。**如果 wait ()方法定义在 Thread 类中，线程正在等待的是哪个锁就不明显了**。简单的说，由于 wait，notify 和 notifyAll 都是锁级别的操作，所以把他们定义在 Object 类中因为锁属于对象。

## ThreadLocal的设计理念与作用。
ThreadLocal并不是一个Thread，而是Thread的局部变量，ThreadLocal为每个使用该变量的线程提供独立的变量副本，所以每一个线程都可以独立地改变自己的副本，而不会影响其它线程所对应的副本.只要线程是活动的并且 ThreadLocal 实例是可访问的；在线程消失之后，其线程局部实例的所有副本都会被垃圾回收（除非存在对这些副本的其他
每个线程中可有多个threadLocal变量,所以每个线程中都是有一个ThreadLocalMap对象，其中key为ThreadLocal对象，value为某个对象(即存储变量)。在调用ThreadLocal对象的set方法方法时,首先会获取当前线程,并获取当前的ThreadLocalMap.如果 ThreadLocalMap是空的那么会创建一个ThreadLocalMap对象保存到当前线程中,最后将ThreadLocal对象和值的映射关系保存到ThreadLocalMap中. 在进行get之前，必须先set，否则会报空指针异常；get()的流程和set()的流程大致相同,首先会获取线程的ThreadLocalMap,如果ThreadLocalMap为空则创建一个新ThreadLoacalMap,并返回调用initialValue()的值,默认情况下时返回null.如果ThreadLoacalMap不为空但不存在当前ThreadLocal值时也会返回调用initialValue()方法的值.如果想在get之前不需要调用set就能正常访问的话，必须重写initialValue()方法。
http://www.cnblogs.com/dolphin0520/p/3920407.html

## Java 中的同步集合与并发集合区别
同步集合与并发集合都为多线程和并发提供了合适的线程安全的集合，不过并发集合的可扩展性更高。在 Java1.5 之前程序员们只有同步集合来用且在**多线程并发的时候会导致争用，阻碍了系统的扩展性**。Java5 介绍了并发集合像 ConcurrentHashMap，**不仅提供线程安全还用锁分离和内部分区等现代技术提高了可扩展性**。

## 为什么你应该在循环中检查等待条件
处于等待状态的线程可能会收到错误警报和伪唤醒，如果不在循环中检查等待条件，程序就会在没有满足结束条件的情况下退出。因此，当一个等待线程醒来时，不能认为它原来的等待状态仍然是有效的，在 notify ()方法调用之后和等待线程醒来之前这段时间它可能会改变。这就是在循环中使用 wait ()方法效果更好的原因.

## 如何写代码来解决生产者消费者问题
在现实中你解决的许多线程问题都属于生产者消费者模型，就是一个线程生产任务供其它线程进行消费，你必须知道怎么进行线程间通信来解决这个问题。比较低级的办法是用 wait 和 notify 来解决这个问题，比较赞的办法是用 Semaphore 或者 BlockingQueue 来实现生产者消费者模型.

		public class ProducerConsumerPattern {
		    public static void main(String args[]){
		     //Creating shared object
		     BlockingQueue sharedQueue = new LinkedBlockingQueue();
		 
		     //Creating Producer and Consumer Thread
		     Thread prodThread = new Thread(new Producer(sharedQueue));
		     Thread consThread = new Thread(new Consumer(sharedQueue));
		 
		     //Starting producer and Consumer thread
		     prodThread.start();
		     consThread.start();
		    }
		}
		 
		//Producer Class in java
		class Producer implements Runnable {
		    private final BlockingQueue sharedQueue;
		    public Producer(BlockingQueue sharedQueue) {
			this.sharedQueue = sharedQueue;
		    }
		    @Override
		    public void run() {
			for(int i=0; i<10; i++){
			    try {
				System.out.println("Produced: " + i);
				sharedQueue.put(i);
			    } catch (InterruptedException ex) {
				Logger.getLogger(Producer.class.getName()).log(Level.SEVERE, null, ex);
			    }
			}
		    }
		}
		 
		//Consumer Class in Java
		class Consumer implements Runnable{
		    private final BlockingQueue sharedQueue;
		    public Consumer (BlockingQueue sharedQueue) {
			this.sharedQueue = sharedQueue;
		    }
		    @Override
		    public void run() {
			while(true){
			    try {
				System.out.println("Consumed: "+ sharedQueue.take());
			    } catch (InterruptedException ex) {
				Logger.getLogger(Consumer.class.getName()).log(Level.SEVERE, null, ex);
			    }
			}
		    }
		}

#同步块与同步方法的区别
从反编译后的结果看，对于同步块使用了monitorenter和monitorexit指令，而同步方法则是依靠方法上的修饰符ACC_SYNCHRONIZED来完成，但它们的本质都是对一个对象监视器进行获取，而这个获取过程是排他的。
同步方法的锁是方法级别，锁定当前对象或类，同步块锁比较灵活可以选择对象锁或类锁．
	
## 显示锁ReentrantLock与内置锁synchronized的相同与区别
相同：显示锁与内置锁在加锁和内存上提供的语义相同(互斥访问临界区)
不同：
1) 使用方式，内置无需指定释放锁，简化锁操作。
2) 功能上：显示锁提供了其他很多功能如定时锁等待、可中断锁等待、公平性、尝试非阻塞获取锁、以及实现非结构化的加锁。（一个线程获取不到锁而被阻塞在synchronized之外时，对该线程进行中断操作，此时该线程的中断表示为会被修改，但线程依旧会被阻塞在synchronized上，等待获取锁。）
3) 对死锁的处理：内置只能重启，显示可以通过设置超时获取锁来避免
4) 性能上：java1.5 显示远超内置，java1.6 显示锁稍微比内置好

## 怎么检测一个线程是否拥有锁
我一直不知道我们竟然可以检测一个线程是否拥有锁，直到我参加了一次电话面试。在 java.lang.Thread 中有一个方法叫 holdsLock ()，它返回 true 如果当且仅当当前线程拥有某个具体对象的锁。

## 你如何在 Java 中获取线程堆栈？
对于不同的操作系统，有多种方法来获得 Java 进程的线程堆栈。当你获取线程堆栈时，JVM 会把所有线程的状态存到日志文件或者输出到控制台。在 Windows 你可以使用 Ctrl + Break 组合键来获取线程堆栈，Linux 下用 kill -3 命令。你也可以用 jstack 这个工具来获取，它对线程 id 进行操作，你可以用 jps 这个工具找到 id。

## 什么是线程池？ 为什么要使用它
1) 资源开销--虚拟机创建销毁，内存开销
2) 响应时间
3) 异常

创建线程要花费昂贵的资源和时间，如果任务来了才创建线程那么响应时间会变长，而且一个进程能创建的线程数有限。为了避免这些问题，在程序启动的时候就创建若干线程来响应处理，它们被称为线程池，里面的线程叫工作线程。从 JDK1.5 开始，Java API 提供了 Executor 框架让你可以创建不同的线程池。比如单线程池，每次处理一个任务；数目固定的线程池或者是缓存线程池（一个适合很多生存期短的任务的程序的可扩展线程池）

## Java 中 interrupted 和 isInterruptedd 方法的区别
interrupted () 和 isInterrupted ()的主要区别是前者会将中断状态清除而后者不会。Java 多线程的中断机制是用内部标识来实现的，调用Thread.interrupt ()来中断一个线程就会设置中断标识为 true。当中断线程调用静态方法Thread.interrupted ()来检查中断状态时，**中断状态会被清零**。而非静态方法 isInterrupted ()用来查询其它线程的中断状态且不会改变中断状态标识。**简单的说就是任何抛出 InterruptedException 异常的方法都会将中断状态清零**。无论如何，一个线程的中断状态有有可能被其它线程调用中断来改变。

## 什么是 FutureTask
Future就是对于具体的Runnable或者Callable任务的执行结果进行取消、查询是否完成、获取结果。必要时可以通过get方法获取执行结果，该方法会阻塞直到任务返回结果。
在 Java 并发程序中 FutureTask 表示一个**可以取消的异步运算**,实现了Runnable, Future接口。它有启动和取消运算、查询运算是否完成和取回运算结果等方法。只有当运算完成的时候结果才能取回，如果运算尚未完成 get 方法将会阻塞。一个 FutureTask 对象可以对调用了 Callable 和 Runnable 的对象进行包装，由于 FutureTask 也是调用了 Runnable 接口所以它可以提交给 Executor 来执行。
它同时实现了Runnable和Future两个接口,
http://blog.csdn.net/kobejayandy/article/details/46293927
http://ifeve.com/futuretask-source/

## 个线程运行时发生异常会怎样
这是我在一次面试中遇到的一个很刁钻的 Java 面试题, 简单的说，如果异常没有被捕获该线程将会停止执行。Thread.UncaughtExceptionHandler 是用于处理未捕获异常造成线程突然中断情况的一个内嵌接口。当一个未捕获异常将造成线程中断的时候 JVM 会使用 Thread.getUncaughtExceptionHandler ()来查询线程的 UncaughtExceptionHandler 并将线程和异常作为参数传递给 handler 的 uncaughtException ()方法进行处理。

## 如何强制启动一个线程
这个问题就像是如何强制进行 Java 垃圾回收，目前还没有觉得方法，虽然你可以使用 System.gc ()来进行垃圾回收，但是不保证能成功。在 Java 里面没有办法强制启动一个线程，它是被线程调度器控制着且 Java 没有公布相关的 API

## Java 中的 ReadWriteLock 是什么
同一时刻允许多个读线程访问，但是在写线程访问时所有的读线程和其他写线程均被阻塞，读写锁维护一对锁，一个读锁和一个写锁，通过分离读锁和写锁，使得并发性相比一般的排他锁有了很大的提升空间．**它支持公平性和非公平性的锁获取方式，默认是非公平性方式**．同时支持**重进入**，只要获取了读锁或写锁，该进程能够再次进入临界区．另外还支持**锁降级**操作，遵循获取写锁，获取读锁然后是否写锁得到读锁的过程．
内部也是采用**同步器**实现，为了支持两种不同的锁，它将状态变量切分为两个部分，**高16位表示读，低16位表示写**．

## Java 中的 fork join 框架是什么
fork join 框架是 JDK7 提供的用于**并行执行任务的框架**．目的是将所有可用的处理能力用来提升程序的性能，它把一个大任务递归分隔成若干个小任务，执行这些小任务，最终汇总每个小任务的结果得到大任务的结果．Java 开发人员可以通过它充分利用现代服务器上的多处理器。**fork join 框架一个巨大的优势是它使用了工作窃取算法，可以完成更多任务的工作线程可以从其它线程中窃取任务来执行**。

## 现在有T1、T2、T3三个线程，你怎样保证T2在T1执行完后执行，T3在T2执行完后执行？
这个线程问题通常会在第一轮或电话面试阶段被问到，目的是检测你对”join”方法是否熟悉。这个多线程问题比较简单，可以用join方法实现

## 用Java实现阻塞队列
用Condition实现了一个可阻塞队

		public class BoundedBuffer {
			final Lock lock = new ReentrantLock();
			final Condition notFull = lock.newCondition();
			final Condition notEmpty = lock.newCondition();
			final Object[] items; // 阻塞队列
			int putptr, takeptr, count;
			public BoundedBuffer(int capacity){
				 items = new Object[capacity];	
			}

			public void put(Object x){
				lock.lock();
				try {
					while (count == items.length) { // 如果队列满了，notFull就一直等待
						notFull.await(); // 调用await的意思取反，及not notFull -> Full
					}
					items[putptr] = x; // 终于可以插入队列
					if (++putptr == items.length)
						putptr = 0; // 如果下标到达数组边界，循环下标置为0
					++count;
					notEmpty.signal(); // 唤醒notEmpty
				}catch(InterruptedException e) {
					e.printStackTrace();
				}finally {
					lock.unlock();
				}
			}

			public Object take(){
				lock.lock();
				try {
					while (count == 0) {
						notEmpty.await();
					}
					Object x = items[takeptr];
					if (++takeptr == items.length)
						takeptr = 0;
					--count;
					notFull.signal();
					return x;
			    	}catch(InterruptedException e) {
					e.printStackTrace();
				}finally {
					lock.unlock();
			    	}
			}

		public static void main(String[] args) throws InterruptedException {
			final BoundedBuffer bb = new BoundedBuffer();
			new Thread(new Runnable() {
			@Override
			public void run() {
				try {
					Thread.sleep(1000);
					System.out.println(Thread.currentThread()+","+bb);
					bb.put("xx");
					bb.put("yy");
					bb.put("zz");
					bb.put("zz");
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			　　　}
			}).start();
			bb.take();
			}
		}

如果不使用JUC，大概是这样的：


		public class BoundedBuffer_Synchronized {
			private Object[] items = new Object[2];
			private Object notEmpty = new Object();
			private Object notFull = new Object();
			int count,putidx,takeidx;

			public  void put(Object obj) throws InterruptedException{
				synchronized(notFull){
					while(count == items.length){
						notFull.wait();
					}
					items[putidx] = obj;
				 	if(++putidx == items.length){
						putidx = 0;
					}
					count ++;
					notEmpty.notify();
				}
			 	
			}
			public Object take() throws InterruptedException{
				synchronized(notEmpty){
				while(count == 0){ // 啥也没有呢 取啥
					notEmpty.wait();
				}
				Object x = items[takeidx];
			        System.out.println("取第"+takeidx+"个元素"+x);
			        if(++takeidx == items.length){
				    takeidx = 0; 
			        }
			         count --;
				 notFull.notify();
			    }
			    return x;
			}
			public static void main(String[] args) throws InterruptedException {
			    final BoundedBuffer_Synchronized bb = new BoundedBuffer_Synchronized();
			    System.out.println(Thread.currentThread()+","+bb);

			    new Thread(new Runnable() {
				@Override
				public void run() {
				    try {
					Thread.sleep(1000);
					System.out.println(Thread.currentThread()+","+bb);
					bb.put("xx");
					bb.put("yy");
					bb.put("zz");
					bb.put("zz");
					bb.put("zz");
				    } catch (InterruptedException e) {
					e.printStackTrace();
				    }
				}
			    }).start();
			    bb.take();
			    bb.take();
			}
		}


## 为什么我们调用start()方法时会执行run()方法，为什么我们不能直接调用run()方法？
这是另一个非常经典的java多线程面试问题。这也是我刚开始写线程程序时候的困惑。现在这个问题通常在电话面试或者是在初中级Java面试的第一轮被问到。这个问题的回答应该是这样的，当你调用start()方法时你将创建新的线程，并且执行在run()方法里的代码。但是如果你直接调用run()方法，它不会创建新的线程也不会执行调用线程的代码。阅读我之前写的《start与run方法的区别》这篇文章来获得更多信息。

## Java中你怎样唤醒一个阻塞的线程？
这是个关于线程和阻塞的棘手的问题，它有很多解决方法。如果线程遇到了IO阻塞，我并且不认为有一种方法可以中止线程。如果线程因为调用wait()、sleep()、或者join()方法而导致的阻塞，你可以中断线程，并且通过抛出InterruptedException来唤醒它。我之前写的《How to deal with blocking methods in java》有很多关于处理线程阻塞的信息。


## 什么是不可变对象，它对写并发应用有什么帮助？
另一个多线程经典面试问题，并不直接跟线程有关，但间接帮助很多。这个java面试问题可以变的非常棘手，如果他要求你写一个不可变对象，或者问你为什么String是不可变的。
答：
1如果一个对象，在它创建完成之后，不能再改变它的状态，那么这个对象就是不可变的。不能改变状态的意思是，不能改变对象内的成员变量，包括基本数据类型的值不能改变，引用类型的变量不能指向其他的对象，引用类型指向的对象的状态也不能改变.所有的状态都是不可改变的，所有域都是final类型．
 如何在Java中写出Immutable的类？
要写出这样的类，需要遵循以下几个原则：
1）immutable对象的状态在创建之后就不能发生改变，任何对它的改变都应该产生一个新的对象。
2）Immutable类的所有的属性都应该是final的。
3）对象必须被正确的创建，比如：对象引用在对象创建过程中不能泄露(leak)。
4）对象应该是final的，以此来限制子类继承父类，以避免子类改变了父类的immutable特性。
5）如果类中包含mutable类对象，那么返回给客户端的时候，返回该对象的一个拷贝，而不是该对象本身（该条可以归为第一条中的一个特例） 

2 使用Immutable类的好处：
1）Immutable对象是线程安全的，可以不用被synchronize就在并发环境中共享
2）Immutable对象简化了程序开发，因为它无需使用额外的锁机制就可以在线程间共享
3）Immutable对象提高了程序的性能，因为它减少了synchroinzed的使用
4）Immutable对象是可以被重复使用的，你可以将它们缓存起来重复使用，就像字符串字面量和整型数字一样。你可以使用静态工厂方法来提供类似于valueOf（）这样的方法，它可以从缓存中返回一个已经存在的Immutable对象，而不是重新创建一个。 

immutable也有一个缺点就是会制造大量垃圾，由于他们不能被重用而且对于它们的使用就是”用“然后”扔“，字符串就是一个典型的例子，它会创造很多的垃圾，给垃圾收集带来很大的麻烦。当然这只是个极端的例子，合理的使用immutable对象会创造很大的价值。 
http://my.oschina.net/jasonultimate/blog/166810


为什么String是不可变类:
String中的成员变量value[]数组被设置为私有的final域,该数组是存储String的内容．并且该类没有提供一个修改该数组的方法．所以具有
string 不是完全不可改变的，可以通过反射机制进行改变．可以反射出String对象中的value属性， 进而改变通过获得的value引用改变数组的结构

String 设置不可变的好处：
1) 常量池的需要，提高资源的利用率．字符串常量池(String pool, String intern pool, String保留池) 是Java堆内存中一个特殊的存储区域, 当创建一个String对象时,假如此字符串值已经存在于常量池中,则不会创建一个新的对象,而是引用已经存在的对象。
2) 安全性,String被许多的Java类(库)用来当做参数,例如 网络连接地址URL,文件路径path,还有反射机制所需要的String参数等, 假若String不是固定不变的,将会引起各种安全隐患。
3)允许String对象缓存HashCode
Java中String对象的哈希码被频繁地使用, 比如在hashMap 等容器中。字符串不变性保证了hash码的唯一性,因此可以放心地进行缓存.这也是一种性能优化手段,意味着不必每次都去计算新的哈希码. 
4) 类上设置final是为了防止继承，防止继承的好处在于保证安全性，保证String的不可变特性．举个例子，如果某个方法的参数String, 如果String类没有设置为final，那么可以生成一个String2继承String，覆盖String中的所有方法并且设置为一个可变的类．那么将String2对象传入到该方法中必然导致一些安全性问题，比如可变性．

## 你在多线程环境中遇到的共同的问题是什么？你是怎么解决它的？
多线程和并发程序中常遇到的有Memory-interface、竞争条件、死锁、活锁和饥饿。问题是没有止境的，如果你弄错了，将很难发现和调试。这是大多数基于面试的，而不是基于实际应用的Java线程问题。

## 在Java中什么是线程调度
每个线程只有获得CPU的使用权才能执行指令.所谓多线程的并发运行,其实是指从宏观上看,各个线程轮流获得CPU的使用权,分别执行各自的任务.在运行池中,会有多个处于就绪状态的线程在等待CPU,JAVA虚拟机的一项任务就是负责线程的调度,线程调度是指按照特定机制为多个线程分配CPU的使用权.

## Java中用到的线程调度算法
有两种调度模型：分时调度模型和抢占式调度模型。
分时调度模型是指让所有的线程轮流获得cpu的使用权,并且平均分配每个线程占用的CPU的时间片这个也比较好理解。
java虚拟机采用抢占式调度模型，是指优先让可运行池中优先级高的线程占用CPU，如果可运行池中的线程优先级相同，那么就随机选择一个线程，使其占用CPU。处于运行状态的线程会一直运行，直至它不得不放弃CPU。

## 无限制创建线程的不足　及线程池
为每个线程分配一个线程，尤其存需要创建大量线程时存在以下不足：
1. 线程生命周期的开销非常高，线程的创建和销毁需要消耗性能和时间，所以会引入一部分的**延迟时间**．
2. 资源消耗，活跃的线程会消耗系统资源，尤其是内存，如果可运行的线程数量多于可用处理器数量，那么有些线程将闲置．大量闲置的内存.虚拟机创建和回收对象需要消耗一部分的系统性能．
3. 稳定性，可创建线程的数量有一个限制，这个限制根据不同平台的不同而不同，并且受多个因素制约，如JVM的启动参数等，如果超过这个限制会抛出OutOfMemory异常．
在一定范围内增加线程可以提高吞吐率，但超过这个范围，在创建线程只会降低程序的执行速度．

线程池指管理一组**同构工作线程**的资源池，线程池包括工作线程和工作队列密切相关的．在工作队列中保存了所有等待执行的任务，工作线程负责从工作队列中获取一个任务，执行任务，然后返回线程池并等待下一个任务。“在线程池中执行任务”比“为每个任务分配一个线程”有更大优势，通过重用现有线程，而不是创建新线程，可以在处理多个请求时**分摊在线程创建和销毁过程中产生的巨大的开销**，另外，当请求到达时，工作线程已经存在，因此不会由于等待创建线程而延迟任务的执行，从而提高响应性，通过调整线程池的大小，可以创建足够多的线程以便使处理器保持忙碌的状态，同时还可以防止过多的线程相互竞争资源而应用程序耗尽内存。

## 为什么使用Executor框架比使用应用创建和管理线程好？
它为灵活且强大的**异步执行框架**提供基础，该框架能支持多种类型的任务执行策略，**它提供了一种标准的方法将任务的提交过程和执行过程解耦**，并用Runnable表示任务．**Executor还提供了生命周期的支持，以及统计信息收集，应用程序管理机制和性能监视等机制**．它是基于生产消费者模式，提交任务的操作相当于生产者，执行任务的线程则相当于消费者．**内置线程池，防止线程无限创建导致的一系列问题**．
Executor的生命周期：**创建，提交，开始和完成**．在Executor中，已经提交但尚未开始的任务可以取消，当对于那些已经开始的任务，只有当它们响应中断时才能取消．
Execotor的生命周期：运行，关闭和终止．

内部内置了线程池，避免了无限制创建线程带来的不足，如创建和销毁开销大，资源消耗和不稳定性等．而使用Executor框架，开发者只要提交任务就可以，无需关注框架是如何分配线程来完成任务的．该框架内置了线程池来管理工作线程，可以提高任务的提交和执行效率．

## 在Java中Executor和Executors的区别？
Executor是一个接口，定义了任务提交与执行过程相解耦的框架，Executors是工具类用来产生线程池的．

## java都有哪些加锁方式
方法锁，同步块，ReentrantLock

## 多线程最佳实践


## 其他问题
用Java编程一个会导致死锁的程序，你将怎么解决？
SOF你遇到过哪些情况。
什么是竞争条件？你怎样发现和解决竞争？
1) 在java中绿色线程和本地线程区别？
 如果同步块内的线程抛出异常会发生什么？
7. atomicinteger和Volatile等线程安全操作的关键字的理解和使用
22. 线程同步的方法：sychronized、lock、reentrantLock等。
27. Concurrent包里的其他东西：ArrayBlockingQueue、CountDownLatch等等。
连接池使用使用什么数据结构实现
实现连接池
结束一条 Thread 有什么方法？ interrupt 底层实现有看过吗？线程的状态是怎么样的？如果给你实现会怎么样做？
AIO与BIO的区别
2. 常用的线程池模式以及不同线程池的使用场景
3. newFixedThreadPool此种线程池如果线程数达到最大值后会怎么办，底层原理。
4. 多线程之间通信的同步问题，synchronized锁的是对象，衍伸出和synchronized相关很多的具体问题，例如同一个类不同方法都有synchronized锁，一个对象是否可以同时访问。或者一个类的static构造方法加上synchronized之后的锁的影响。
8. 线程间通信，wait和notify
9. 定时线程的使用
10. 场景：在一个主线程中，要求有大量(很多很多)子线程执行完之后，主线程才执行完成。多种方式，考虑效率。
14. 并发、同步的接口或方法
16. J.U.C下的常见类的使用。 ThreadPool的深入考察； BlockingQueue的使用。（take，poll的区别，put，offer的区别）；原子类的实现。
17. 简单介绍下多线程的情况，从建立一个线程开始。然后怎么控制同步过程，多线程常用的方法和结构
19. 实现多线程有几种方式，多线程同步怎么做，说说几个线程里常用的方法
7) 在线程中你怎么处理不可捕捉异常？
8) 什么是线程组，为什么在Java中不推荐使用？
11) 如何在Windows和Linux上查找哪个线程使用的CPU时间最长？


