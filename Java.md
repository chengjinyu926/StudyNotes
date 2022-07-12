# Java

## 基础

### 深拷贝和浅拷贝

* 浅拷贝：拷贝一个对象时，不拷贝类型为引用类型的属性。
* 深拷贝：拷贝一个对象时，其类型为引用类型的属性也被拷贝。

### 四种引用类型

#### 强引用

​	Java中最常见的引用。把一个对象赋给一个引用变量，这个引用变量就是一个强引用。当一个对象被强引用变量引用时，它处于可达状态，它是不可能被垃圾回收机制回收的。即使该对象以后永远都不会用到，JVM也不会回收。因此强引用是造成Java内存泄露的主要原因之一。

#### 软引用

​	需要用SoftReference类来实现，对于只有软引用的对象来说，当系统内存足够时，它不会被回收，当系统内存空间不足时它会被回收。软引用通常用在对内存敏感的程序中。

#### 弱引用

​	需要用WeakReference类实现，它比软引用的生存周期更短，对于只有弱引用的对象来说，只要GC一旦运行，无论JVM内存空间是否足够，总会对弱引用对象进行回收。

#### 虚引用

​	需要用PhantomReference类实现，它不可以单独使用，必须和引用队列联合使用。虚引用的主要作用是跟踪对象被垃圾回收的状态。

## JVM

​	JVM是可运行Java代码的假想计算机。JVM是运行在操作系统之上的，它与硬件没有直接的交互。

### 构成

#### 类装载系统

​	Class loader ，根据给定的全限定名类型来装载 class 文件到 Runtime data area 中的 method area。

#### 执行引擎

​	Execution engine，执行 class 中的指令。

##### 即时编译器

​	JIT Complier，

##### 垃圾收集器

​	Garbage Collector，JVM针对老年代和新生代提供了多种不同的垃圾收集器

###### Serial

​	工作在新生代的使用复制算法的最基本的垃圾收集器。

​	单线程的垃圾回收器，只使用一个CPU或一条线程工作。工作时必须暂停其他的工作线程直至GC结束。

​	虽然在GC过程中必须暂停其他工作线程，但是它简单高效，对于限定单个CPU工作环境时，没有线程交互的开销，可以获得最高的单线程垃圾回收效率。因此，Serial垃圾收集器仍然是JVM运行在Client模式下默认的新生代垃圾收集器。

###### ParNew

​	多线程版本的Serial收集器。工作时仍然要暂停其他工作线程。

​	使用复制算法。工作在新生代。

​	默认开启和CPU数目相同的线程数。可以通过-XX:ParallelGCThreads参数来限定线程数。

​	很多JVM运行在Server模式下的新生代的默认垃圾回收器。

###### Parallel Scavenge

​	使用复制算法的多线程的工作在新生代的垃圾回收器。

​	重点关注的是程序达到一个可控制的吞吐量。

###### Serial Old

​	使用标记整理算法的老年代单线程垃圾回收器。

​	JVM运行在Client模式下的默认老年代垃圾回收器。

​	Server模式下的两个主要用途。

1. JDK 1.5 之前版本与 Parallel Scavenger 配合使用。
2. 老年代中使用CMS收集器的后备方案。

###### Parallel Old

​	多线程的标记整理算法的老年代垃圾回收器。

​	关注点是吞吐量优先。

###### CMS

​	Concurrent mark sweep，使用标记清除算法的多线程老年代垃圾回收器。

​	主要目标是获取最短垃圾回收停顿时间。最短的垃圾回收停顿时间可以为交互比较频繁的程序提高用户体验，

* 工作机制
  1. 初始标记：标记GC Roots能直接关联的对象，速度很快，但仍需暂停其他的工作线程。
  2. 并发标记：进行GC Roots跟踪，和用户线程一起工作，无需暂停其他工作线程。
  3. 并发清除：清除GC Roots不可达对象，和用户线程一起工作，无需暂停其他工作线程。

###### G1

​	Garbage First

​	相比CMS：

1. 基于标记整理算法，不产生内存碎片。
2. 可以非常精确控制停顿时间，在不牺牲吞吐量的前提下，实现低停顿垃圾回收。

​	避免全区域垃圾回收，把堆内存划分为大小固定的几个独立区域，并且跟踪这些区域的垃圾收集进度，同时在后台维护一个优先级列表，每次根据所允许的收集时间，优先回收垃圾最多的区域。

