### Java多线程

#### 线程和进程

##### 进程

> 进程是程序的一次执行过程，是程序运行的基本单位。系统运行一个程序即是一个进程从创建，运行到消亡的过程。

在Java中，当我们启动main函数时其实就是启动了一个JVM的进程，而main函数所在的进程就是这个进程中的一个线程，也称主线程。

##### 线程

> 线程与进程相似，但线程是一个比进程更小的执行单位。一个进程在其执行的过程中可以产生多个线程。与进程不同的是**同类的多个线程共享进程的==堆==和==方法区==资源，但每个线程有自己的程序计数器、虚拟机栈和本地方法栈**，所以系统在产生一个线程，或是在各个线程之间作切换工作时，负担要比进程小得多，也正因为如此，线程也被称为轻量级进程。

main线程执行main方法。

**一个Java程序的运行是main线程和多个其他线程同时运行。**

#### 线程与进程的关系、区别、优缺点

![image-20210410103353799](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410103353799.png)

**一个进程中可以有多个线程，多个线程共享进程的==堆==和==方法区==资源，但是每个线程有自己的==程序计数器、虚拟机栈和本地方法栈==。**

> 线程是进程划分成的更小的运行单位。线程和进程最大的不同在于**基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。线程执行开销小，但不利于资源的管理和保护；而进程正相反**。

##### 程序计数器为什么是私有的

主要有以下两个作用：

1. 字节码解释器通过改变程序计数器来依次读取指令，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。
2. 在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。

需要注意的是，如果执行的是native方法，那么程序计数器记录的是undefined地址，只有执行的是Java代码时程序计数器记录的才是下一条指令的地址。

所以，程序计数器私有主要是为了**线程切换后能恢复到正确的执行位置。**

##### 虚拟机栈和本地方法栈为什么是私有的

- 虚拟机栈：每个Java方法在执行的同时会创建一个栈帧用于存储局部变量表、操作数栈、常量池引用等信息。从方法调用直至执行完成的过程，就对应着一个栈帧在Java虚拟机栈中入栈和出栈的过程。
- 本地方法栈：和虚拟机栈所发挥的作用非常相似，区别是：**虚拟机栈为虚拟机执行Java方法(也就是字节码)服务，而本地方法栈则为虚拟机使用到的Native方法服务。**在`HotSpot` 虚拟机中和Java虚拟机栈合二为一。

所以，为了**==保证线程中的局部变量不被别的线程访问到==，虚拟机栈和本地方法栈是线程私有的。**

##### 堆和方法区为什么是公有的

​		堆和方法区是所有线程共享的资源，其中堆是进程中最大的一块内存，主要用于存放新创建的对象(所有对象都在这里分配内存)，方法区主要用于存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

#### 说说并发与并行的区别？

并发：同一时间段，多个任务都在执行(单位时间内不一定同时执行)；

并行：单位时间内，多个任务同时执行。

#### 多线程的安全问题

##### 并发编程的三要素

```markdown
**
1. 原子性
	一个或者多个操作，要么全部执行，要么就全部都不执行。
2. 可见性
	多个线程操作一个共享变量时，其中一个线程对变量进行修改后，其他线程可以立即看到修改的结果。 
3. 有序性
	程序的执行顺序按照代码的先后顺序来执行。
```

##### 线程安全的两个方面

```markdown
**
1. 执行控制
>    执行控制的目的是控制代码执行(顺序)及是否可以并发执行。
2. 内存可见。
>    内存可见控制的是线程执行结果在内存中对其它线程的可见性。根据Java内存模型的实现，线程在具体执行时，会先拷贝主存数据到线程本地(CPU缓存)，操作完成后再把结果从线程本地刷到主存。
**
```

**************

#### Java多线程下的long和double

在看一些代码的时候，会发现在定义long型和double型的变量时，会在前面加上volatile关键字，当然也会看到在其它原子类型的变量前加上这个关键字，但这里要说的还是有区别的。

在java中，**java的内存模型要求，变量的读取操作和写入操作都必须是原子操作的**，但是对于非volatile类型的long和double有些不同，因为这两个变量是64位存储，**JVM允许将64位的读操作或写操作分解为2个32位的操作**。这样，当在多线程环境中读取一个非volatile的long变量时，可能会出现读取到这个变量一个值的高32位和另一个值的低32位，从而导致数据出问题。

****************

#### ==Java创建线程的方式==

1. 通过继承Thread类创建线程类；

   > **1、定义一个类继承Thread类，并重写Thread类的run()方法，run()方法的方法体就是线程要完成的任务，因此把run()称为线程的执行体；**
   >
   > **2、创建该类的实例对象，即创建了线程对象；**
   >
   > **3、调用线程对象的start()方法来启动线程；**
   >
   > ![image-20210426201829497](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210426201829497.png)

2. 通过实现Runnable接口创建线程类；

   > **1、定义一个类实现Runnable接口；**
   >
   > **2、创建该类的实例对象obj；**
   >
   > **3、将obj作为构造器参数传入Thread类实例对象，这个对象才是真正的线程对象；**
   >
   > **4、调用线程对象的start()方法启动该线程；**
   >
   > ![image-20210426202357930](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210426202357930.png)

3. 通过Callable和Future接口创建线程；

   > **1、创建Callable接口实现类，并实现call()方法，该方法将作为线程执行体，且该方法有返回值，再创建Callable实现类的实例；**
   >
   > **2、使用FutureTask类来包装Callable对象，该Future对象封装了该Callable对象的call()方法的返回值；**
   >
   > **3、使用FutureTask对象作为Thread对象的target创建并启动新线程；**
   >
   > **4、调用FutureTaks对象的get()方法来获得子线程执行结束后的返回值。**
   >
   > ![image-20210506211052991](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210506211052991.png)

##### 对比

> **通过继承Thread类实现多线程：**
>
> 优点：
>
> 1、实现起来简单，而且要获取当前线程，无需调用`Thread.currentThread()`方法，直接使用this即可获取当前线程；
>
> 缺点：
>
> 1、线程类已经继承Thread类了，就不能再继承其他类；
>
> 2、多个线程不能共享同一份资源(如前面分析的成员变量 i )；
>
> **通过实现Runnable接口或者Callable接口实现多线程**：
>
> 优点：
>
> 1、线程类只是实现了接口，还可以继承其他类；
>
> 2、多个线程可以使用同一个target对象，适合多个线程处理同一份资源的情况。(**避免单继承的局限性，方便同一对象被多个线程使用**)
>
> 缺点：
>
> 1、通过这种方式实现多线程，相较于第一类方式，编程较复杂；
>
> 2、要访问当前线程，必须调用`Thread.currentThread()`方法。
>
> 综上：
>
> 一般采用第二类方式实现多线程。

#### 代理模式

代理模式分为动态代理和静态代理。两者的差别还是很大的，不过思想都是一样的，起到一个服务中介的作用。

在这里我们通过出租房子为例进行演示代理模式。我们知道我们出租房子一般是通过两种途径，第一，我们直接找到租客进行洽谈。第二，我们还可以通过那些黑心中介来帮助我们出租。但是不管是哪一种方式都是为了出租房子。下面我们就使用一张图来直观的表示一下：

![image-20210427163421109](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210427163421109.png)

代理模式其实特别的简单，房屋主人想要出租房屋，现在要通过第二种方式，使用中介来出租。中介就是代理。出租整个流程就是代理模式的应用。

看一下类图，再从类的角度分析一下代理模式

![image-20210427163716246](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210427163716246.png)

##### 静态代理模式

> 静态代理：所谓静态代理也就是在程序运行前就已经存在代理类的字节码文件(在真正出租房屋之前，就已经做好了相关工作，中介和房屋主人就做好了相关联系)，代理类和委托类的关系在运行前就确定了。

我们的代理其实啥都没有干，就算是出租房子也是直接new出来一个`RealHouseSubject`。让它去出租的房子。是不是有一种被中介欺骗了的感觉，明明中介没有出租房子，但是表面上还是他出租的房子。这就是静态代理，需要我们程序员自己去创建一个。

它存在了大量的弊端，比如说当我们的房屋主人很多，而且每一个房屋主人的要求也不一样，有的房屋主人要求为其出租房子的同时还要为其打扫卫生，还要为其砌墙等等(很明显要求不合理，但是中介还要干)，这时候再去用静态代理就会很麻烦。这时候就开始用到动态代理了。下面我们就开始看看动态代理是如何实现的这个例子。

**缺点**

(1)代理对象(中介)的一个接口只服务于一种类型的对象(房屋主人)，如果要代理的方法很多，势必要为每一种方法都进行代理，静态代理在程序规模稍大时就无法胜任了。

(2)如果接口增加一个方法，除了所有实现类需要实现这个方法外，所有代理类也需要实现此方法。增加了代码维护的复杂度。

##### 动态代理模式

![image-20210427164734277](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210427164734277.png)

态代理可以服务多个目标对象。当目标对象的需求不一定，而且很多时候也能够很好的去完成工作。也就是在静态代理中提前做好一些事，但是动态代理不是，动态代理在真正需要去做的时候再去做，而且是把相应的工作拿过来(比如说打扫，需要打扫的时候调用此方法就好)。

下面我们再从程序的角度来分析一下动态代理模式：

设计动态代理类(DynamicProxy)时，不需要显式实现与目标对象类(RealSubject)相同的接口，而是将这种实现推迟到程序运行时由JVM来实现，也就是说动态代理在需要的时候才去创建，而不是提前创建好。就好比是歌星有很多个助理，需要什么助理就找一个，不需要就一脚踢开。如果不能理解先来看一下实现再去分析吧。

##### 代理模式缺点

![image-20210427165010587](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210427165010587.png)



####  为什么使用多线程呢

先从总体上来说：

- **从计算机底层来说：**线程可以比作是轻量级的进程，是程序执行的最小单位，线程间的切换和调度的成本远远小于进程。另外，多核CPU时代意味着多个线程可以同时运行，这减少了线程上下文切换的开销。
- **从当代互联网发展趋势来说：**现在的系统动不动就要求百万级甚至千万级的并发量，而多线程并发编程正是开发高并发系统的基础，利用好多线程机制可以大大提高系统整体的并发能力以及性能。

再深入到计算机底层来探讨：

- **单核时代：**在单核时代多线程主要是为了提高CPU和IO设备的综合利用率。举个例子：当只有一个线程的时候会导致CPU计算时，I0设备空闲；进行I0操作时，CPU空闲。我们可以简单地说这两者的利用率目前都是50%左右。但是当有两个线程的时候就不一样了，当一个线程执行CPU计算时，另外一个线程可以进行I0操作，这样两个的利用率就可以在理想情况下达到100%了。
- **多核时代：**多核时代多线程主要是为了提高CPU利用率。举个例子：假如我们要计算一个复杂的任务，我们只用一个线程的话，CPU只会一个CPU核心被利用到，而创建多个线程就可以让多个CPU核心被利用到，这样就提高了CPU的利用率。

#### 使用多线程可能带来什么问题？

> 内存泄漏、上下文切换、死锁还有受限于硬件和软件的资源闲置问题。

#### ==线程的生命周期和状态==

![image-20210410105058137](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410105058137.png)

线程的生命周期中并不是固定于某一个状态，而是随着字节码的执行在不同状态之间切换。

![image-20210807202425454](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210807202425454.png)

​	由上图可以看出：线程创建之后它将处于**NEW(新建)**状态，调用`start()`方法后开始运行，线程这时候处于**READY(就绪)**状态。可运行状态的线程获得了CPU时间片( timeslice)后就处于**RUNNING(运行)**状态。

> 操作系统隐藏Java虚拟机(JVM)中的 RUNNABLE和RUNNING状态，它只能看到RUNNABLE 状态，所以Java系统一般将这两个状态统称为**RUNNABLE(运行中)**状态。

![image-20210410110311763](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410110311763.png)

当线程执行`wait()`方法之后，线程进入**WAITING(等待)**状态。进入等待状态的线程需要依靠其他线程的通知才能够返回到运行状态，而**TIME_WAITING(超时等待)**状态相当于在等待状态的基础上增加了超时限制，比如通过`sleep(long millis)`方法或`wait(long millis)`方法可以将Java 线程置于**TIMED WAITING**状态。当超时时间到达后Java线程将会返回到**RUNNABLE**状态。当线程调用同步方法时，在没有获取到锁的情况下，线程将会进入到**BLOCKED(阻塞)**状态。线程在执行 Runnable的`run()`方法之后将会进入到**TERMINATED(终止**)状态。

##### 线程进入BLOCKED(阻塞)状态

- 运行的线程执行 `wait()` 方法，该线程会释放占有的所有资源。JVM会把该线程放入“等待池”中。进入这个状态后，是不能自动唤醒的，必须依靠其他线程调用 `notify()` 或 `notifyAll()` 方法才能被唤醒。
- 当线程调用同步方法时，在没有获取到锁的情况下，线程将会进入到**BLOCKED(阻塞)**状态。
- 运行的线程执行 `sleep()` 或 `join()` 方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当`sleep()  `状态超时、`join()` 等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。

##### wait()和block()区别

**阻塞BLOCKED**

阻塞表示线程在等待对象的monitor锁，试图通过synchronized去获取某个锁，但是此时其他线程已经独占了monitor锁，那么当前线程就会进入等待状态。

**等待WAITING**

当前线程等待其他线程执行某些操作，典型场景就是生产者消费者模式，在任务条件不满足时，等待其他线程的操作从而使得条件满足。可以通过 `wait()` 方法或者 `Thread.join()` 方法都会使线程进入等待状态。

==实际上不用特意区分两者, 因为两者都会暂停线程的执行. 两者的区别是: 进入waiting状态是线程主动的, 而进入blocked状态是被动的. 更进一步的说, 进入blocked状态是在同步(synchronized代码之外), 而进入waiting状态是在同步代码之内.==

#### 一个线程两次调用start()方法会出现什么情况？

Java的线程是**不允许启动两次**的，第二次调用必然会抛出`ava.lang.IllegalThreadStateException`，这是一种运行时异常，多次调用`start()`被认为是编程错误。run() 方法没有限制。

在第二次调用start()方法的时候，线程可能处于终止或者其他(非NEW)状态，但是不论如何，都是不可以再次启动的。

#### ==说说sleep()方法和wait()方法的区别和共同点==

- 两者最主要的区别在于：**`sleep()`方法没有释放锁，而`wait()`方法释放了锁。**
- 两者都可以暂停线程的执行。
- `wait()`通常被用于线程间交互/通信，`sleep()`通常被用于暂停执行。
- `wait()`方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的`notify()`或者`notifyAll()`方法。`sleep()`方法执行完成后，线程会自动苏醒。或者可以使用`wait(long timeout)`超时后线程会自动苏醒。

#### 为什么我们调用start()方法时会执行run()方法，为什么我们不能直接调用run()方法？

`new `一个`Thread`，线程进入了新建状态；调用`start()`方法，会启动一个线程并使线程进入了就绪状态，当分配到时间片后就可以开始运行了。==`start()`会执行线程的相应准备工作，然后自动执行`run()`方法的内容，这是真正的多线程工作。但是，直接执行`run()`方法，会把`run()`方法当成一个main线程下的普通方法去执行，并不会在某个线程中执行它，所以这并不是多线程工作。==

总结：**调用`start()`方法方可启动线程并使线程进入就绪状态，直接执行`run()`方法的话不会以多线程的方式执行。**

#### 守护(daemon)线程

> **在Java中有两类线程：User Thread(用户线程)、Daemon Thread(守护线程) **
>
> 虚拟机必须确保用户线程执行完毕
>
> 虚拟机不用等待守护线程执行完毕    因为没有了被守护者，Daemon也就没有工作可做了，也就没有继续运行程序的必要了。
>
> 例子：后台记录操作日志、监控内存、垃圾回收等。
>
> daemon的作用是为其他线程的运行提供便利服务，守护线程最典型的应用就是 GC (垃圾回收器)，它就是一个很称职的守护者。