​	区域划分和优先级制度确保G1收集器可以在有限时间内获得最高的垃圾回收效率。

#### 本地接口

​	Native Interface，与 native libraries 交互，是其他编程语言交互的接口。

#### 运行时数据区域

​	Runtime data area ，JVM内存。

##### 方法区

​	永久代，用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码数据等。被所有线程所共享。

##### 堆

​	类实例区，Java虚拟机中内存最大的一块，被所有线程所共享。创建的对象和数组都保存在堆内存中，也是垃圾收集器进行垃圾收集最重要的内存区域。

​	由于现代VM采用分代收集算法，因此Java堆从GC的角度还可以细分为新生代和老年代。

* 新生代

  一般占用1/3的堆空间

  * Eden

    一般占用8/10的新生代空间。

    Java新对象的出生地，如果新创建的对象占用内存很大，则直接分配到老年代。当Eden区内存不够的时候就会触发MinorGC。

  * From Survivor

    一般占用1/10的新生代空间。

    上一次GC的幸存者，作为这一次GC的被扫描者。

  * To Survivor

    一般占用1/10的新生代空间。

    保留了一次MinorGC过程中的幸存者。

* 老年代

  一般占用2/3的堆空间

##### 虚拟机栈

​	线程私有。虚拟机栈和线程的生命周期相同。

​	一个线程中，每调用一个方法创建一个栈帧。

* 结构：

  * 本地变量表

  * 操作数栈

  * 对运行时常量池的引用

* 异常

  * StackOverflowError：线程请求的栈深度大于JVM所允许的深度。
  * OutOfMemoryError：JVM允许动态扩展的情况下无法申请到足够的内存。

##### 本地方法栈

​	线程私有。

* 异常

  * StackOverflowError：线程请求的栈深度大于JVM所允许的深度。

  * OutOfMemoryError：JVM允许动态扩展的情况下无法申请到足够的内存。

##### 程序计数器

​	指向虚拟机字节码指令的位置，当前线程所执行的字节码的行号指示器。字节码解析器的工作是通过改变这个计数器的值，来选取下一条需要执行的字节码指令。分支、循环、跳转、异常处理、线程恢复等基础功能，都需要依赖程序计数器。

### 运行机制

​	首先通过编译器（java c）把Java文件编译成字节码文件，类加载器再把字节码文件加载到内存中，将其放在运行时数据区的方法区内，而字节码文件只是JVM的一套指令集规范，并不能直接交给底层的操作系统去执行，因此需要执行引擎，将字节码翻译成底层系统指令，再交由CPU去执行，而这个过程需要调用其它语言的本地库接口来实现整个程序的功能。

#### 类的加载

​	将类的.class文件中的二进制数据读入到内存中，将其放在运行时数据区的方法区内，然后在堆中创建一个`java.lang.Class`对象，用来封装类在方法区内的数据结构。

### 垃圾回收与算法

* 对方法区的永生代的回收主要包括废弃常量和无用的类。
* 对象内存的分配主要在新生代，少数情况会直接分配到老年代。

#### 确认垃圾

##### 引用计数法

​	一个对象如果没与任何与之关联的引用，即他们的引用计数都不为0，则说明对象不太可能再被用到，那么这个对象就是可回收的对象。

##### 可达性分析

​	可以解决引用计数法的循环引用问题。通过一系列的 " GC roots " 对象作为起点搜索。

​	如果在 GC roots 和一个对象之间没有可达路径，则称该对象是不可达的。

​	不可达对象不等价于可回收对象，不可达对象变为可回收对象至少要经过两次标记过程。

#### 回收算法

##### 标记清除算法

​	最基础的垃圾回收算法，先标记出所有需要回收的对象，清除阶段回收被标记对象所占用的内存空间。

* 弊端
  * 内存碎片化严重，后续可能发生大对象找不到可利用的空间。

##### 复制算法

​	按内存容量将内存划为等大小的两块，每次只使用其中一块。当这一块内存满后将尚存活的对象复制到另一块上去，将已使用的内存清除掉。

* 弊端
  * 可用内存被压缩至原来的一半。
  * 存活对象数量多时，算法效率低。

##### 标记整理算法

​	标记要回收的对象，标记后将存活的对象移至内存的一端。然后清除边界外的对象。

##### 分代收集算法