```java
// 设定 daemonThread 为 守护线程，default false(非守护线程)
 daemonThread.setDaemon(true);
// 验证当前线程是否为守护线程，返回 true 则为守护线程
 daemonThread.isDaemon();
```

#### Java实现线程同步的几种方式

##### 为什么要实现线程同步

java允许多线程并发控制，当多个线程同时操作一个可共享的资源变量时(如数据的增删改查)，将会导致数据不准确，相互之间产生冲突，因此加入同步锁以避免在该线程没有完成操作之前，被其他线程的调用，从而保证了该变量的唯一性和准确性

##### 实现线程同步的方式

- **使用同步方法**
  - `synchronized` 关键字修饰的方法。
  - ==synchronized关键字也可以修饰静态方法，此时如果调用该静态方法，将会锁住整个类==
- **使用同步代码块**
  - `synchronized` 关键字修饰的语句块。
- **使用重入锁实现线程同步(`ReentrantLock`)**
  -  `ReentrantLock` 类是可重入、互斥、实现了Lock接口的锁
  -  `ReentrantLock` 类的常用方法有：
    - `ReentrantLock()` : 创建一个 `ReentrantLock` 实例
    - `lock() `: 获得锁
    - `unlock() `: 释放锁
- **使用特殊域变量(volatile)实现同步(每次重新计算，安全但并非一致)**
  - 保持内存可见性和防止指令重排序。
- **使用原子变量实现线程同步( `AtomicInteger`(乐观锁))**
  - 需要使用线程同步的根本原因在于对普通变量的操作不是原子的。
  - 原子操作就是指将读取变量值、修改变量值、保存变量值看成一个整体来操作。这几种行为要么同时完成，要么都不完成，跟数据库的事务的原子性有些类似。
- **使用阻塞队列实现线程同步(`BlockingQueue`(常用)add(),offer(),put())**

#### Java线程通信

线程与线程之间不是相互独立的个体，它们彼此之间需要相互通信和协作，最典型的例子就是生产者-消费者问题：当队列满时，生产者需要等待队列有空间才能继续往里面放入商品，而在等待的期间内，生产者必须释放对临界资源(即队列)的占用权。因为生产者如果不释放对临界资源的占用权，那么消费者就无法消费队列中的商品，就不会让队列有空间，那么生产者就会一直无限等待下去。

##### 方式一：使用 volatile 关键字

基于 **volatile** 关键字来实现线程间相互通信是使用共享内存的思想，大致意思就是多个线程同时监听一个变量，当这个变量发生变化的时候 ，线程能够感知并执行相应的业务。这也是最简单的一种实现方式。

```java
public class TestSync {
    // 定义一个共享变量来实现通信，它需要是volatile修饰，否则线程不能及时感知
    static volatile boolean notice = false;

    public static void main(String[] args) {
        List<String>  list = new ArrayList<>();
        // 实现线程A
        Thread threadA = new Thread(() -> {
            for (int i = 1; i <= 10; i++) {
                list.add("abc");
                System.out.println("线程A向list中添加一个元素，此时list中的元素个数为：" + list.size());
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                if (list.size() == 5)
                    notice = true;
            }
        });
        // 实现线程B
        Thread threadB = new Thread(() -> {
            while (true) {
                if (notice) {
                    System.out.println("线程B收到通知，开始执行自己的业务...");
                    break;
                }
            }
        });
        //　需要先启动线程B
        threadB.start();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        // 再启动线程A
        threadA.start();
    }
}
```

![在这里插入图片描述](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20190112153112342.png)

##### 方式二：wait/notify 机制

- **wait()——让当前线程 ( `Thread.concurrentThread()` 方法所返回的线程) 释放对象锁并进入等待(阻塞)状态。**
- **notify()——唤醒一个正在等待相应对象锁的线程，使其进入就绪队列，以便在当前线程释放锁后竞争锁，进而得到CPU的执行。**
- **notifyAll()——唤醒所有正在等待相应对象锁的线程，使它们进入就绪队列，以便在当前线程释放锁后竞争锁，进而得到CPU的执行。**

> wait和 notify必须配合synchronized使用，wait方法释放锁，notify方法不释放锁

```java
public class TestSync {
    public static void main(String[] args) {
        // 定义一个锁对象
        Object lock = new Object();
        List<String>  list = new ArrayList<>();
        // 实现线程A
        Thread threadA = new Thread(() -> {
            synchronized (lock) {
                for (int i = 1; i <= 10; i++) {
                    list.add("abc");
                    System.out.println("线程A向list中添加一个元素，此时list中的元素个数为：" + list.size());
                    try {
                        Thread.sleep(500);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    if (list.size() == 5)
                        lock.notify();// 唤醒B线程
                }
            }
        });
        // 实现线程B
        Thread threadB = new Thread(() -> {
            while (true) {
                synchronized (lock) {
                    if (list.size() != 5) {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                    System.out.println("线程B收到通知，开始执行自己的业务...");
                }
            }
        });
        //　需要先启动线程B
        threadB.start();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        // 再启动线程A
        threadA.start();
    }
}
```

![在这里插入图片描述](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20190112154820961.png)

​		线程A发出 `notify()` 唤醒通知之后，依然是走完了自己线程的业务之后，线程B才开始执行，这也正好说明了，notify()方法不释放锁，而wait()方法释放锁。

##### 方式三：Condition

​    `Condition` 是在java 1.5中出现的，它用来替代传统的 `Object` 的 `wait()/notify()` 实现线程间的协作，它的使用依赖于 `Lock`，`Condition`、`Lock` 和 `Thread` 三者之间的关系如下图所示。相比使用 `Object` 的 `wait()/notify()`，使用`Condition` 的 `await()/signal()` 这种方式能够更加安全和高效地实现线程间协作。

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20170519154927256)

```java
/ 线程 A
class ThreadA extends Thread {
    private MyService service;
    public ThreadA(MyService service) {
        super();
        this.service = service;
    }
    @Override
    public void run() {
        service.awaitA();
    }
}
// 线程 B
class ThreadB extends Thread {
    public ThreadB(MyService service) {
        super();
        this.service = service;
    }
    @Override
    public void run() {
        service.awaitB();
    }
}
 
class MyService {
    private Lock lock = new ReentrantLock();
    // 使用多个Condition实现通知部分线程
    public Condition conditionA = lock.newCondition();
    public Condition conditionB = lock.newCondition();
 
    public void awaitA() {
        lock.lock();
        try {
            System.out.println("begin awaitA时间为" + System.currentTimeMillis()
                    + " ThreadName=" + Thread.currentThread().getName());
            conditionA.await();
            System.out.println("  end awaitA时间为" + System.currentTimeMillis()
                    + " ThreadName=" + Thread.currentThread().getName());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
 
    public void awaitB() {
        lock.lock();
        try {
            System.out.println("begin awaitB时间为" + System.currentTimeMillis()
                    + " ThreadName=" + Thread.currentThread().getName());
            conditionB.await();
            System.out.println("  end awaitB时间为" + System.currentTimeMillis()
                    + " ThreadName=" + Thread.currentThread().getName());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
 
    public void signalAll_A() {
        try {
            lock.lock();
            System.out.println("  signalAll_A时间为" + System.currentTimeMillis()
                    + " ThreadName=" + Thread.currentThread().getName());
            conditionA.signalAll();
        } finally {
            lock.unlock();
        }
    }
 
    public void signalAll_B() {
        try {
            lock.lock();
            System.out.println("  signalAll_B时间为" + System.currentTimeMillis()
                    + " ThreadName=" + Thread.currentThread().getName());
            conditionB.signalAll();
        } finally {
            lock.unlock();
        }
    }
}
 
// 测试
public class Run {
    public static void main(String[] args) throws InterruptedException {
        MyService service = new MyService();
 
        ThreadA a = new ThreadA(service);
        a.setName("A");
        a.start();
 
        ThreadB b = new ThreadB(service);
        b.setName("B");
        b.start();
 
        Thread.sleep(3000);
        service.signalAll_A();
    }

```

##### 方式四：基本 `LockSupport` 实现线程间的阻塞和唤醒

```java
public class TestSync {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        // 实现线程B
        final Thread threadB = new Thread(() -> {
            if (list.size() != 5) {
                LockSupport.park();
            }
            System.out.println("线程B收到通知，开始执行自己的业务...");
        });
        // 实现线程A
        Thread threadA = new Thread(() -> {
            for (int i = 1; i <= 10; i++) {
                list.add("abc");
                System.out.println("线程A向list中添加一个元素，此时list中的元素个数为：" + list.size());
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                if (list.size() == 5)
                    LockSupport.unpark(threadB);
            }
        });
        threadA.start();
        threadB.start();
    }
}
```

![在这里插入图片描述](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20190113073609459.png)

##### 生产者/消费者模式

###### 利用缓冲区解决：管程法

```java
package gaoji;

/**
 * @author LYJ
 * @create 2021-05-03 10:49
 * 测试：生产者消费者模型-->利用缓冲区解决：管程法
 * 生产者，消费者，产品，缓冲区
 */
public class TestPC {
    public static void main(String[] args) {
        SynContainer container = new SynContainer();

        new Productor(container).start();
        new Consumer(container).start();
    }
}

//生产者
class Productor extends Thread{

    SynContainer container;

    public Productor(SynContainer container){
        this.container = container;
    }

    @Override
    public void run() {
        for (int i=1;i<=100;i++){
            System.out.println("生产了---->id为" + i + "的鸡");
            container.push(new Chicken(i));
        }
    }
}

//消费者
class Consumer extends Thread{
    SynContainer container;
    public Consumer(SynContainer container){
        this.container = container;
    }

    @Override
    public void run() {
        for (int i=1;i<=100;i++){
            System.out.println("消费了---->id为" + container.pop().id + "的鸡");
        }
    }
}


//产品
class Chicken{
    int id; //产品编号
    public Chicken(int id){
        this.id = id;
    }
}

//缓冲区
class SynContainer{
    //容器大小
    Chicken[] chickens = new Chicken[10];
    int count = 0;//容器计数器

    //生产者放入产品
    public synchronized void push(Chicken chicken){
        //如果容器满了，就需要消费者消费
        if (count==chickens.length){
            //容器满了：通知生产者消费；生产者等待。
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        //如果容器没有满，生产者可以丢入产品
        chickens[count] = chicken;
        count++;
        //通知消费者消费
        this.notifyAll();
    }

    //消费者消费产品
    public synchronized Chicken pop(){
        //判断容器满不满(能否可以消费)
        if (count==0){
            //等待生产者生产，消费者等待
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        //如果可以消费
        count--;
        Chicken chicken = chickens[count];

        //消费了，通知生产者生产
        this.notifyAll();
        return chicken;
    }
}
```

###### 信号灯法(设置标志位)

```java
package gaoji;

/**
 * @author LYJ
 * @create 2021-05-03 11:27
 * 测试生产者消费者问题2：信号灯法(设置标志位)
 */
public class TestPC2 {
    public static void main(String[] args) {
        TV tv = new TV();
        new Player(tv).start();
        new Watcher(tv).start();
    }
}


//生产者---->演员
class Player extends Thread{
    TV tv;
    public Player(TV tv){
        this.tv = tv;
    }

    @Override
    public void run() {
        for (int i=0;i<20;i++){
            if (i%2==0){
                this.tv.play("快乐大本营");
            }
            else {
                this.tv.play("抖音：记录美好生活");
            }
        }
    }
}

//消费者---->观众
class Watcher extends Thread{
    TV tv;
    public Watcher(TV tv){
        this.tv = tv;
    }

    @Override
    public void run() {
        for (int i=0;i<20;i++)
            this.tv.watch();
    }
}

//产品---->节目
class TV{
    //演员表演，观众等待  T
    //观众观看，演员等待  F
    String voice; // 表演的节目
    boolean flag = true;

    //表演
    public synchronized void play(String voice){
        if (!flag){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        System.out.println("演员表演了：" + voice);
        //通知观众观看
        this.voice = voice;
        this.flag = !this.flag;
        this.notifyAll();
    }
    //观看
    public synchronized void watch(){
        if (flag){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println("观看了：" + voice);
        //通知演员表演
        this.flag = !this.flag;
        this.notifyAll();
    }
}
```

************

#### 上下文切换

多线程编程中一般线程的个数都大于CPU核心的个数，而一个CPU核心在任意时刻只能被一个线程使用，为了让这些线程都能得到有效执行，CPU采取的策略是为每个线程分配时间片并轮转的形式。当一个线程的时间片用完的时候就会重新处于就绪状态让给其他线程使用，这个过程就属于一次上下文切换。

概括来说就是：当前任务在执行完CPU时间片切换到另一个任务之前会先保存自己的状态，以便下次再切换回这个任务时，可以再加载这个任务的状态。**任务从保存到再加载的过程就是一次上下文切换。**

上下文切换通常是计算密集型的。也就是说，它需要相当可观的处理器时间，在每秒几十上百次的切换中，每次切换都需要纳秒量级的时间。所以，上下文切换对系统来说意味着消耗大量的CPU时间，事实上，可能是操作系统中时间消耗最大的操作。

Linux相比与其他操作系统(包括其他类Unix系统)有很多的优点，其中有一项就是，其上下文切换和模式切换的时间消耗非常少。

#### ==什么是线程死锁？如何避免死锁？==

> 多个线程同时被阻塞，他们中的一个或者全部都在等待某个资源被释放。由于线程被无限期地阻塞，因此程序不可能被正常终止。

![image-20210410110912831](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410110912831.png)

##### 产生死锁的四个条件

```markdown
**
1. 互斥条件：该资源任意一个时刻只由一个线程占用。
2. 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
3. 不剥夺条件：线程已获得的资源在未使用完之前不能被其他线程强行剥夺，只有自己使用完毕后才释放资源。
4. 循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系。
```

##### 如何避免死锁？

为了避免死锁，我们只要破坏死锁的四个条件中的其中一个就可以了：

1. **破坏互斥条件：**这个条件没法破坏，因为用锁本来就是想让他们互斥的(临界资源需要互斥访问)。
2. **破坏请求与保持条件：**一次性申请所有的资源。
3. **破坏不剥夺条件：**占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源。
4. **破坏循环等待条件：**靠按序申请资源来预防。按某一顺序申请资源，释放资源则反向释放。破坏循环等待条件。

***************************

#### ==synchronized关键字==

```markdown
锁主要存在四种状态，依次是：无锁状态、偏向锁状、轻量级锁状态、重量级锁状态，他们会随着竞争的激烈而逐渐升级。

其中锁可以升级不可降级，这种策略是为了提高获得锁和释放锁的效率。
```

**每个对象对应一把锁。**

`synchronized `关键字**解决的是多个线程之间访问资源的同步性**，`synchronized`关键字可以保证**被它修饰的方法或者代码块在任意时刻只能有一个线程执行。**

`synchronzied`的作用

- 原子性：确保线程互斥的访问同步代码；
- 可见性：保证共享变量的修改能够及时可见，其实是通过Java内存模型中的 “对一个变量unlock 操作之前，必须要同步到主内存中；如果对一个变量进行lock操作，则将会清空工作内存中此变量的值，在执行引擎使用此变量前，需要重新从主内存中load操作或assign操作初始化变量值” 来保证的；
- 有序性：有效解决重排序问题，即 “一个unlock操作先行发生(happen-before)于后面对同一个锁的lock操作”

##### synchronized关键字最主要的三种使用方式

- **修饰实例方法：**作用于当前对象实例加锁，进入同步代码前要获得当前对象实例的锁。![image-20210410143056419](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410143056419.png)

- **修饰静态方法(等价于修饰类)：**静态方法是随着类的加载而加载的，也就是给==当前类加锁，会作用于类的所有对象实例==，进入同步代码前要获得当前class的锁。因为静态成员不属于任何一个实例对象，是类成员(static表明这是该类的一个静态资源，不管new了多少个对象，只有一份)。所以，如果一个线程A调用一个实例对象的非静态`synchronized` 方法，而线程B需要调用这个实例对象所属类的静态`synchronized`方法，是允许的，不会发生互斥现象，**因为访问静态`synchronized`方法占用的锁是当前类的锁，而访问非静态`synchronized`方法占用的锁是当前实例对象锁。**![image-20210410143212519](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410143212519.png)

- **修饰代码块：**指定加锁对象，对给定对象/类加锁。`synchronized(this object)`表示进入同步代码库前要获得给定对象的锁。`synchronized(类.clas)`表示进入同步代码前要获得当前 class的锁。![image-20210410143317009](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410143317009.png)

  > ```java
  > Class A {
  >  public synchronized methodA() { //对当前对象加锁
  >     }
  > 
  >     public methodB() {
  >      synchronized(this){}//对当前对象加锁，与methodA用法相同
  >     }
  > 
  >     public static synchronized methodC() {}//对类加锁，即对所有此类的对象加锁
  > 
  >     public methodD(){
  >      synchronized(A.class){}//对类加锁，即对所有此类的对象加锁
  >     }
  > }
  >    ```

**总结：**
`synchronized`关键字加到 `static` 静态方法和`synchronized(class)`代码块上都是给 `Class` 类上锁。

`synchronized`关键字加到实例方法上是给对象实例上锁。

> **只要采用类锁，就会拦截所有线程，只能让一个线程访问。**
>
> **对于对象锁(this)，如果是同一个实例，线程就会按顺序访问，但是如果是不同实例，就可以同时访问。**

尽量不要使用`synchronized(String a)`因为 `JVM` 中，字符串常量池具有缓存功能！

##### synchronized关键字用在方法和代码块上的区别

> ```java
> //下列两个方法有什么区别
> public synchronized void method1(){}
> 
> public void method2(){
> 	synchronized  (obj){}
> }
> ```

如果该方法不是静态方法的话，二者没有区别。不过锁代码块的时候，代码会灵活一点，效率略高；

如果该方法是静态方法的话，给静态方法加锁就等价于给当前类加锁。

##### ==双重检验锁方式实现单例模式==

> 单例模式是 `Java` 中最简单的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
>
> 这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。例如，`Windows` 中只能打开一个任务管理器，这样可以避免因打开多个任务管理器窗口而造成内存资源的浪费，或出现各个窗口显示内容的不一致等错误。
>
> 使用场景：
>
> ​		对象需要被公用的场合适合使用，如多个模块使用同一个数据源连接对象等等：任务管理器、回收站、线程的线程池、数据库连接池、操作系统的文件系统。

![image-20210410144827557](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410144827557.png)

另外，需要注意`uniqueInstance`采用`volatile`关键字修饰也是很有必要。

`uniqueInstance` 采用`volatile `关键字修饰也是很有必要的，`uniqueInstance = new Singleton()`；这段代码其实是分为三步执行：

1. 为`uniqueInstance`分配内存空间
2. 初始化`uniqueInstance`
3. 将`uniqueInstance` 指向分配的内存地址

但是由于JVM具有指令重排的特性，执行顺序有可能变成 $1\rightarrow3\rightarrow2$ 。指令重排在单线程环境下不会出现问题，但是在多线程环境下会导致一个线程获得还没有初始化的实例。例如，线程T1执行了1和3，此时T2调用`getUniqueInstance()`后发现`uniqueInstance`不为空，因此返回`uniqueInstance`，但此时`uniqueInstance`还未被初始化。 

使用`volatile`可以禁止JVM的指令重排，保证在多线程的环境下也能正常运行。

###### **饿汉式**

```java
package LearnJUC.Single;

/**
 * @author LYJ
 * @create 2021-05-13 11:15
 * 饿汉式单例  直接创建单例  浪费空间
 */
public class Hungry {
    private Hungry(){
    }

    private static final Hungry hungry = new Hungry();

    public static Hungry getInstance(){
        return hungry;
    }
}
```

###### **懒汉式**

```java

public class Singleton1 {
 
    private static Singleton1 instance = null;
 
    private Singleton1() {
    }
 
    /**
     * 1、适用于单线程环境(不推荐)
     */
    public static Singleton1 getInstanceA() {
        if (null == instance) {
            instance = new Singleton1();
        }
        return instance;
    }
 
    /**
     * 2、适用于多线程环境，但效率不高(不推荐)
     */
    public static synchronized Singleton1 getInstanceB() {
        if (instance == null) {
            instance = new Singleton1();
        }
        return instance;
    }
 
    /**
     * 3、双重检查加锁(推荐)
     */
    public static Singleton1 getInstanceC() {
        // 先判断实例是否存在，若不存在再对类对象进行加锁处理
        if (instance == null) {
            synchronized (Singleton1.class) {
                if (instance == null) {
                    instance = new Singleton1();
                }
            }
        }
        return instance;
    }
}
```

###### 静态内部类

```java
package LearnJUC.Single;

/**
 * @author LYJ
 * @create 2021-05-13 11:36
 * 静态内部类实现单例模式
 */
public class Holder {
    private Holder(){

    }

    public static Holder getInstance(){
        return InnerClass.holder;
    }

    public static class InnerClass{
        private static final Holder holder = new Holder();
    }
}
```





##### 构造方法不能使用synchronized修饰

构造方法本身就属于线程安全的，不存在同步的构造方法一说。

##### ==synchronized 关键字的底层原理==

**synchronized 关键字底层原理属于 JVM 层⾯。**

###### 1. synchronized 修饰同步语句块的情况

![image-20210410145403866](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410145403866.png)

![image-20210410150832975](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410150832975.png)

`synchronized `同步语句块的实现使⽤的是 `monitorenter `和 `monitorexit `指令，其中`monitorenter `指令指向同步代码块的开始位置， `monitorexit `指令则指明同步代码块的结束位置。

当执行`monitorenter`指令时，线程试图获取锁也就是获取**对象监视器`monitor`**的持有权。

> 在Java虚拟机( HotSpot )中，Monitor是居于C++实现的，由`ObjectMonitor`实现。每个对象都内置了一个`ObjectMonitor`对象。
>
> 另外，`wait/notify`等方法也依赖于`monitor`对象，这也就是为什么只有在同步的块或方法中才能调用`wait/notify`等方法，否则会抛出`java.lang.IllegalMonitorStateException`异常。

**在执行`monitorenter`时，会尝试获取对象的锁，如果锁的计数器为0则表示锁可以被获取，获取后将锁计数器设为1也就是加1，该线程即为monitor的所有者；如果线程已经占有该monitor，只是重新进入，则进入monitor的进入数加1；如果其他线程已经占用了monitor，则该线程进入阻塞状态，直到monitor的进入数为0，再重新尝试获取monitor的所有权。**

**在执行`monitorexit `指令后，将锁计数器设为0，表明锁被释放。如果获取对象锁失败，那当前线程就要阻塞等待，直到锁被另外一个线程释放为止。**

==Synchronized的语义底层是通过一个monitor的对象来完成，其实wait/notify等方法也依赖于monitor对象，这就是为什么只有在同步块或者方法中才能调用wait/notify等方法，否则会抛出`java.lang.IllegalMonitorStateException`的异常的原因。==

###### 2. synchronized 修饰方法的情况

![image-20210410150405565](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410150405565.png)

![image-20210410150816498](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410150816498.png)

`synchronized` 修饰的方法并没有` monitorenter` 指令和` monitorexit `指令，取而代之的是`ACC_SYNCHRONIZED `标识，该标识指明该方法是一个同步方法，JVM 通过该`ACC_SYNCHRONIZED`访问标志来辨别一个方法是否声明为同步方法，从而执行相应的同步调用。

当方法调用时，调用指令将会检查方法的 `ACC_SYNCHRONIZED` 访问标志是否被设置，如果设置了，执行线程将先获取`monitor`，获取成功之后才能执行方法体，方法执行完后再释放 `monitor`。在方法执行期间，其他任何线程都无法再获得同一个 `monitor` 对象。 其实本质上没有区别，只是方法的同步是一种隐式的方式来实现，无需通过字节码来完成。

###### 总结
==`synchronized `同步语句块的实现使用的是`monitorenter`和`monitorexit `指令，其中`monitorenter `指令指向同步代码块的开始位置，`monitorexit` 指令则指明同步代码块的结束位；`synchronized` 修饰的方法并没有`monitorenter`指令和`monitorexit `指令，取得代之的确实是`ACC_SYNCHRONIZED `标识，该标识指明了该方法是一个同步方法。==

不过两者的**本质都是对对象监视器`monitor`的获取。**

*******************

#### Java中的锁

**分类：**

- **乐观锁/悲观锁**
  
  - 乐观锁：每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，一般会使用“数据版本机制”或“CAS操作”来实现。乐观锁适用于多读的应用类型，这样可以提高吞吐量，在Java中 `java.util.concurrent.atomic` 包下面的原子变量类就是使用了乐观锁的一种实现方式**[CAS](####CAS)**(Compare and Swap 比较并交换)实现的。
    - 乐观锁适用于写比较少的情况下(多读场景)，即冲突真的很少发生的时候，这样可以省去了锁的开销，加大了系统的整个吞吐量。
    - **乐观锁的问题：**ABA问题、自旋时间长开销大、只能保证一个共享变量的原子操作( CAS只对单个共享变量有效)。
  - 悲观锁：每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁。比如Java里面的同步原语**synchronized关键字**的实现就是悲观锁。
    - 乐观锁适用于读比较少的情况下(多写场景)，如果是多写的情况，一般会经常产生冲突，这就会导致上层应用会不断的进行retry，这样反倒是降低了性能，所以一般多写的场景下用悲观锁就比较合适。
  
- 独享锁/共享锁
  - 独享锁是指该锁一次只能被一个线程所持有。共享锁是指该锁可被多个线程所持有。
    - 对于Java `ReentrantLock` 而言，其是独享锁。但是对于Lock的另一个实现类 `ReadWriteLock`，其读锁是共享锁，其写锁是独享锁。
  - 读锁的共享锁可保证并发读是非常高效的，读写，写读，写写的过程是互斥的。
    - 独享锁与共享锁也是通过AQS来实现的，通过实现不同的方法，来实现独享或者共享。
    - 对于 `Synchronized` 而言，当然是独享锁。
  
- 互斥锁/读写锁
  - 独享锁/共享锁就是一种广义的说法，互斥锁/读写锁就是具体的实现。
  - 互斥锁在Java中的具体实现就是 `ReentrantLock` 。读写锁在Java中的具体实现就是 `ReadWriteLock`。
  
- **可重入锁**
  
  - 可重入锁又名递归锁，是指在同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。说的有点抽象，下面会有一个代码的示例。
  - 对于Java `ReetrantLock` 而言，从名字就可以看出是一个重入锁，其名字是 `ReetrantLock` 重新进入锁。
  - 对于 `Synchronized` 而言，也是一个可重入锁。可重入锁的一个好处是可一定程度避免死锁。
  
- 公平锁/非公平锁
  - 公平锁是指多个线程按照申请锁的顺序来获取锁。
  - 非公平锁是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁。有可能，会造成优先级反转或者饥饿现象.
  - 对于Java `ReetrantLock` 而言，通过构造函数指定该锁是否是公平锁，默认是非公平锁。非公平锁的优点在于吞吐量比公平锁大。
  - 对于 `Synchronized` 而言，也是一种非公平锁。由于其并不像 `ReentrantLock`是通过AQS的来实现线程调度，所以并没有任何办法使其变成公平锁。
  
- 分段锁

- 偏向锁/轻量级锁/重量级锁

- 自旋锁

  - ```markdown
    ** 
    > 是指当一个线程在获取锁的时候，如果锁已经被其它线程获取，那么该线程将循环等待，然后不断的判断锁是否能够被成功获取，直到获取到锁才会退出循环。
    	线程自旋是需要消耗cpu的，说白了就是让cpu在做无用功，如果一直获取不到锁，那线程也不能一直占用cpu自旋做无用功，所以需要设定一个自旋等待的最大时间。
    **
    ```
    

##### 互斥锁、读写锁和自旋锁的区别

###### 读写锁

- 读写锁有三种状态: 读加锁、写加锁和不加锁状态。
- 只有一个线程可以占有写状态的锁，但可以有多个线程占有读状态的锁。
  - 这也是它可以实现高并发的原因。
  - 当处于写锁的状态下，任何想要尝试获取锁的线程都会被阻塞，直到写状态锁被释放;
  - 当处于读锁的状态下，允许其它线程获得它的读状态锁，但是不允许获得它的写状态锁，直到所有线程的读状态锁被释放;
  - 为了避免想要尝试写操作的线程一直得不到写状态锁，当处于读模式的读写锁接收到一个试图对其进行写模式加锁操作时，便会阻塞后面对其进行读模式加锁操作的线程。
- 多个读的线程可以同时进行读; 写必须互斥：只允许一个写; 写由于读：一旦有写, 则后续的读必须等待，唤醒时优先考虑写。

###### 互斥锁

- > 一次只能有一个线程拥有互斥锁, 其他线程只能等待。

- 在访问共享资源之前对进行加锁操作，在访问完成之后进行解锁操作。加锁后，任何其他试图再次加锁的线程会被阻塞，直到当前线程解锁。如果解锁时有一个以上的线程阻塞，那么所有该锁上的线程都被编程就绪状态， 第一个变为就绪状态的线程又执行加锁操作，那么其他的线程又会进入等待。 在这种方式下，只有一个线程能够访问被互斥锁保护的资源。

###### 自旋锁

- 自旋锁是一种特殊的互斥锁，当资源被枷锁后，其他线程想要再次加锁，此时该线程不会被阻塞睡眠而是陷入循环等待状态(CPU不能做其它事情)，循环检查资源持有者是否已经释放了资源，这样做的好处是减少了线程从睡眠到唤醒的资源消耗，但会一直占用CPU的资源。适用于资源的锁被持有的时间短，而又不希望在线程的唤醒上花费太多资源的情况。

******************

##### 锁升级

> 无锁(锁对象初始化时)$－＞$　偏向锁(有线程请求锁) $－＞$ 轻量级锁(多线程轻度竞争)$－＞$重量级锁(线程过多或长耗时操作，线程自旋过度消耗cpu)；
>
> 锁一共有四种状态，级别从低到高依次是：无锁状态、偏向锁状态、轻量级锁状态和重量级锁状态，这几个状态随着竞争情况逐渐升级。为了提高获得锁和释放锁的效率，锁可以升级但不能降级，意味着偏向锁升级为轻量级锁后不能降级为偏向锁。
>
> ```markdown
> **
> 1. 首先是无锁状态
> 2. 线程首次获取锁的时候，会在对象头中加一个锁标志，表示锁偏向当前线程，即偏向锁。
> 3. 另一个线程需要获取锁的时候，会直接比较对象锁标志
> 	3.1 如果线程 `id` 相同，表示是当前线程，则进入或者重入，仍然是偏向锁;
> 	3.2 如果锁标志不相等，则通过CAS获取当前锁.
> 	   3.2.1 如果获取成功，表明之前的线程已经释放锁了，直接修改对象标识为当前线程，此时锁偏向当前线程，仍然是偏向锁。
> 	   3.2.2 如果CAS操作失败，即之前的线程还持有锁，表明存在并发冲突的可能，锁升级为轻量级锁。
> 4. 相比较偏向锁只比较对象头的锁标志，轻量级锁使用CAS自旋操作。即后续线程进来后，不再比较对象的锁标志，而是直接通过CAS自旋获取锁。
> 	4.1 如果在限定时间内，获取成功，则仍是轻量级锁(相当于乐观锁)。
> 	4.2 如果失败，表明并发冲突很大，等待时间较长，升级为重量级锁，需要获取对象的监视器。
> **
> ```



> 锁的状态就在对象头的 `mark word`上，它是java对象数据结构中的一部分，对象的`mark word`和java各种类型的锁密切相关，用来存储对象自身的运行时数据，如 `hashcode`、``gc分代年龄``等。
>
> `mark word` 数据的长度在32位和64位的虚拟机中分别为32bit和64bit，它的最后2bit是锁状态标志位，用来标记当前对象的状态，对象所处的状态，决定了 `mark word` 存储的内容。
>
> ![image-20210815100818841](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210815100818841.png)
>

#### 锁优化

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1285727-20180625070314183-2036349251.png)