​	根据对象存活的不同生命周期将内存划分为不同的域。一般情况下将GC堆划分为老年代和新生代。

​	老年代的特点是每次回收只有少量对象需要被回收。

​	新生代的特点是每次回收都有大量对象需要被回收。

###### 新生代的回收算法

​	目前大部分JVM的GC对于新生代都采取复制算法。但通常并不是按照1：1来划分新生代。一般将新生代划分为一块较大的Eden区和两块较小的Survivor区（To & From）,每次使用Eden区和其中一块Survivor（From）区，进行回收时，将两块空间中还存活的对象复制到另一块Survivor（To）区中。

* To Survivor 中无法存储某个对象时，这个对象会被存储到老年代。
* 当对象在Survior区躲过一次GC后，其年龄就会+1。默认情况下，年龄达到15时，该对象会被移到老年代中。

###### 老年代的回收算法

​	老年代每次只回收少量对象，因而采用标记整理算法。

##### 分区收集算法

​	将整个堆空间划分为连续的不用小区间，每个小区间独立使用，独立回收。

* 控制一次回收多少个小区间，根据目标停顿时间，每次合理地回收若干个小区间，从而减少一次GC所产生的停顿。

## 集合

> [Java集合详解 (biancheng.net)](http://c.biancheng.net/view/6824.html)

​	Java提供了集合类，集合类主要负责保存其他数据。

​	和数组不同，集合中的元素只能是对象，不能是基本类型的值。

### Collection

​	Collection接口是List、Set、Queue的父接口。

#### List

​	有序、可重复的集合，集合中每个元素都有对应的顺序索引。可以根据索引来访问对应位置的元素。

##### ArrayList

​	底层使用数组。因此其具有增删慢，查找快的特点。其线程不安全。

* 扩容机制：容量不足时，容量 = 容量 * 1.5 + 1

##### Vector

​	牺牲了效率换取了线程安全。其底层基于数组实现，所以具有增删慢，查找快的特点。

* 扩容机制：容量不足时，默认扩展一倍容量。

##### LinkedList

​	基于双向循环链表实现，所以具有增删快，查找慢的特点。线程不安全。

#### Set

​	无序、不可重复的集合。最多只允许包含一个null元素。

##### HashSet

​	基于哈希表实现，其内部是HashMap。

##### TreeSet

​	基于二叉树实现。

##### LinkedHashSet

​	使用哈希表存储，采用双向链表记录插入顺序。内部是LinkedHashMap。

#### Queue

​	Java提供的队列实现。

### Map

​	存储具有映射关系的数据的集合。

##### HashMap

​	底层是哈希表。键值对均可为null。线程不安全。

​	由数组、链表、红黑树组成。数组中的元素是单项链表或红黑树，当链表中存储的元素超过8个时，链表会转化成红黑树。

##### HashTable

​	底层是哈希表。

​	键值对均不能为null。

​	线程安全。

##### TreeMap

​	底层是二叉树。

## NIO

​	NIO有三大核心部分：通道、缓冲区、选择器。

​	传统IO基于字节流和字符流进行操作，NIO基于通道和缓冲区进行工作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。选择器用于监听多个通道的事件。因此，单个线程可以监听多个数据通道。

​	NIO和IO之间很显著的区别是IO是面向流的，NIO是面向缓冲区的。

### IO模型

#### 阻塞IO模型

​	读写数据过程中会发生阻塞现象。

​	当用户线程发出IO请求后，内核会去查看数据是否就绪，如果没有就绪就会等待数据就绪，而用户线程就会处于阻塞状态。当数据就绪后，内核会将数据拷贝给用户线程，用户线程才会解除阻塞状态。

#### 非阻塞IO模型

​	当用户线程发起读操作时，并不需要直接等待就会得到一个结果。如果结果是Error时，他就知道数据还未准备好，它可以再次发送读操作。一旦内核中的数据准备好，并且再次收到用户线程的请求时，它马上将数据拷贝给用户线程。

​	非阻塞IO模型中，用户线程需要不断询问内核数据是否就绪。即非阻塞IO不会交出CPU。

#### 多路复用IO模型

​	有一个线程会不断轮询多个socket的状态，只有当socket真正有读写事件时，才真正调用实际的IO读写操作。

​	只需要使用一个线程就可以管理多个socket，系统不需要建立和维护新的进程或线程，并且只有在socket读写事件进行中，才会调用IO资源，所以他大大减少了资源占用。

​	多路复用的IO中，询问每个socket的状态是在内核中进行的。这个效率要比用户线程要高得多。

​	多路复用IO模型是通过轮询的方式来检测是否有事件到达，并且对到达的事件进行逐一的响应。一旦事件响应体很大，就会导致后续的事件迟迟得不到处理，并且会影响新的事件轮询。

#### 信号驱动IO模型

​	当用户线程发起IO请求时，会给对应的socket注册一个信号函数，然后用户线程会继续执行，当内核数据就绪时会发送一个信号给用户线程，用户线程接收到信号之后，便在信号函数中调用IO读写操作完成实际的IO请求操作。

#### 异步IO模型 

​	最理想的IO模型。

​	用户线程发起读操作时，就可以去做其他事情了。内核收到异步读的信号后，它会立刻返回，说明读请求成功发起，因此不会对用户产生任何block，之后内核会准备数据并将数据拷贝至用户线程，当这一切都结束后，内核会给用户线程发送信号告知其读操作已经完成。

​	用户线程只需要发送一个读请求，当接收内核返回的成功信号时就得知IO操作已经完成，可以去使用数据了。

### 通道

​	和普通IO的流相比，通道可以双向传输数据，而流只能是单向的。

### 缓冲区

### 选择器

## 多线程和JUC

### 线程和进程

* 进程
  * 操作系统分配资源的基本单位。
  * 一个进程至少包含一个线程，线程不能独立于进程而存在。
* 线程
  * 操作系统能够进行运算调度的基本单位。
  * 包含在进程中，是进程中的实际运行单位。
  * 一个线程可以创建和撤销另一个线程，同一进程中的多个线程可以并发执行。
  * 线程不拥有系统资源，只拥有一点在运行中必不可少的资源。

#### 并发和并行

* 并发
  * 多个线程利用一个CPU工作，他们争夺同一个资源。
* 并行
  * 多个线程之间工作利用多个CPU工作，其互不影响。

#### 线程的状态

* 新建
  * 当程序使用new关键字创建了一个线程后，该线程就处于新建状态，此时仅有JVM为其分配内存，并初始化其成员变量的值。
* 就绪
  * 当线程调用了start()方法后，该线程处于就绪状态。JVM会为其分配虚拟机栈和程序计数器。

* 运行
  * 处于就绪状态的线程获得了CPU，开始执行run()方法。
* 阻塞
  * 线程因为某种原因放弃了线程使用权，暂时停止运行。
  * 阻塞状态
    * 等待阻塞：运行中的线程调用了wait()方法，JVM把该线程放入等待队列。
    * 同步阻塞：运行中的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则JVM会把该线程放入锁池中。
    * 其他阻塞：运行中的线程执行了sleep()方法或join()方法时，或发生了IO请求时，JVM会把该线程置为阻塞状态。当sleep()超时、join()等待线程中止或超时、IO处理完毕时，线程重新转入可运行状态。
* 死亡
  * 正常结束：run()或call()方法执行完成。
  * 异常结束：线程抛出一个未捕获的Exception或Error。
  * 调用stop：直接调用stop()方法结束线程，该方法通常容易导致死锁。

#### 创建线程的方式

1. 继承Thread类

   ```java
   public class StudyThread {
       public static void main(String[] args) {
           WayExtendThread wayExtendThread = new WayExtendThread();
           wayExtendThread.start();
       }
   }
   
   class WayExtendThread extends Thread {
       @Override
       public void run() {
           System.out.println("Excute a new thread");
       }
   }
   ```

2. 实现Runnable接口

   ```java
   public class StudyThread {
       public static void main(String[] args) {
           Thread thread = new Thread(new WayImplementRunnbale());
           thread.start();
       }
   }	
   
   class WayImplementRunnbale implements Runnable {
       @Override
       public void run() {
           System.out.println("Excute a new thread");
       }
   }
   ```

3. 实现Callable接口

   ```java
   public class StudyThread {
       public static void main(String[] args) {
           WayImplementCallable thread = new WayImplementCallable();
           FutureTask<Boolean> result = new FutureTask<Boolean>(thread);
           new Thread(result).start();
           try {
               if (result.get()){
                   System.out.println("执行成功!");
               }
           } catch (InterruptedException e) {
               e.printStackTrace();
           } catch (ExecutionException e) {
               e.printStackTrace();
           }
       }
   }
   class WayImplementCallable implements Callable<Boolean> {
       @Override
       public Boolean call() throws Exception {
           System.out.println("excute a new thread");
           return true;
       }
   }
   ```

4. 基于线程池

#### 终止线程的方式

#### wait 和 sleep

|          |    wait    |  sleep   |
| :------: | :--------: | :------: |
| 锁的释放 |    释放    |  不释放  |
| 使用环境 | 同步代码块 | 任何地方 |
|  所属类  |   Object   |  Threa   |

### ThreadLocal

​	ThreadLocal叫做***线程变量***，意思是ThreadLocal中填充的变量属于当前线程，该变量对其他线程而言是隔离的，也就是说该变量是当前线程独有的变量。ThreadLocal为变量在每个线程中都创建了一个副本，那么每个线程可以访问自己内部的副本变量。

​	使用场景：

* 每个线程需要有自己单独的实例
* 实例需要在多个方法中共享，但不希望被多线程共享

### 线程池

#### ThreadPoolExecutor

```java
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.acc = System.getSecurityManager() == null ?
                null :
                AccessController.getContext();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

* corePoolSize：核心线程池大小
* maximumPoolSize：最大线程池大小
* keepAliveTime：线程最大空闲时间
* unit：时间单位
* workQueue：线程等待队列
* threadFactory：线程创建工厂
* handler：拒绝策略

#### 等待队列

##### LinkedBlockingQueue

##### ArrayBlockingQueue

##### DelayQueue

##### PriorityQueue

##### SynchronizedQueue

##### TransferQueue

#### 拒绝策略

##### AbortPolicy

​	默认的拒绝策略，任务不能被提交时，抛出异常。

##### DiscardPolicy

​	线程队列满时，后续提交的任务会被丢弃且不抛出异常。

##### DiscardOldestPolicy

​	抛弃队列最前方的任务，重新提交被拒绝的任务。

##### CallerRunsPolicy

​	任务被拒绝了，则由提交任务的线程执行任务。

#### 默认提供的线程池

##### newFixedThreadPool

```java
  	 public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
```

* 核心线程数等于最大线程数，即线程全部是核心线程
* 线程最大空闲时间为0
* 线程等待队列是无界阻塞队列，队列最大值为Integer.MAX_VALUE。如果任务提交速度大于任务处理速度。有可能造成内存溢出。
* 无序执行任务。

##### newCachedThreadPool

```java
     public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
```

* 线程数量无限制。
* 线程空闲60s后结束。
* 线程等待队列为同步队列。
* 适合处理量大耗时短的任务。

##### newSingleThreadExecutor

```java
    public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }
```

* 外层包裹FinalizableDelegatedExecutorService，目的是保证线程池无法成功向下转型，从而保证真正Single。
* 只有一个核心线程。

##### newScheduledThreadPool

```java
    public ScheduledThreadPoolExecutor(int corePoolSize) {
        super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
              new DelayedWorkQueue());
    }
```

* 创建一个可以安排在给定延迟后运行或定期执行任务的线程池。

### 锁

#### 公平锁

​	不允许插队

#### 可重入锁

​	又称递归锁

​	某个线程已经获取到了锁，可以再次获取锁而不会出现死锁。

#### synchronized关键字

## 序列化

​	序列化是一种用来处理对象流的机制，所谓对象流就是将对象的内容进行流化，将数据分解为字节流，以便存储在文件中或在网络上进行传输。

#### 为何要进行序列化

1. 网络传输。

   网络传输的数据都必须是二进制数据，但在Java中都是对象，是没有办法在网络中传输的，所以就需要Java对象进行序列化，并要求这个序列化是可逆的。

2. 对象持久化，即将内存中的对象状态保存到文件或数据库中 

3. 实现分布式对象，如RMI（远程方法调用）要利用对象序列化进行远程主机上的服务，就像在本地机上运行对象时一样。

### 如何实现序列化

1. Java原生序列化
   1. 需要被序列化的类实现Serializable接口
   2. 具体实现
      1. 序列化：`ObjectOutputStream#writeObject(Object obj)`
      2. 反序列化：`ObjectInputStream#readObject()`
   3. 缺点：效率较低，序列化后的流数据比较大。
2. 第三方序列化，比如JSON。
3. 注意
   1. transient修饰的属性不会被序列化。
   2. static修饰的属性不会被序列化。