锁优化的思路和方法有以下几种：

- 减少锁持有时间
- 减小锁粒度
- 锁分离
- 锁粗化
- 锁消除

##### 减少锁的持有时间

例如：避免给整个方法加锁

> ```java
> public synchronized void syncMethod(){  
>         othercode1();  
>         mutextMethod();  
>         othercode2(); 
>     }
> // 像上述代码这样，在进入方法前就要得到锁，其他线程就要在外面等待。
> // 这里优化的一点在于，要减少其他线程等待的时间，所以，只需要在有线程安全要求的程序代码上加锁。
> public void syncMethod(){  
>         othercode1();  
>         synchronized(this)
>         {
>             mutextMethod();  
>         }
>         othercode2(); 
>     }
> ```

##### 减小锁粒度

将大对象，拆成小对象，大大增加并行度，降低锁竞争. 如此一来偏向锁，轻量级锁成功率提高。

**最最典型的减小锁粒度的案例就是 `ConcurrentHashMap`。**

##### 锁分离

最常见的锁分离就是读写锁 `ReadWriteLock`，根据功能进行分离成读锁和写锁，这样读读不互斥，读写互斥，写写互斥。即保证了线程安全，又提高了性能。

读写分离思想可以延伸，只要操作互不影响，锁就可以分离。

比如 `LinkedBlockingQueue`：

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20170814142947191)

从头部取出数据，从尾部放入数据，使用两把锁。

##### 锁粗化

通常情况下，为了保证多线程间的有效并发，会要求每个线程持有锁的时间尽量短，即在使用完公共资源后，应该立即释放锁。只有这样，等待在这个锁上的其他线程才能尽早的获得资源执行任务。

但是，凡事都有一个度，如果对同一个锁不停的进行请求、同步和释放，其本身也会消耗系统宝贵的资源，反而不利于性能的优化 。

> ```java
> for(int i = 0; i < 1000; i++){
>         synchronized(lock){
> 
>         }
>     }
> 
>     // 优化后
>     synchronized(lock){
>         for(int i = 0;i < 1000; i++){
> 
>         }
>     }
> ```

##### 锁消除

锁消除是在编译器级别的事情。

在即时编译器时，如果发现不可能被共享的对象，则可以消除这些对象的锁操作。

也许你会觉得奇怪，既然有些对象不可能被多线程访问，那为什么要加锁呢？写代码时直接不加锁不就好了。

但是有时，这些锁并不是程序员所写的，有的是JDK实现中就有锁的，比如 `Vector` 和 `StringBuffer` 这样的类，它们中的很多方法都是有锁的。当我们在一些不会有线程安全的情况下使用这些类的方法时，达到某些条件时，编译器会将锁消除来提高性能。



#### 为什么要弄⼀个 CPU ⾼速缓存呢？

类似开发网站后台系统使用的缓存(比如 `Redis` )是为了解决程序处理速度和访问常规关系星数据库速度不对等的问题。 **CPU 缓存则是为了解决 CPU 处理速度和内存处理速度不对等的问题。**

我们甚至可以把**内存看成外存的高速缓存**，程序运行的时候我们把外存的数据复制到内存，由于内存的处理速度远远高于外存，这位提高了处理速度。

总结：**CPU Cache缓存的是内存数据，用于解决CPU处理速度和内存不匹配的问题，内存缓存的是硬盘数据，用于解决硬盘访问速度过慢的问题。**

![image-20210410151917858](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410151917858.png)

CPU Cache的工作方式：
先复制一份数据到CPU Cache中，当CPU需要用到的时候就可以直接从CPU Cache中读取数据，当运算完成后，再将运算得到的数据写回Main Memory中。但是，这样存在**内存缓存不一致性的问题！**比如我执行一个$i++$操作的话，如果两个线程同时执行的话，假设两个线程从CPU Cache中读取的$i=1$，两个线程做了$i++$运算完之后再写回Main Memory之后$i=2$，而正确结果应该是$i=3$。

**CPU为了解决内存缓存不一致问题可以通过制定缓存一致协议或者其他手段来解决。**

#### JMM(Java内存模型 Java Memory Model)

##### 概念

在并发编程领域，有两个关键问题：线程之间的**通信**和**同步**。

- 线程之间的**通信机制**有两种**共享内存**和**消息传递**。
  - **共享内存：**线程之间共享程序的公共状态，线程之间通过写-读内存中的公共状态来隐式进行通信，典型的共享内存通信方式就是通过**共享对象**进行通信。
  - **消息传递：**线程之间没有公共状态，线程之间必须通过明确的发送消息来显式进行通信，在java中典型的消息传递方式就是 **`wait()`** 和 **`notify()`**。
- **同步**是指程序用于控制不同线程之间操作发生相对顺序的机制。
  - 在**共享内存**并发模型里，同步是**显式进行**的。程序员必须显式指定某个方法或某段代码需要在线程之间互斥执行。
  - 在**消息传递**的并发模型里，由于消息的发送必须在消息的接收之前，因此同步是**隐式进行**的

​    

Java线程之间的通信采用的是 **共享内存** 模型，这里提到的共享内存模型指的就是**Java内存模型(简称`JMM`)**，**JMM决定一个线程对共享变量的写入何时对另一个线程可见**。从抽象的角度来看，`JMM` 定义了线程和主内存之间的抽象关系：**线程之间的共享变量存储在主内存(main memory)中，每个线程都有一个私有的本地内存(local memory)，本地内存中存储了该线程以读/写共享变量的副本**。本地内存是JMM的一个抽象概念，并不真实存在。它涵盖了缓存，写缓冲区，寄存器以及其他的硬件和编译器优化。

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTYwOTIxMTgyMzM3OTA0)

从上图来看，线程A与线程B之间如要通信的话，必须要经历下面2个步骤：

```markdown
1. 首先，线程A把本地内存A中更新过的共享变量刷新到主内存中去。
2. 然后，线程B到主内存中去读取线程A之前已更新过的共享变量。
```



##### JVM对Java内存模型的实现

在 `JVM` 内部，**Java内存模型**把内存分成了两部分：**线程栈区和堆区**，下图展示了Java内存模型在JVM中的逻辑视图：

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTYwOTIxMTgyODM3Njk3)

JVM中运行的每个线程都拥有自己的线程栈，

```markdown
**
1. 线程栈包含了当前线程执行的方法调用的相关信息, 又称调用栈。随着代码的不断执行，调用栈会不断变化。
2. 所有原始类型(boolean,byte,short,char,int,long,float,double)的本地变量都直接保存在线程栈当中，对于它们的值各个线程之间都是独立的。对于原始类型的本地变量，一个线程可以传递一个副本给另一个线程，它们之间是无法共享的。
>  堆区包含了Java应用创建的所有对象信息，不管对象是哪个线程创建的，其中的对象包括原始类型的封装类(如Byte、Integer、Long等等)。不管对象是属于一个成员变量还是方法中的本地变量，它都会被存储在堆区。
**
```

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTYwOTIxMTgyOTQ4NjAx)

```markdown
**
1. 对于本地变量 
	1.1 如果是原始类型，那么它会被完全存储到栈区。
	1.2 如果是一个对象的引用，这种情况下，这个本地引用会被存储到栈中，但是对象本身仍然存储在堆区。
2. 对于对象的成员方法，这些方法中包含本地变量，仍需要存储在栈区，即使它们所属的对象在堆区。
3. 对于对象的成员变量，不管它是原始类型还是包装类型，都会被存储到堆区。
4. Static类型的变量以及类本身相关信息都会随着类本身存储在堆区。
5. 堆中的对象可以被多线程共享。如果一个线程获得一个对象的应用，它便可访问这个对象的成员变量。如果两个线程同时调用了同一个对象的同一个方法，那么这两个线程便可同时访问这个对象的成员变量，但是对于本地变量，每个线程都会拷贝一份到自己的线程栈中
**
```

##### 硬件内存架构

不管是什么内存模型，最终还是运行在计算机硬件上的，所以我们有必要了解计算机硬件内存架构，下图就简单描述了当代计算机硬件内存架构：

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTYwOTIxMTgzMDEzNTcw)

现代计算机一般都有2个以上CPU，而且每个CPU还有可能包含多个核心。因此，如果我们的应用是多线程的话，这些线程可能会在各个CPU核心中并行运行。

在CPU内部有一组CPU寄存器，也就是CPU的储存器。CPU操作寄存器的速度要比操作计算机主存快的多。在主存和CPU寄存器之间还存在一个CPU缓存，CPU操作CPU缓存的速度快于主存但慢于CPU寄存器。某些CPU可能有多个缓存层(一级缓存和二级缓存)。计算机的主存也称作RAM，所有的CPU都能够访问主存，而且主存比上面提到的缓存和寄存器大很多。

当一个CPU需要访问主存时，会先读取一部分主存数据到CPU缓存，进而在读取CPU缓存到寄存器。当CPU需要写数据到主存时，同样会先flush寄存器到CPU缓存，然后再在某些节点把缓存数据flush到主存。

**Java内存模型和硬件架构之间的桥接**

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTYwOTIxMTgzMTQ0OTk1)

当对象和变量存储到计算机的各个内存区域时，必然会面临一些问题，其中最主要的两个问题是：

```markdown
1. 共享对象对各个线程的可见性
2. 共享对象的竞争现象
```

###### 共享对象的可见性

​	在 `JDK1.2` 之前，`Java` 的内存模型实现总是从**主存**(即共享内存)读取变量，是不需要进行特别的注意的。而在当前的`Java` 内存模型下，线程可以把变量保存在**本地内存**(比如机器的寄存器)中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成**数据的不一致**，即**缓存一致性问题**。

![image-20210410152416090](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410152416090.png)

要解决这个问题，就需要把变量声明为`volatile`，这就指示 `JVM`，这个变量是共享且不稳定的，每次使用它都到主存中进行读取。

所以，**`volatile `关键字除了防止JVM的指令重排，还有一个重要的作用就是保证变量的可见性。**

![image-20210410152719877](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410152719877.png)





#### ==Volatile==

> 谈谈你对Volatile的理解

Volatile是Java中的一个关键字，是Java虚拟机提供的**轻量级的同步机制**。

#####　保证可见性

> > 可见性：当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看到修改的值。
> >
> > ###### ![image-20210513094202241](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210513094202241.png)**实现原理：**
> >
> > 1. **缓存一致性协议：**
> >
> >    - ```markdown
> >     **
> >      1. 现代处理器为了提高处理速度，在处理器和内存之间增加了多级缓存，处理器不会直接去和内存通信，将数据读到内部缓存中再进行操作。由于引入了多级缓存，就存在缓存数据不一致问题。
> >     2. 缓存一致性协议: 每个处理器通过嗅探在总线上传播的数据来检查自己缓存的值是不是过期了，当处理器发现自己缓存行对应的内存地址被修改，就会将当前处理器的缓存行设置成无效状态，当处理器要对这个数据进行修改操作的时候，会强制重新从系统内存里把数据读到处理器缓存里。
> >        **
> >      ```
> > 
> >    - 如果一个变量被 **`volatile`** 所修饰的话，在每次数据变化之后，其值都会被强制刷入主存。而其他处理器的缓存由于遵守了**缓存一致性协议**，也会把这个变量的值从主存加载到自己的缓存中。这就保证了一个 **`volatile`** 在并发编程中，其值在多个缓存中是可见的。
> > 
> > 2. **`happen-before`**
> >
> >    - 对 **`volatile`** 域的**写入操作** **`happends-before`** 于每一个后续对同一域的**读操作**。
> >     当我们去写一个 **`volatile`** 变量的时候，JMM会把该线程对应的本地内存中的共享变量值刷新到主内存中，读一个 **`volatile`** 变量的时候，JMM会把该线程对应的本地内存置为无效，接下来线程从主内存中读取共享变量。两个线程，线程A写一个 **`volatile`** 变量，线程B随后读这个 **`volatile`** 变量。这个过程实际上就是线程A和线程B通过**主内存**进行通信(线程间通信)。
> > 
> >   - ```markdown
> >      # happen-before法则
> >      1. 程序次序法则：按照代码顺序执行
> >     2. 监视器锁法则：一个unlock操作要先于同一个锁的lock操作
> >      3. volatile变量法则：对volatile域的写入操作happends-before于每一个后续对同一域的读操作
> >      4. 线程启动法则：在一个线程里，对Thread.start()的调用会先于Thread.run()；
> >      5. 线程终结法则：线程中的任何动作都happends-before于其他线程检测到这个线程已经终结，或者从Thread.join 调用中成功返回，或者Thread.isAlive返回false
> >      > 中断法则：一个线程调用另一个线程的interrupt.happens-before于被中断的线程发现中断。(通过跑出interruptedException,或者调用isInterrupted和interrupted)
> >      6. 终结法则：一个对象的构造函数的结束happends-before于这个对象finalizer的开始。
> >      7. 传递性：如果A happens-before于B, 且B happends-before 于C, 则A happens-before 于C
> >     ```
> > 
> > 

##### ==不保证原子性==

> > 原子性：一个操作或者多个操作 要么全部执行并且执行的过程不会被任何因素打断，要么就都不执行。
> >
> > > ```java
> > > package LearnJUC.LearnVolatile;
> > > 
> > > public class VolatileDemo {
> > >  private volatile static int num = 0;
> > > 
> > >  public static void Plus(){
> > >      ++num;
> > >  }
> > > 
> > >  public static void main(String[] args) {
> > >      // 理论上结果应该为20000，可是最后不是
> > >      for (int i = 0; i < 20; i++) {
> > >          new Thread(()->{
> > >              for (int j = 0; j < 1000; j++) {
> > >                  Plus();
> > >              }
> > >          }).start();
> > >      }
> > >      System.out.println(Thread.currentThread().getName() + "===>" + num);
> > >  }
> > > }
> > > ```
> > >
> > > **如果不加lock和synchronized，怎么样保证原子性**
> > >
> > > 使用原子类
> > >
> > > ![image-20210513095621882](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210513095621882.png)
> > >
> > > ```java
> > > import java.util.concurrent.atomic.AtomicInteger;
> > > 
> > > public class VolatileDemo {
> > >  private volatile static AtomicInteger num = new AtomicInteger(); // 原子类的Integer
> > > 
> > >  public synchronized static void Plus(){
> > > //        ++num; // 不是原子性操作
> > >      num.getAndIncrement(); // AtomicInteger的+1操作 (CAS)
> > >  }
> > > 
> > >  public static void main(String[] args) {
> > >      for (int i = 0; i < 20; i++) {
> > >          new Thread(()->{
> > >              for (int j = 0; j < 1000; j++) {
> > >                  Plus();
> > >              }
> > >          }).start();
> > >      }
> > >      System.out.println(Thread.currentThread().getName() + "===>" + num);
> > >  }
> > > }
> > > ```
> > >
> > > 

##### 禁止指令重排

> > 有序性：即程序执行的顺序按照代码的先后顺序执行。
> >
> > 指令重排序：一般来说，处理器为了提高程序运行效率，可能会对输入代码进行优化，它**不保证程序中各个语句的执行先后顺序同代码中的顺序一致**，但是它会保证程序最终执行结果和代码顺序执行的结果是一致的。
> >
> > ```java
> > int i = 0;              
> > boolean flag = false;
> > i = 1;                //语句1  
> > flag = true;          //语句2
> > ```
> >
> > 语句1和语句2谁先执行对最终的程序结果并没有影响，那么就有可能在执行过程中，语句2先执行而语句1后执行。但是要注意，虽然处理器会对指令进行重排序，但是它会保证程序最终结果会和代码顺序执行结果相同。
> >
> > **因为处理器在进行重排序时是会==考虑指令之间的数据依赖性==，如果一个指令Instruction 2必须用到Instruction 1的结果，那么处理器会保证Instruction 1会在Instruction 2之前执行。**
> >
> > ==指令重排序不会影响单个线程的执行，但是会影响到线程并发执行的正确性。==
> >
> > ```java
> > //线程1:
> > context = loadContext();   //语句1
> > inited = true;             //语句2
> > 
> > //线程2:
> > while(!inited ){
> > sleep()
> > }
> > doSomethingwithconfig(context);
> > ```
> >
> > 由于语句1和语句2没有数据依赖性，因此可能会被重排序。假如发生了重排序，在线程1执行过程中先执行语句2，而此时线程2会以为初始化工作已经完成，那么就会跳出while循环，去执行 `doSomethingwithconfig(context)` 方法，而此时context并没有被初始化，就会导致程序出错。

###### volatile的原理和实现机制

> “观察加入volatile关键字和没有加入volatile关键字时所生成的汇编代码发现，加入volatile关键字时，会多出一个**lock前缀指令**”
>
> 　　lock前缀指令实际上相当于一个**内存屏障**(也称内存栅栏)，内存屏障会提供3个功能：
>
> ```markdown
> **
> 1. 它确保指令重排序时不会把其后面的指令排到内存屏障之前的位置，也不会把前面的指令排到内存屏障的后面；即在执行到内存屏障这句指令时，在它前面的操作已经全部完成；
> 2. 它会强制将对缓存的修改操作立即写入主存;
> 3. 如果是写操作，它会导致其他CPU中对应的缓存行无效。
> ```
>
> > - Store：将处理器缓存的数据刷新到内存中。
> > - Load：将内存存储的数据拷贝到处理器的缓存中。
> >
> > | 屏障类型          | 指令示例                 | 说明                                                         |
> > | :---------------- | :----------------------- | :----------------------------------------------------------- |
> > | `LoadLoad `屏障   | Load1;LoadLoad;Load2     | 该屏障确保Load1数据的装载先于Load2及其后所有装载指令的的操作。 |
> > | `StoreStore `屏障 | Store1;StoreStore;Store2 | 该屏障确保Store1立刻刷新数据到内存(使其对其他处理器可见)的操作先于Store2及其后所有存储指令的操作。 |
> > | `LoadStore `屏障  | Load1;LoadStore;Store2   | 确保Load1的数据装载先于Store2及其后所有的存储指令刷新数据到内存的操作。 |
> > | `StoreLoad `屏障  | Store1;StoreLoad;Load2   | 该屏障确保Store1立刻刷新数据到内存的操作先于Load2及其后所有装载装载指令的操作。它会使该屏障之前的所有内存访问指令(存储指令和访问指令)完成之后,才执行该屏障之后的内存访问指令。 |
> >
> > StoreLoad Barriers同时具备其他三个屏障的效果，因此也称之为`全能屏障`，是目前大多数处理器所支持的；但是相对其他屏障，该屏障的开销相对昂贵。

*******************

#### 说说 synchronized 关键字和 volatile 关键字的区别

`synchronized `关键字和 `volatile `关键字是两个互补的存在，而不是对立的存在

- **`volatile `关键字是线程同步的轻量级实现，所以 `volatile `性能肯定⽐ `synchronized `关键字要好。但是 `volatile `关键字只能⽤于变量⽽ `synchronized `关键字还可以用于修饰⽅法以及代码块。**
- **`volatile `关键字能保证数据的可⻅性，但不能保证数据的原⼦性。`synchronized `关键字两者都能保证。**
- **`volatile `关键字主要⽤于解决变量在多个线程之间的可⻅性，⽽ `synchronized `关键字解决的是多个线程之间访问资源的同步性。**



#### synchronized和lock的区别

- `lock `是一个接口，而 `synchronized `是 `java` 的一个关键字，`synchronized`是内置的语言实现；
- `Lock `是显式锁(手动开启和关闭锁)；`synchronized `是隐式锁，出了作用域自动释放；
  - `Lock `只有代码块锁，`synchronized` 有代码块锁和方法锁；
- `synchronized `在发生异常时候会自动释放占有的锁，因此不会出现死锁；而 `lock `发生异常的时候，不会主动释放占有的锁，必须手动 `unlock `来释放锁，可能引起死锁的发生。(所以 `lock `将同步代码块用 `try catch` 包起来，`finally`中写入 `unlock`，避免死锁的发生。)
- `lock `等待锁过程中可以用 `interrupt` 来中断等待，而 `synchronized` 只能等待锁的释放，不能响应中断；
- `Lock `可以通过 `trylock` 来知道有没有获取锁，而 `synchronized` 不能；
- ` synchronized` 的锁可重入、不可中断、非公平，而 `Lock `锁可重入、可中断、可公平(两者皆可)
- ` synchronized` 使用Object对象本身的wait 、notify、notifyAll调度机制，而Lock可以使用Condition进行线程之间的调度；

#### ==Java Lock的底层实现原理==

Lock完全用Java写成，在java这个层面是无关JVM实现的。

在`java.util.concurrent.locks`包中有很多Lock的实现类，常用的有`ReentrantLock`、`ReadWriteLock`(实现类`ReentrantReadWriteLock`)，其实现都依赖`java.util.concurrent.AbstractQueuedSynchronizer`类，实现思路都大同小异，因此我们以`ReentrantLock`作为讲解切入点。

`ReentrantLock`把所有Lock接口的操作都委派到一个Sync类上，该类继承了`AbstractQueuedSynchronizer`：

![image-20210428103931161](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210428103931161.png)

Sync又有两个子类:

![image-20210428103950708](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210428103950708.png)

显然是为了支持公平锁和非公平锁而定义，默认情况下为非公平锁。 

##### ReentrantLock.lock()方法的调用过程(默认非公平锁)

![image-20210428104501995](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210428104501995.png)



##### ==实现Lock锁的基本思想==

两个必备元素：

- ==一个是表示锁状态的变量(我们假设0表示没有线程获取锁，1表示有线程占有锁)。**该变量必须声明为volatile类型**==
- ==一个是队列，队列中的结点表示因未能获取锁而阻塞的线程。==

为了解决多核处理器下多线程缓存不一致的问题，表示状态的变量必须声明为 `voaltile` 类型，并且对表示状态的变量和队列的某些操作要保证原子性和可见性。原子性和可见性的操作主要通过Atomic包中的方法实现。

###### 线程获取锁的大致过程

1. 读取表示锁状态的变量
2. 如果表示锁状态的变量的值为 0，那么当前线程尝试将变量值设置为1(通过CAS操作完成)，当多个线程同时将表示状态的变量值由0设置为1时，仅有一个线程能成功，其余线程会失败：
   1. 若成功，表示获取了锁。
      1. 如果该线程位于队列中，则将其出队列(并将下一个结点变成队列的头结点**`FIFO`**).
      2. **如果当前线程未入列，则不用对队列进行维护**。
   2. 若失败，则当前线程将自身放入等待队列中并阻塞自身，此时线程一直被阻塞在 `lock` 方法中，没有从该方法中返回(**被唤醒后仍然在lock方法中，并从下一条语句开始执行，这里会回到第一步重新开始**)。
3. 如果表示状态的变量的值为1，那么将当前线程放入等待队列中，然后将自身阻塞**(被唤醒后仍然在lock方法中，并从下一条语句继续执行，这里又会回到第1步重新开始)**。



##### 锁实现(加锁)

简单说来，`AbstractQueuedSynchronizer`会把所有的请求线程构成一个 `CLH `队列，当一个线程执行完毕(`lock.unlock()`)时会激活自己的后继节点，但正在执行的线程并不在队列中，而那些等待执行的线程全部处于阻塞状态，经过调查线程的显式阻塞是通过调用`LockSupport.park()`完成，而`LockSupport.park()`则调用`sun.misc.Unsafe.park()`本地方法，再进一步，`HotSpot`在Linux中中通过调用`pthread_mutex_lock`函数把线程交给系统内核进行阻塞。

![image-20210428105127467](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210428105127467.png)

与 `synchronized `相同的是，这也是一个虚拟队列，不存在队列实例，仅存在节点之间的前后关系。令人疑惑的是为什么采用CLH队列呢？原生的CLH队列是用于自旋锁，但Doug Lea把其改造为阻塞锁。 
**当有线程竞争锁时，该线程会首先尝试获得锁，这对于那些已经在队列中排队的线程来说显得不公平，这也是非公平锁的由来**，与synchronized实现类似，这样会极大提高吞吐量。 
如果已经存在Running线程，则新的竞争线程会被追加到队尾，具体是采用基于CAS的Lock-Free算法，因为线程并发对Tail调用CAS可能会导致其他线程CAS失败，解决办法是**循环CAS直至成功**。

###### `Sync.nonfairTryAcquire`

`nonfairTryAcquire`方法将是lock方法间接调用的第一个方法，每次请求锁时都会首先调用该方法。

该方法会使用`c = getState()`判断当前状态，如果c\==0说明没有线程正在竞争该锁，如果不c !=0 说明有线程正拥有了该锁。 
如果发现c==0，则通过CAS设置该状态值为acquires, acquires的初始调用值为1，每次线程重入该锁都会+1，每次unlock都会-1，但为0时释放锁。如果CAS设置成功，则可以预计其他任何线程调用CAS都不会再成功，也就认为当前线程得到了该锁，也作为Running线程，**很显然这个Running线程并未进入等待队列。** 
如果c !=0 但发现自己已经拥有锁，只是简单地++acquires，并修改status值，但因为没有竞争，所以通过`setStatus`修改，而非CAS，也就是说这段代码实现了偏向锁的功能，并且实现的非常漂亮。

###### `AbstractQueuedSynchronizer.addWaiter`

`addWaiter`方法负责把**当前无法获得锁的线程包装为一个Node添加到队尾**：

其中参数mode是独占锁还是共享锁，默认为null，独占锁。追加到队尾的动作分两步： 
如果当前队尾已经存在(tail!=null)，则使用CAS把当前线程更新为Tail 
如果当前**Tail为null或则线程调用CAS设置队尾失败**，则通过enq方法继续设置Tail 。

`enq`该方法就是**循环调用CAS**，即使有高并发的场景，无限循环将会最终成功把当前线程追加到队尾(或设置队头)。总而言之，**`addWaiter`的目的就是通过CAS把当前线程追加到队尾，并返回包装后的Node实例。**

把线程要包装为Node对象的主要原因，除了用Node构造供虚拟队列外，还用Node包装了各种线程状态，这些状态被精心设计为一些数字值：

- SIGNAL(-1) ：线程的后继线程正/已被阻塞，当该线程release或cancel时要重新这个后继线程(`unpark`)
- CANCELLED(1)：因为超时或中断，该线程已经被取消
- CONDITION(-2)：表明该线程被处于条件队列，就是因为调用了`Condition.await`而被阻塞
- PROPAGATE(-3)：传播共享锁
- 0：0代表无状态

###### `AbstractQueuedSynchronizer.acquireQueued`

`acquireQueued`的主要作用是把已经追加到队列的线程节点(`addWaiter`方法返回值)进行阻塞，但阻塞前又通过`tryAccquire`重试是否能获得锁，如果重试成功能则无需阻塞，直接返回。

###### 解锁

请求锁不成功的线程会被挂起在`acquireQueued`方法的第12行，12行以后的代码必须等线程被解锁锁才能执行，假如被阻塞的线程得到解锁，则执行第13行，即设置interrupted = true，之后又进入无限循环。

并不是得到解锁的线程一定能获得锁，必须调用`tryAccquire`重新竞争，因为锁是非公平的，有可能被新加入的线程获得，**从而导致刚被唤醒的线程再次被阻塞**，这个细节充分体现了“非公平”的精髓。通过之后将要介绍的解锁机制会看到，第一个被解锁的线程就是Head，因此p == head的判断基本都会成功。

解锁代码相对简单，主要体现在`AbstractQueuedSynchronizer.release`和`Sync.tryRelease`方法。

`tryRelease`语义很明确：如果线程多次锁定，则进行多次释放，直至status==0则真正释放锁，所谓释放锁即设置status为0，因为无竞争所以没有使用CAS。 
`release`的语义在于：如果可以释放锁，则唤醒队列第一个线程(Head)

##### 并发编程的三个主要特性

1. **原子性：**一个或者多个操作作为一个整体，要么全部执行，要么都不执行，并且操作在执行过程中不会被线程调度机制打断；而且这种操作一旦开始，就一直运行到结束，中间不会有任何上下文切换(context switch)。
2. **可见性：**指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看到修改的值。
3. **有序性：**即程序执行的顺序按照代码的先后顺序执行。

*********************

#### ==ThreadLocal==

##### `ThreadLocal`简介

​	通常情况下，我们创建的变量是可以被任何一个线程访问并修改的。如果**想实现每一个线程都有自己的专属本地变量**该如何解决呢？JDK中提供的`ThreadLocal `类正是为了解决这样的问题。

​	**`ThreadLocal `类主要解决的就是让每个线程绑定自己的值，可以将` ThreadLocal`类形象的比喻成存放数据的盒子，盒子中可以存储每个线程的私有数据。**

​	**如果你创建了一个`ThreadLocal`变量，那么访问这个变量的每个线程都会有这个变量的本地副本，这也是`ThreadLocal`变量名的由来。他们可以使用`get()`和`set()`方法来获取默认值或将其值更改为当前线程所存的副本的值，从而避免了线程安全问题。**每个线程的本地变量不是存放在 `ThreadLocal` 实例中，而是放在调用线程的 `ThreadLocals` 变量里面(前面也说过，该变量是Thread类的变量)。

**应用场景：**

```markdown
1. 保存线程上下文信息，在需要的地方可以获取
2. 线程间数据隔离
3. 数据库连接;
```

##### `ThreadLocal`原理

![image-20210410173145086](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410173145086.png)

​	从上面`Thread`类源代码可以看出`Thread`类中有一个`threadLocals `和一个`inheritableThreadLocals `变量，它们都是`ThreadLocalMap`类型的变量。`ThreadLocal` 的核心数据结构是 `ThreadLocalMap`，我们可以把`ThreadLocalMap`理解为`ThreadLocal`类实现的定制化的`HashMap`。默认情况下这两个变量都是`null`，只有当前线程调用`ThreadLocal `类的`set` 或`get `方法时才创建它们，实际上调用这两个方法的时候，我们调用的是`ThreadLocalMap`类对应的`get()`、`set()`方法。

​	`ThreadLocalMap`数据结构：

```java
static class ThreadLocalMap {
  
  // 这里的entry继承WeakReference了
  static class Entry extends WeakReference<ThreadLocal<?>> {
      Object value;
      Entry(ThreadLocal<?> k, Object v) {
          super(k);
          value = v;
      }
  }
 
  // 初始化容量,必须是2的n次方
  private static final int INITIAL_CAPACITY = 16;
 
  // entry数组,用于存储数据
  private Entry[] table;
 
  // map的容量
  private int size = 0;
 
  // 数据量达到多少进行扩容,默认是 table.length * 2 / 3
  private int threshold;
}
```

###### **`ThreadLocal`类的`set()`方法**

```java
public void set(T value) {
    //(1)获取当前线程(调用者线程)
    Thread t = Thread.currentThread();
    //(2)以当前线程作为key值，去查找对应的线程变量，找到对应的map
    ThreadLocalMap map = getMap(t);
    //(3)如果map不为null，就直接添加本地变量，key为当前定义的ThreadLocal变量的this引用，值为添加的本地变量值
    if (map != null)
        map.set(this, value);
    //(4)如果map为null，说明首次添加，需要首先创建出对应的map
    else
        createMap(t, value);
}

ThreadLocalMap getMap(Thread t) {
    //获取线程自己的变量threadLocals，并绑定到当前调用线程的成员变量threadLocals上
    return t.threadLocals; 
}

void createMap(Thread t, T firstValue) {
    // createMap方法不仅创建了threadLocals，同时也将要添加的本地变量值添加到了threadLocals中
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```

**最终的变量是放在了当前线程的`ThreadLocalMap `中，并不是存在 `ThreadLocal `上，` ThreadLocal` 可以理解为只是`ThreadLocalMap `的封装，传递了变量值。**

​	`ThrealLocal`类中可以通过`Thread.currentThread()`获取到当前线程对象后，直接通过`getMap(Thread t)`可以访问到该线程的 `ThreadLocalMap`对象。

###### **`ThreadLocal`类的`get()`方法**

```java
public T get() {
    //(1)获取当前线程
    Thread t = Thread.currentThread();
    //(2)获取当前线程的threadLocals变量
    ThreadLocalMap map = getMap(t);
    //(3)如果threadLocals变量不为null，就可以在map中查找到本地变量的值
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    //(4)执行到此处，threadLocals为null，调用该更改初始化当前线程的threadLocals变量
    return setInitialValue();
}

private T setInitialValue() {
    //protected T initialValue() {return null;}
    T value = initialValue();
    //获取当前线程
    Thread t = Thread.currentThread();
    //以当前线程作为key值，去查找对应的线程变量，找到对应的map
    ThreadLocalMap map = getMap(t);
    //如果map不为null，就直接添加本地变量，key为当前线程，值为添加的本地变量值
    if (map != null)
        map.set(this, value);
    //如果map为null，说明首次添加，需要首先创建出对应的map
    else
        createMap(t, value);
    return value;
}
```

**`ThreadLocal`类的`remove()`方法**

`remove()` 方法判断该当前线程对应的 `threadLocals` 变量是否为null，不为 `null` 就直接删除当前线程中指定的 `threadLocals` 变量。

```java
public void remove() {
    //获取当前线程绑定的threadLocals
     ThreadLocalMap m = getMap(Thread.currentThread());
     //如果map不为null，就移除当前线程中指定ThreadLocal实例的本地变量
     if (m != null)
         m.remove(this);
 }
```



​	**`ThreadLocal` 内部维护的是一个类似`Map`的`ThreadLocalMap `数据结构，`key`为当前对象的`Thread`对象，值为`Object`对象。**

![image-20210410174830563](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410174830563.png)

​		比如我们在同一个线程中声明了两个`ThreadLocal`对象的话，会使用`Thread`内部都是使用仅有那个`ThreadLocalMap`存放数据的，`ThreadLocalMap`的`key`就是`ThreadLocal`对象，`value`就是`ThreadLocal `对象调用`set`方法设置的值。



从下面的结构图，我们已经窥见 `ThreadLocal` 的核心机制：

- 每个 Thread 线程内部都有一个Map。
- Map里面存储线程本地对象(key)和线程的变量副本(value)
- 但是，Thread内部的Map是由 `ThreadLocal` 维护的，由 `ThreadLocal` 负责向map获取和设置线程的变量值。

所以对于不同的线程，每次获取副本值时，别的线程并不能获取到当前线程的副本值，形成了副本的隔离，互不干扰。

`ThreadLocalMap` 是 `ThreadLocal` 的内部类，没有实现Map接口，用独立的方式实现了Map的功能，其内部的Entry也独立实现。

在 `ThreadLocalMap` 中，也是用 `Entry` 来保存K-V结构数据的。但是 `Entry` 中key只能是 `ThreadLocal` 对象，这点被Entry的构造方法已经限定死了。

![image-20210410175108037](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410175108037.png)

![image-20210410175125815](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410175125815.png)


##### `ThreadLocal` 内存泄露问题

​		**`ThreadLocalMap` 中使用的`key`为`ThreadLocal`的弱引用，而`value`是强引用**。所以，如果`ThreadLocal` 没有被外部强引用的情况下，在垃圾回收的时候，`key`会被清理掉，而`value`不会被清理掉。这样一来，`ThreadLocalMap`中就会出现`key`为`null`的`Entry`。假如我们不做任何措施的话，`value`永远无法被GC回收，这个时候就可能会产生内存泄露。`ThreadLocalMap`实现中已经考虑了这种情况，在调用`set()、get()、remove()`方法的时候，会清理掉`key`为`null`的记录。使用完`ThreadLocal `方法后最好手动调用`remove()`方法。

```java
public void remove() {
    ThreadLocalMap m = getMap(Thread.currentThread());
    if (m != null) {
        m.remove(this);
    }
}
ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}
```

弱引用介绍：

> 如果一个对象只具有弱引用，那就类似于可有可无的生活用品。**弱引用与软引用的区别在于：只具有弱引用的对象拥有更短暂的生命周期。**在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。不过，由于垃圾回收器是一个优先级很低的线程，因此不一定会很快发现那些只具有弱引用的对象。
> 弱引用可以和一个引用队列(Reference Queue)联合使用，如果弱引用所引用的对象被垃圾回收，Java虚拟机就会把这个弱引用加入到与之关联的引用队列中。

##### 为什么使用弱引用？

- key 使用强引用：引用 `ThreadLocal` 的对象被回收了，但是 `ThreadLocalMap` 还持有 `ThreadLocal` 的强引用，如果没有手动删除，`ThreadLocal` 不会被回收，导致内存泄漏。
- key 使用弱引用：引用的 `ThreadLocal` 的对象被回收了，由于 `ThreadLocalMap` 持有 `ThreadLocal` 的弱引用，即使没有手动删除，`ThreadLocal` 也会被回收。value在下一次 `ThreadLocalMap` 调用 `set`, `get`, `remove`的时候会被清除。
- 比较两种情况，我们可以发现：由于 `ThreadLocalMap` 的生命周期跟 `Thread` 一样长，如果都没有手动删除对应key，都会导致内存泄漏，但是使用弱引用可以多一层保障：弱引用 `ThreadLocal` 不会内存泄漏，对应的value在下一次`ThreadLocalMap` 调用 `set`, `get`, `remove`的时候会被清除。

##### `ThreadLocalMap` Hash冲突怎么解决

**`ThreadLocalMap` 中解决Hash冲突的方式并非链表的方式，而是采用线性探测的方式**。

`ThreadLocalMap` 解决Hash冲突的方式就是简单的步长加1或减1，寻找下一个相邻的位置。

```java
/**
 * Increment i modulo len.
 */
private static int nextIndex(int i, int len) {
    return ((i + 1 < len) ? i + 1 : 0);
}
 
/**
 * Decrement i modulo len.
 */
private static int prevIndex(int i, int len) {
    return ((i - 1 >= 0) ? i - 1 : len - 1);
}
```

显然 `ThreadLocalMap` 采用线性探测的方式解决 `Hash` 冲突的效率很低，如果有大量不同的 `ThreadLocal` 对象放入map中时发送冲突，或者发生二次冲突，则效率很低。

**所以这里引出的良好建议是：每个线程只存一个变量，这样的话所有的线程存放到map中的Key都是相同的 `ThreadLocal`，如果一个线程要保存多个变量，就需要创建多个 `ThreadLocal` ，多个 `ThreadLocal` 放入Map中时会极大的增加Hash冲突的可能。**

`ThreadLocalMap` 的 `set` 方法：

- 采用线性探测法,寻找合适的插入位置。首先判断key是否存在,存在则直接覆盖。如果key不存在证明被垃圾回收了此时需要用新的元素替换旧的元素;
- 不存在对应的元素,需要创建一个新的元素;
- 清除entry不为空，但是 `ThreadLocal` (entry的key被回收了)的元素，防止内存泄露;
- 如果满足条件: `size >= threshold - threshold / 4` 就将数组扩大为之前的两倍，同时会重新计算数组元素所处的位置并进行移动(rehash)。比如最开始数组初始大小为16,当size >= (16*2/3=10) - (10/4) = 8的时候就会扩容,将数组大小扩容至32.

```java
private void set(ThreadLocal<?> key, Object value) {
 
  Entry[] tab = table;
  int len = tab.length;
  int i = key.threadLocalHashCode & (len-1);
 
  // 采用线性探测,寻找合适的插入位置
  for (Entry e = tab[i]; e != null; e = tab[i = nextIndex(i, len)]) {
      ThreadLocal<?> k = e.get();
      // key存在则直接覆盖
      if (k == key) {
          e.value = value;
          return;
      }
      // key不存在,说明之前的ThreadLocal对象被回收了
      if (k == null) {
          replaceStaleEntry(key, value, i);
          return;
      }
  }
 
  // 不存在也没有旧元素,就创建一个
  tab[i] = new Entry(key, value);
  int sz = ++size;
  // 清除旧的槽(entry不为空，但是ThreadLocal为空)，并且当数组中元素大于阈值就rehash
  if (!cleanSomeSlots(i, sz) && sz >= threshold)
    expungeStaleEntries();
    // 扩容
    if (size >= threshold - threshold / 4)
      resize();
}
```



*********************

#### ==线程池==

##### 为什么使用线程池

> 池化技术已经屡见不鲜了，线程池、数据库连接池、Http连接池等等都是对这个思想的应用。
>
> 池化技术的思想主要是为了减少每次获取资源的消耗，提高对资源的利用率。
>
> **线程池适合单系统的大量的异步任务处理，比如发送短信、保存日志。**

**线程池**提供了一种限制和管理资源(包括执行一个任务)。每个线程池还维护一些基本统计信息，例如已完成任务的数量。

###### 使用线程池的好处

- **降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度**。当任务到达时，任务可以不需要的等到线程创建就能立即执行。
- **提高线程的可管理性。**线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/thread-pool.png)

##### ==线程池三大方法==

```java
Executors.newSingleThreadExecutor();// 创建单个线程
Executors.newFixedThreadPool(5);// 创建固定数量大小的线程池
Executors.newCachedThreadPool();// 创建大小可伸缩的线程池
Executors.ScheduledThreadPoolExecutor(); //创建一个定长线程池,支持定时及周期性任务执行
```

![image-20210815103427057](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210815103427057.png)

![image-20210815103706742](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210815103706742.png)

![image-20210815103835756](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210815103835756.png)

![image-20211013173726430](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20211013173726430.png)

##### 阻塞队列 `BlockingQueue`

> - 队列：    FIFO  先入先出
> - 阻塞：
>   - 向队列写入的时候，如果队列满了，就必须阻塞等待；
>   - 如果队列是空的，必须阻塞等待。

###### **无界队列**

队列大小无限制，常用的为无界的**`LinkedBlockingQueue`(队列容量为 Integer.MAX_VALUE)**，使用该队列做为阻塞队列时要尤其当心，当任务耗时较长时可能会导致**大量新任务在队列中堆积最终导致OOM**。阅读代码发现，`Executors.newFixedThreadPool` 采用就是 `LinkedBlockingQueue`，而楼主踩到的就是这个坑，当QPS很高，发送数据很大，大量的任务被添加到这个无界`LinkedBlockingQueue` 中，导致cpu和内存飙升服务器掉。

###### **有界队列**

常用的有两类，一类是遵循FIFO原则的队列如`ArrayBlockingQueue`与无界的`LinkedBlockingQueue`，另一类是优先级队列如`PriorityBlockingQueue`。`PriorityBlockingQueue`中的优先级由任务的`Comparator`决定。 
使用有界队列时队列大小需和线程池大小互相配合，线程池较小有界队列较大时可减少内存消耗，降低cpu使用率和上下文切换，但是可能会限制系统吞吐量。

###### **同步移交队列**

如果不希望任务在队列中等待而是希望将任务直接移交给工作线程，可使用`SynchronousQueue`作为等待队列。`SynchronousQueue`不是一个真正的队列，而是一种线程之间移交的机制。要将一个元素放入`SynchronousQueue`中，必须有另一个线程正在等待接收这个元素。只有在使用无界线程池或者有饱和策略时才建议使用该队列。

> `SynchronousQueue` 同步队列
>
> 和其他的`BlockingQueue`不一样。
>
> **容量为1， 进去一个元素，必须等待取出来之后，才能再往里面放一个元素！**
>
> 只有 put()、take()两个方法

##### ==线程池的参数==

![image-20210427203355559](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210427203355559.png)

线程池的构造函数有7个参数，分别是`corePoolSize`、`maximumPoolSize`、`keepAliveTime`、`unit`、`workQueue`、`threadFactory`、`handler`。

1. `corePoolSize` 线程池核心线程大小。		

   > 线程池中会维护一个最小的线程数量，即使这些线程处于空闲状态，他们也不会被销毁，除非设置了`allowCoreThreadTimeOut`。这里的最小线程数量即是`corePoolSize`。

2. `maximumPoolSize `线程池最大线程数量。  	

   > 一个任务被提交到线程池以后，首先会找有没有空闲存活线程，如果有则直接将任务交给这个空闲线程来执行，如果没有则会缓存到工作队列(后面会介绍)中，如果工作队列满了，才会创建一个新线程，然后从工作队列的头部取出一个任务交由新线程来处理，而将刚提交的任务放入工作队列尾部。线程池不会无限制的去创建新线程，它会有一个最大线程数量的限制，这个数量即由`maximunPoolSize`指定。
   >
   > ==任务提交时，判断的顺序为 `corePoolSize` –> `workQueue` –> `maximumPoolSize`。==

3. `keepAliveTime` 空闲线程存活时间     

   > 一个线程如果处于空闲状态，并且当前的线程数量大于`corePoolSize`，那么在指定时间后，这个空闲线程会被销毁，这里的指定时间由`keepAliveTime`来设定。

4. unit 空闲线程存活时间单位 

   > `keepAliveTime`的计量单位

5. `workQueue `工作队列

   > 新任务被提交后，会先进入到此工作队列中，任务调度时再从队列中取出任务。jdk中提供了四种工作队列：
   >
   > ①`ArrayBlockingQueue`
   >
   > 基于数组的有界阻塞队列，按FIFO排序。新任务进来后，会放到该队列的队尾，有界的数组可以防止资源耗尽问题。当线程池中线程数量达到`corePoolSize`后，再有新任务进来，则会将任务放入该队列的队尾，等待被调度。如果队列已经是满的，则创建一个新线程，如果线程数量已经达到`maxPoolSize`，则会执行拒绝策略。
   >
   > ②`LinkedBlockingQuene`
   >
   > 基于链表的无界阻塞队列(其实最大容量为`Interger.MAX`)，按照FIFO排序。由于该队列的近似无界性，当线程池中线程数量达到`corePoolSize`后，再有新任务进来，会一直存入该队列，而不会去创建新线程直到`maxPoolSize`，因此使用该工作队列时，参数`maxPoolSize`其实是不起作用的。
   >
   > ③`SynchronousQuene`
   >
   > 一个不缓存任务的阻塞队列，生产者放入一个任务必须等到消费者取出这个任务。也就是说新任务进来时，不会缓存，而是直接被调度执行该任务，如果没有可用线程，则创建新线程，如果线程数量达到`maxPoolSize`，则执行拒绝策略。
   >
   > ④`PriorityBlockingQueue`
   >
   > 具有优先级的无界阻塞队列，优先级通过参数`Comparator`实现。

6. `threadFactory `线程工厂

   > 每当线程池创建一个新的线程时，都是通过线程工厂方法来完成的。在 ThreadFactory 中只定义了一个方法 newThread，每当线程池需要创建新线程就会调用它。
   >
   > ```java
   > public class MyThreadFactory implements ThreadFactory {
   >     private final String poolName;
   > 
   >     public MyThreadFactory(String poolName) {
   >         this.poolName = poolName;
   >     }
   > 
   >     public Thread newThread(Runnable runnable) {
   >         return new MyAppThread(runnable, poolName);//将线程池名字传递给构造函数，用于区分不同线程池的线程
   >     }
   > }
   > ```

7. `RejectedExecutionHandler` 拒绝策略

   > 当工作队列中的任务已到达最大限制，并且线程池中的线程数量也达到最大限制，这时如果有新任务提交进来，该如何处理呢。这里的拒绝策略，就是解决这个问题的，jdk中提供了4种拒绝策略:
   >
   > ①`CallerRunsPolicy`
   >
   > 该策略下，在调用者线程中直接执行被拒绝任务的run方法，除非线程池已经shutdown，则直接抛弃任务。
   >
   > ②`AbortPolicy`
   >
   > **该策略下，直接丢弃任务，并抛出`RejectedExecutionException`异常。默认。**
   >
   > ③`DiscardPolicy`
   >
   > 该策略下，直接丢弃任务，什么都不做。
   >
   > ④`DiscardOldestPolicy`
   >
   > 该策略下，抛弃进入队列最早的那个任务，然后尝试把这次拒绝的任务放入队列。
   >
   > ```java
   > /** 
   >  * new ThreadPoolExecutor.AbortPolicy()
   >  *      线程数量超过最大承载量(阻塞队列的capacity + maximumPoolSize)以后，抛出异常
   >  * new ThreadPoolExecutor.CallerRunsPolicy()
   >  *      线程数量超过最大承载量(阻塞队列的capacity + maximumPoolSize)以后，由调用者所在的线程来执行任务
   >  * new ThreadPoolExecutor.DiscardPolicy()
   >  *      队列满了就丢掉任务，不抛出异常
   >  * new ThreadPoolExecutor.DiscardOldestPolicy()
   >  *      队列满了，尝试去和最早的竞争，不会抛出异常
   >  */
   > ```

##### 线程池生命周期

> ```java
> *   RUNNING:  Accept new tasks and process queued tasks
> *   SHUTDOWN: Don't accept new tasks, but process queued tasks
> *   STOP:     Don't accept new tasks, don't process queued tasks,
> *             and interrupt in-progress tasks
> *   TIDYING:  All tasks have terminated, workerCount is zero,
> *             the thread transitioning to state TIDYING
> *             will run the terminated() hook method
> *   TERMINATED: terminated() has completed
> ```

- `RUNNING:` 接收新的任务并处理队列中的任务
- `SHUTDOWN:` 不接收新的任务，但是处理队列中的任务
- `STOP: `不接收新的任务，不处理队列中的任务，同时中断处理中的任务
- `TIDYING: `所有的任务处理完成，有效的线程数是0
- `TERMINATED:  `terminated()方法执行完毕

##### 线程池状态如何改变

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/bVbj3Gx)

1、调用 `shutdown()` 方法线程池的状态由 `RUNNING——>SHUTDOWN`

2、调用 `shutdowNow()` 方法线程池的状态由 `RUNNING——>STOP` 

3、当任务队列和线程池均为空的时候线程池的状态由 `STOP/SHUTDOWN——–>TIDYING`

4、当 `terminated()` 方法被调用完成之后，线程池的状态由 `TIDYING———->TERMINATED` 状态

##### [如何优雅的关闭Java线程池](https://www.cnblogs.com/qingquanzi/p/9018627.html)

**在介绍线程池关闭之前，先介绍下 `Thread` 的 `interrupt`。**

> 首先强调一点：在程序中，**我们是不能随便中断一个线程的**，因为这是极其不安全的操作，我们无法知道这个线程正运行在什么状态，它可能持有某把锁，强行中断可能导致锁不能释放的问题；或者线程可能在操作数据库，强行中断导致数据不一致混乱的问题。正因此，JAVA里将Thread的stop方法设置为过时，以禁止大家使用。一个线程什么时候可以退出呢？当然只有线程自己才能知道。

**所以我们这里要说的 `Thread` 的 `interrrupt` 方法，本质不是用来中断一个线程。是将线程设置一个中断状态。**

当我们调用线程的interrupt方法，它有两个作用：

1、如果此线程处于阻塞状态(比如调用了wait方法，io等待)，则会立马退出阻塞，并抛出 `InterruptedException` 异常，线程就可以通过捕获 `InterruptedException` 来做一定的处理，然后让线程退出。

2、如果此线程正处于运行之中，则线程不受任何影响，继续运行，仅仅是线程的中断标记被设置为true。所以线程要在适当的位置通过调用 `isInterrupted` 方法来查看自己是否被中断，并做退出操作。

**注：**

如果线程的interrupt方法先被调用，然后线程调用阻塞方法进入阻塞状态，InterruptedException异常依旧会抛出。

如果线程捕获InterruptedException异常后，继续调用阻塞方法，将不再触发InterruptedException异常。



线程池提供了两个关闭方法，`shutdownNow`和 `shutdown` 方法。

`shutdownNow()` 方法的解释是：线程池拒接收新提交的任务，同时立马关闭线程池，线程池里的任务不再执行。

`shutdown()` 方法的解释是：线程池拒接收新提交的任务，同时等待线程池里的任务执行完毕后关闭线程池。

需要强调一点的是，调用完 `shutdownNow` 和 `shuwdown` 方法后，并不代表线程池已经完成关闭操作，它只是异步的通知线程池进行关闭处理。如果要同步等待线程池彻底关闭后才继续往下执行，需要调用 `awaitTermination` 方法进行同步等待。

###### `shutdownNow`

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1287675-20180510110620835-1148297954.png)

在 `shutdownNow` 方法里，重要的三句代码我用红色数字标出来了。

第一句就是原子性的修改线程池的状态为STOP状态;

第三句是将队列里还没有执行的任务放到列表里，返回给调用方。

第二句是遍历线程池里的所有工作线程，然后调用线程的interrupt方法。如下图：

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1287675-20180510110637779-1551415768.png)

**以上就是 `shutdownNow `方法的执行逻辑：**将线程池状态修改为STOP，然后调用线程池里的所有线程的 `interrupt` 方法。

调用 `shutdownNow` 后，线程池里的线程会做如何反应呢？那就要看，线程池里线程正在执行的代码逻辑了。其在线程池的`runWorker` 方法里(对线程池的执行原理不了解的，请看之前的文章)，其代码如下：

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1287675-20180510110657079-796827446.png)

正常情况下，线程池里的线程，就是在这个while循环里不停地执行。其中代码 `task.run()` 就是在执行我们提交给线程池的任务，**如当我们调用 `shutdownNow` 时，`task.run()` 里面正处于IO阻塞，则会导致报错，如果 `task.run()` 里正在正常执行，则不受影响，继续执行完这个任务。**

从上图看的出来，如果 `getTask()` 方法返回 `null` ,也会导致线程的退出。我们再来看看 `getTask` 方法的实现：

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1287675-20180510110717452-1932932739.png)

如果我们调用 `shutdownNow` 方法时，线程处于从队列里读取任务而阻塞中(图中下边的红框)，则会导致抛出`InterruptedException` 异常，但因为异常被捕获，线程将会继续在这个for循环里执行。

还记得 `shutdownNow` 方法里将线程修改为STOP状态吧，当执行到上边红框里的代码时，由于STOP状态值是大于SHUTDOWN状态，STOP也大于等于STOP，不管任务队列是否为空，都会进入if语句从而返回null,线程退出。

###### 总结：

> 当我们调用线程池的 `shutdownNow` 时，
>
> 如果线程正在 `getTask` 方法中执行，则会通过for循环进入到if语句，于是` getTask` 返回null,从而线程退出。不管线程池里是否有未完成的任务。
>
> 如果线程因为执行提交到线程池里的任务而处于阻塞状态，则会导致报错(如果任务里没有捕获 `InterruptedException` 异常)，否则线程会执行完当前任务，然后通过 `getTask` 方法返回为null来退出。

###### `shutdown`

源码：

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1287675-20180510110753743-529313580.png)

跟 `shutdownNow` 类似，只不过它是将线程池的状态修改为 `SHUTDOWN` 状态，然后调用 `interruptIdleWorkers` 方法，来中断空闲的线程。这是 `interruptIdleWorkers` 方法的实现：

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1287675-20180510110813239-1768041644.png)

跟 `shutdownNow` 方法调用 `interruptWorkers` 方法不同的是，`interruptIdleWorkers` 方法在遍历线程池里的线程时，有一个 `w.tryLock()` 加锁判断，只有加锁成功的线程才会被调用 `interrupt` 方法。那什么情况下才能被加锁成功?什么情况下不能被加锁成功呢？这就需要我们继续回到线程执行的 `runWorker` 方法。

在上边 `runWorker` 方法代码的截图中，我刻意将 `w.lock()` 和 `w.unlock()`调用用红框圈起。其实就是正运行在`w.lock`和 `w.unlock` 之间的线程将因为加锁失败，而不会被调用interrupt方法，换句话说，就是正在执行线程池里任务的线程不会被中断。

不管是被调用了interrupt的线程还是没被调用的线程，什么时候退出呢？，这就要看 `getTask` 方法的返回是否为null了。

在 `getTask` 里的if判断(上文中 `getTask` 代码截图中上边红色方框的代码)中，由于线程池被 `shutdown` 方法修改为SHUTDOWN状态，SHUTDOWN大于等于SHUTDOWN成立没问题，但是SHUTDOWN不在大于等于STOP状态，所以只有队列为空，`getTask` 方法才会返回null，导致线程退出。

###### 总结：

当我们调用线程池的 `shuwdown` 方法时，

如果线程正在执行线程池里的任务，即便任务处于阻塞状态，线程也不会被中断，而是继续执行。

如果线程池阻塞等待从队列里读取任务，则会被唤醒，但是会继续判断队列是否为空，如果不为空会继续从队列里读取任务，为空则线程退出。

##### 总的总结

我们知道，使用 `shutdownNow` 方法，可能会引起报错，使用 `shutdown` 方法可能会导致线程关闭不了。

- 所以当我们使用 `shutdownNow` 方法关闭线程池时，一定要对任务里进行异常捕获。
- 当我们使用 `shuwdown` 方法关闭线程池时，一定要确保任务里不会有永久阻塞等待的逻辑，否则线程池就关闭不了。
- 最后，一定要记得，`shutdownNow` 和 `shuwdown` 调用完，线程池并不是立马就关闭了，要想等待线程池关闭，还需调用`awaitTermination` 方法来阻塞等待。



##### 实现Runable接口和Callable接口区别

​		`Runnable `自Java 1.0以来一直存在，但`Callable` 仅在Java1.5中引入，目的就是为了来处理`Runnable `不支持的用例。**`Runnable `接口不会返回结果或抛出检查异常，但是`Callable `接口可以。**所以，如果任务不需要返回结果或抛出异常推荐使用`Runnable `接口，这样代码看起来会更加简洁。

​		工具类`Executors `可以实现`Runnable`对象和`Callable `对象之间的相互转换。`(Executors.callable(Runnable task)`或`Executors.callable(Runnable task，Object resule))`。

`Runable.java`

![image-20210411091851795](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411091851795.png)

`Callable.java`

![image-20210411091912366](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411091912366.png)

##### 执行execute()和submit()方法的区别是什么呢？

- `execute()`方法用于提交不需要返回值的任务，所以无法判断任务是否被线程池执行成功与否；
- `submit()`方法用于提交需要返回值的任务。线程池会返回一个` Future`类型的对象，通过这个`Future `对象可以判断任务是否执行成功，并且可以通过`Future`的`get()`方法来获取返回值，`get()`方法会阻塞当前线程直到任务完成，而使用`get(long timeout，TimeUnit unit)`方法则会阻塞当前线程一段时间后立即返回，这时候有可能任务没有执行完。

![image-20210922224636974](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210922224636974.png)

![image-20210411092152470](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411092152470.png)

![image-20210411092214067](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411092214067.png)

##### 线程池如何创建线程？

==不允许使用`Executors`去创建，而是通过`ThreadPoolExecutor`的方式。==

`Executors`返回线程池对象的弊端

> - **`FixedThreadPool`和`Single ThreadExecutor`：**允许请求的队列长度为`Integer.MAX_VALUE`，可能堆积大量的请求，从而导致OOM。
> - **`CachedThreadPool `和`ScheduledThreadPool`：**允许创建的线程数量为`Integer.MAX_VALUE`，可能会创建大量线程，从而导致OOM。

###### 方法一：通过构造方法实现

![image-20210411092900589](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411092900589.png)

###### 方法二：通过Executor框架的工具类Executors来实现

我们可以创建三种类型的`ThreadPoolExecutor`：

- **`FixedThreadPool`：**该方法返回一个固定线程数量的线程池。该线程池中的线程数量始终不变。当有一个新的任务提交时，线程池中若有空闲线程，则立即执行。若没有，则新的任务会被暂存在一个任务队列中，待有线程空闲时，便处理在任务队列中的任务。
- **`SingleThreadExecutor`：**方法返回一个只有一个线程的线程池。若多余一个任务被提交到该线程池，任务会被保存在一个任务队列中，待线程空闲，按先入先出的顺序执行队列中的任务。
- **`CachedThreadPool`：**该方法返回一个可根据实际情况调整线程数量的线程池。线程池的线程数量不确定，但若有空闲线程可以复用，则会优先使用可复用的线程。若所有线程均在工作，又有新的任务提交，则会创建新的线程处理任务。所有线程在当前任务执行完毕后，将返回线程池进行复用。

![image-20210411093324000](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411093324000.png)

##### `ThreadPoolExecutor`类

`ThreadPoolExecutor` 类中提供的四个构造方法。我们来看最长的那个，其余三个都是在这个构造方法的基础上产生(其他几个构造方法说白点都是给定某些默认参数的构造方法比如默认制定拒绝策略是什么)。

![image-20210411093527404](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411093527404.png)

###### `ThreadPoolExecutor`构造函数重要参数

**3个重要的参数**

- ==`corePoolSize：`核心线程数线程数定义了**最小可以同时运行**的线程数量.==
- ==`maximumPoolSize`：当队列中存放的任务达到队列容量的时候，当前可以同时运行的线程数量变为最大线程数。==
- ==`workQueue：`当新任务来的时候会先判断当前运行的线程数量是否达到核心线程数，如果达到的话，新任务就会被存放在队列中。==

`ThreadPoolExecutor `其他常见参数：

- `keepAliveTime`：当线程池中的线程数量大于`corePoolSize`的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了`keepAliveTime `才会被回收销毁；
- `unit:keepAliveTime`参数的时间单位。
- `threadFactory:executor`创建新线程的时候会用到。
- `handler`：饱和策略。关于饱和策略下面单独介绍一下。

###### `ThreadPoolExecutor `饱和策略

如果当前同时运行的线程数量达到最大线程数量并且队列也已经被放满了任务时，`ThreadPoolTaskExecutor `定义一些策略：

- `ThreadPoolExecutor.AbortPolicy`：抛出`RejectedExecutionException `来拒绝新任务的处理。
- `ThreadPoolExecutor.CallerRunsPolicy`：调用执行自己的线程运行任务。您不会任务请求。但是这种策略会降低对于新任务提交速度，影响程序的整体性能。另外，这个策略喜欢增加队列容量。如果您的应用程序可以承受此延迟并且你不能任务丢弃任何一个任务请求的话，你可以选择这个策略。
- `ThreadPoolExecutor.DiscardPolicy`：不处理新任务，直接丢弃掉。
- `ThreadPoolExecutor.DiscardOldestPolicy`：此策略将丢弃最早的未处理的任务请求。

举个例子：`Spring`通过`ThreadPoolTaskExecutor `或者我们直接通过`ThreadPoolExecutor`的构造函数创建线程池的时候，当我们不指定`RejectedExecutionHandler`饱和策略的话来配置线程池的时候默认使用的是`ThreadPoolExecutor.AbortPolicy`。在默认情况下，`ThreadPoolExecutor `将抛出`RejectedExecutionException` 来拒绝新来的任务，这代表你将丢失对这个任务的处理。对于可伸缩的应用程序，建议使用`ThreadPoolExecutor.CallerRunsPolicy`。当最大池被填满时，此策略为我们提供可伸缩队列。

##### 线程池原理：线程组+阻塞队列

> - 队列：    FIFO  先入先出
> - 阻塞：
>   - 向队列写入的时候，如果队列满了，就必须阻塞等待；
>   - 从队列读取的时候，如果队列是空的，必须阻塞等待； 

**为了搞懂线程池的原理，我们需要⾸先分析⼀下 `execute `⽅法。**

使用`executor.execute(worker)`来提交一个任务到线程池中去，这个方法非常重要，下面我们来看看它的源码：

![image-20210411094329793](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411094329793.png)

![image-20210411094340216](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411094340216.png)

![image-20210411094402022](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411094402022.png)



##### 现在有一个项目，需要用到线程池，你会怎么设计呢？

- 首先要确认这个任务的类型：CPU密集型还是IO密集型。如果是前者，开辟线程池并不能带来多少的性能提升，因为CPU密集型的运算通过多线程并不能提高多少性能；如果是后者，那么线程池的真正作用就完全发挥出来了，将会带给你飞一般的体验。

  - ```markdown
    **
    1. CPU密集型任务的特点是要进行大量的计算，消耗CPU资源，比如计算圆周率、对视频进行高清解码等等，全靠CPU的运算能力。这种计算密集型任务虽然也可以用多任务完成，但是任务越多，花在任务切换的时间就越多，CPU执行任务的效率就越低，所以，要最高效地利用CPU，计算密集型任务同时进行的数量应当等于CPU的核心数
    2. IO是指信息处理系统(比如计算机)和外部世界(可以是人或其他信息处理系统)的通信。输入是指系统接收的信号或数据，输出是指从系统发出的数据或信号。
      > 涉及到网络、磁盘IO的任务都是IO密集型任务，这类任务的特点是CPU消耗很少，任务的大部分时间都在等待IO操作完成(因为IO的速度远远低于CPU和内存的速度)。对于IO密集型任务，任务越多，CPU效率越高，但也有一个限度。常见的大部分任务都是IO密集型任务，比如Web应用。
    **
    ```
    
  - **CPU 密集型任务(N+1)**： 这种任务消耗的主要是 CPU 资源，可以将线程数设置为 N（CPU 核心数）+1，比 CPU 核心数多出来的一个线程是为了防止某些原因导致的任务暂停（线程阻塞，如io操作，等待锁，线程sleep）而带来的影响。一旦某个线程被阻塞，释放了cpu资源，而在这种情况下多出来的一个线程就可以充分利用 CPU 的空闲时间。

  - **I/O 密集型任务(2N)**： 系统会用大部分的时间来处理 I/O 操作，而线程等待 I/O 操作会被阻塞，释放 cpu资源，这时就可以将 CPU 交出给其它线程使用。因此在 I/O 密集型任务的应用中，我们可以多配置一些线程，具体的计算方法：最佳线程数 = CPU核心数 * (1/CPU利用率) = CPU核心数 * (1 + (I/O耗时/CPU耗时))，一般可设置为2N。

![image-20210824081430936](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210824081430936.png)

**************

#### Atomic原子类

`Atomic`

> 指一个操作是不可中断的。即使多个线程一起执行的时候，一个操作一旦开始，就不会被其他线程干扰。
>
> 所以，所谓原子类，简单来说就是具有原子/原子操作特征的类。

并发包` java.util.concurrent` 的原子类都放在`java.util.concurrent.atomic `下：

![image-20210411094716105](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411094716105.png)

##### JUC包中的原子类是哪4类

```markdown
1. 基本类型 
	1.1 AtomicInteger：整型原子类
	1.2 AtomicLong：长整型原子类
	1.3 AtomicBoolean ：布尔型原子类

2. 数组类型
	2.1 AtomicIntegerArray：整型数组原子类
		- 常用方法
		public final int get(int i) //获取 index=i 位置元素的值
        public final int getAndSet(int i, int newValue)//返回 index=i 位置的当前的值，并将其设置为新值：newValue
        public final int getAndIncrement(int i) //获取 index=i 位置元素的值，并让该位置的元素自增
        public final int getAndDecrement(int i) //获取 index=i 位置元素的值，并让该位置的元素自减
        public final int getAndAdd(int i, int delta) //获取 index=i 位置元素的值，并加上预期的值
        boolean compareAndSet(int i, int expect, int update) //如果输入的数值等于预期值，则以原子方式将 index=i 位置的元素值设置为输入值（update）
        public final void lazySet(int i, int newValue)//最终 将index=i 位置的元素设置为newValue,使用 lazySet 设置之后可能导致其他线程在之后的一小段时间内还是可以读到旧的值。
	2.2 AtomicLongArray：长整型数组原子类
	2.3 AtomicReferenceArray ：引用类型数组原子类

3. 引用类型
	3.1 AtomicReference：引用类型原子类
	3.2 AtomicReferenceFieldUpdater：原子更新引用类型里的字段
	3.3 AtomicMarkableReference ：原子更新带有标记位的引用类型
	
4. 对象的属性修改类型
	4.1 AtomicIntegerFieldUpdater:原子更新整型字段的更新器
	4.2 AtomicLongFieldUpdater:原子更新长整型字段的更新器
	4.3 AtomicStampedReference:原子更新带有版本号的引用类型。该类将整数值与引用关联起来，可用于解决原子的更新数据和数据的版本号，可以解决使用 CAS 进行原子更新时可能出现的 ABA 问题。
	4.4 AtomicMarkableReference原子更新带有标记的引用类型。该类将 boolean 标记与引用关联起来，也可以解决使用 CAS 进行原子更新时可能出现的 ABA 问题。
```

#### ==CAS==

> #### CAS(Compare and Swap)
>
> > 比较和替换；**无锁操作**；
> >
> > **自旋锁或者乐观锁的核心操作**。
> >
> > ==不会产生死锁==
>
> 它的实现很简单，就是用一个旧的预期的值和内存值进行比较，如果两个值相等，就用新的值替换内存值，并返回 `true`。否则，返回 `false`。
>
> ```java
> /**
>      * Atomically sets the value to the given updated value
>      * if the current value {@code ==} the expected value.
>      *
>      * @param expect the expected value
>      * @param update the new value
>      * @return {@code true} if successful. False return indicates that
>      * the actual value was not equal to the expected value.
>      */
>     public final boolean compareAndSet(int expect, int update) {
>         return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
>     }
> ```

##### 作用

```markdown
# 解决问题：CAS 要解决的问题就是保证原子操作。

# 原子操作：原子就是最小不可拆分的，原子操作就是最小不可拆分的操作，也就是说操作一旦开始，就不能被打断，直到操作完成。在多线程环境下，原子操作是保证线程安全的重要手段。

# 举例：i++
	要对一个整数 i 进行自增操作，需要基本的三个步骤：
	1、读取 i 的当前值；
	2、对 i 值进行加 1 操作；
	3、将 i 值写回内存；
>假设两个进程都读取了 i 的当前值，假设是 0，这时候 A 线程对 i 加 1 了，B 线程也加1，最后 i 的是 1 ，而不是 2。这就是因为自增操作不是原子操作，分成的这三个步骤可以被干扰。如下面这个例子，10个线程，每个线程都执行 10000 次 i++ 操作，我们期望的值是 100,000，但是很遗憾，结果总是小于 100,000 的。
	static int i = 0;
    public static void add(){
        i++;
    }
    private static class Plus implements Runnable{
        @Override
        public void run(){
            for(int k = 0;k<10000;k++){
                add();
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException{
        Thread[] threads = new Thread[10];
        for(int i = 0;i<10;i++){
            threads[i] = new Thread(new Plus());
            threads[i].start();
        }

        for(int i = 0;i<10;i++){
            threads[i].join();
        }
        System.out.println(i);
    }
> 解决方法：加锁，比如说Synchronized或者ReentrantLock。
public synchronized static void add(){
        i++;
 }
private static Lock lock = new ReentrantLock();
    public static void add(){
        lock.lock();
        i++;
        lock.unlock();
}
```

​		既然用锁或 `synchronized `关键字可以实现原子操作，那么为什么还要用 `CAS `呢，因为加锁或使用 `synchronized `关键字带来的性能损耗较大，而用 `CAS` 可以实现乐观锁，它实际上是直接利用了 `CPU` 层面的指令，所以性能很高。

​		`CAS `是实现自旋锁的基础，**`CAS `利用 `CPU `指令保证了操作的原子性**，以达到锁的效果，至于自旋呢，看字面意思也很明白，自己旋转，翻译成人话就是循环，一般是用一个无限循环实现。这样一来，一个无限循环中，执行一个 `CAS `操作，当操作成功，返回 `true `时，循环结束；当返回 `false `时，接着执行循环，继续尝试 `CAS `操作，直到返回 `true`。

##### 底层原理

比如如原子类 `AtomicInteger`

```java
AtomicInteger.compareAndSet();
    /**
     * Atomically sets the value to {@code newValue}
     * if the current value {@code == expectedValue},
     * with memory effects as specified by {@link VarHandle#compareAndSet}.
     *
     * @param expectedValue the expected value
     * @param newValue the new value
     * @return {@code true} if successful. False return indicates that
     * the actual value was not equal to the expected value.
     */
    public final boolean compareAndSet(int expectedValue, int newValue) {
        return U.compareAndSetInt(this, VALUE, expectedValue, newValue);
    }
```

`AtomicInteger.compareAndSet()`调用了`Unsafe.compareAndSetInt()`方法 。

```java

    /**
     * Atomically updates Java variable to {@code x} if it is currently
     * holding {@code expected}.
     *
     * <p>This operation has memory semantics of a {@code volatile} read
     * and write.  Corresponds to C11 atomic_compare_exchange_strong.
     *
     * @return {@code true} if successful
     */
    @HotSpotIntrinsicCandidate
    public final native boolean compareAndSetInt(Object o, long offset,
                                                 int expected,
                                                 int x);
```

##### **缺点**

- 循环会耗时
- 一次性只能保证一个共享变量的原子性
- ABA问题

##### ABA 问题

> 因为CAS需要在操作值的时候检查下值有没有发生变化，如果没有发生变化则更新，但是如果一个值原来是A，变成了B，又变成了A，那么使用CAS进行检查时会发现它的值没有发生变化，但是实际上却变化了。
>
> ABA问题的解决思路就是==使用版本号==。在变量前面追加上版本号，每次变量更新的时候把版本号加一，那么A－B－A 就会变成1A-2B－3A。

##### CAS算法在JDK中的应用

在原子类变量中，都使用了这些底层的JVM支持为数字类型的引用类型提供一种高效的CAS操作，而在 `java.util.concurrent` 中的大多数类在实现时都直接或间接的使用了这些原子变量类。比如说：`AtomicInteger.incrementAndGet`。

#### AQS

AQS 全称是`AbstractQueuedSynchronizer `，这个类在`java.util.concurrent.locks `包下面：

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411095444277.png" alt="image-20210411095444277" style="zoom:50%;" />

​	AQS是一个用来构建锁和同步器的框架，使用AQS能简单且高效地构造出应用广泛的大量的同步器，比如我们提到的`ReentrantLock`，`Semaphore`，其他的诸如`ReentrantReadWriteLock`，`SynchronousQueue`，`FutureTask` 等等皆是基于AQS的。当然，我们自己也能利用AQS非常轻松容易地构造出符合我们自己需求的同步器。

##### AQS原理分析

![image-20210411095821650](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411095821650.png)

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411095850917.png">

当线程调用 lock 方法时 ，如果 state=0，说明没有任何线程占有共享资源的锁，可以获得锁并将 state加1。如果 state不为0，则说明有线程目前正在使用共享变量，其他线程必须加入同步队列(**`CLH` 队列**)进行等待。

> **`CLH` 队列**是一个虚拟的双向队列(虚拟的双向队列即不存在队列实例，仅存在结点之间的关联关系)。AQS是将每条请求共享资源的线程封装成一个CLH锁队列的一个结点(Node)来实现锁的分配。

![image-20210411095801818](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411095801818.png)

前线程获取同步状态失败时，同步器会将当前线程以及等待状态（独占或共享 ）构造成为一个节点（Node）并将其加入同步队列并进行自旋，当同步状态释放时，会把首节点中的后继节点对应的线程唤醒，使其再次尝试获取同步状态。



##### AQS对资源的共享方式

![image-20210411095927537](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411095927537.png)

##### AQS底层使用了模板方法模式

![image-20210411100015234](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411100015234.png)

![image-20210411100043845](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411100043845.png)

![image-20210411100112406](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411100112406.png)

##### AQS组件总结

![image-20210411100147796](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411100147796.png)

**********

#### 并发工具类

##### CountDownLatch

CountDownLatch用于某个线程等待其他线程**执行完任务**再执行，与thread.join()功能类似。常见的应用场景是开启多个线程同时执行某个任务，等到所有任务执行完再执行特定操作，如汇总统计结果。

`CountDownLatch`的作用就是允许`count`个线程阻塞在一个地方，直至所有线程的任务都执行完毕。

可以理解为==减法计数器==。

```java
CountDownLatch coutDownLatch = new CoutDownLatch(6);//减法计数器
for(int i=1;i<=6;i++){
 new Thread(()->{
		System.out.println(Thread.currentThread().getName + "go out");
     coutDownLatch.countDown();//计数器-1
 },String.valueof(i)).start();
}
coutDownLatch.await();//等待计数器减为0以后才往下执行；若不加这一行代码，下面的代码不会最后执行，显然是不对的
System.out.println("Close the Door!");
```

**应用场景举例**

​	我们要读取处理6个文件，这6个任务都是没有执行顺序依赖的任务，但是我们需要返回给用户的时候将这几个文件的处理的结果进行统计整理。为此我们定义了一个线程池和`count`为 6 的`CountDownLatch`对象。使用线程池处理读取任务，每一个线程处理完之后就将`count-1`，调用`CountDownLatch`对象的`await()`方法，直到所有文件读取完之后，才会接着执行后面的逻辑。

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411100358327.png">

![image-20210807204223420](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210807204223420.png)

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210411100421803.png">

##### CyclicBarrier

允许一组线程全部等待达到彼此共同的屏障点的同步辅助。

可以理解为：==加法计数器==

```java
public CyclicBarrier(int parties, Runnable barrierAction) {
}
public CyclicBarrier(int parties) {
}
```

参数parties指让多少个线程或者任务等待至某个状态；参数barrierAction为当这些线程都达到某个状态时会执行的内容。

```java
public class CyclicBarrierTest {
    // 请求的数量
    private static final int threadCount = 10;
    // 需要同步的线程数量
    private static final CyclicBarrier cyclicBarrier = new CyclicBarrier(5);

    public static void main(String[] args) throws InterruptedException {
        // 创建线程池
        ExecutorService threadPool = Executors.newFixedThreadPool(10);

        for (int i = 0; i < threadCount; i++) {
            final int threadNum = i;
            Thread.sleep(1000);
            threadPool.execute(() -> {
                try {
                    test(threadNum);
                } catch (InterruptedException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            });
        }
        threadPool.shutdown();
    }

    public static void test(int threadnum) throws InterruptedException, BrokenBarrierException {
        System.out.println("threadnum:" + threadnum + "is ready");
        try {
            /**等待60秒，保证子线程完全执行结束*/
            cyclicBarrier.await(60, TimeUnit.SECONDS);
        } catch (Exception e) {
            System.out.println("-----CyclicBarrierException------");
        }
        System.out.println("threadnum:" + threadnum + "is finish");
    }

}
```

运行结果如下，可以看出CyclicBarrier是可以重用的：

```java
threadnum:0is ready
threadnum:1is ready
threadnum:2is ready
threadnum:3is ready
threadnum:4is ready
threadnum:4is finish
threadnum:3is finish
threadnum:2is finish
threadnum:1is finish
threadnum:0is finish
threadnum:5is ready
threadnum:6is ready
...
```



##### Semaphore

Semaphore类似于锁，它用于控制同时访问特定资源的线程数量，控制并发线程数。

```java
public class SemaphoreDemo {
    public static void main(String[] args) {
        final int N = 7;
        Semaphore s = new Semaphore(3);
        for(int i = 0; i < N; i++) {
            new Worker(s, i).start();
        }
    }

    static class Worker extends Thread {
        private Semaphore s;
        private int num;
        public Worker(Semaphore s, int num) {
            this.s = s;
            this.num = num;
        }
		
        @Override
        public void run() {
            try {
                s.acquire();
                System.out.println("worker" + num +  " using the machine");
                Thread.sleep(1000);
                System.out.println("worker" + num +  " finished the task");
                s.release();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

运行结果如下，可以看出并非按照线程访问顺序获取资源的锁，即

```java
worker0 using the machine
worker1 using the machine
worker2 using the machine
worker2 finished the task
worker0 finished the task
worker3 using the machine
worker4 using the machine
worker1 finished the task
worker6 using the machine
worker4 finished the task
worker3 finished the task
worker6 finished the task
worker5 using the machine
worker5 finished the task
```



