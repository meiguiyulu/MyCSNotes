### Java基础

#### 0、后端知识

##### 0.1、字符编码中ASCII、Unicode和UTF-8的区别

1. ASCII

   ASCII 只有127个字符，表示英文字母的大小写、数字和一些符号，比如大写字母A的编码是65，小写字母z的编码是122。但由于其他语言用ASCII编码表示字节不够，例如：常用中文需要两个字节，且不能和ASCII冲突，中国定制了GB2312编码格式。相同的，其他国家的语言也有属于自己的编码格式。

2. Unicode

   由于每个国家的语言都有属于自己的编码格式，在多语言编辑文本中会出现乱码，这样Unicode应运而生，Unicode就是将这些语言统一到一套编码格式中，通常两个字节表示一个字符，而ASCII是一个字节表示一个字符，这样如果你编译的文本是全英文的，用Unicode编码比ASCII编码需要多一倍的存储空间，在存储和传输上就十分不划算。

3. UTF-8

   为了解决上述问题，又出现了把Unicode编码转化为“可变长编码”UTF-8编码，UTF-8编码将Unicode字符按数字大小编码为1-6个字节，英文字母被编码成一个字节，常用汉字被编码成三个字节，如果你编译的文本是纯英文的，那么用UTF-8就会非常节省空间，并且ASCII码也是UTF-8的一部分。

#### 1、面向对象和面向过程的区别

- **面向过程：性能比面向对象高**。因为类调用的时候需要实例化，开销比较大，比较消耗资源，所以当性能是最重要的考量因素的时候，比如单片机、嵌入式开发、Linux/Unix等一般采用面向过程开发。但是，面向过程没有**面向对象易维护、易复用、易扩展。**
- **面向对象：面向对象易维护、易复用、易扩展。**因为面向对象有封装、继承、多态性的特性，所以可以设计出低耦合的系统，使系统更加灵活、更加易于维护。但是，**面向对象性能比面向过程低。**

##### 面向对象的理解

面向对象是将现实问题构建关系。然后抽象成类，给类定义属性和方法后，再将类实例化成实例，通过实例的属性和方法来进行使用。

##### Java JDK与JRE的区别

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20170314192719335)

- **`JRE(Java Runtime Enviroment)`**是 **Java运行时环境**。面向Java程序的使用者，而不是开发者。如果你仅下载并安装了JRE，那么你的系统只能运行Java程序。JRE是运行Java程序所必须环境的集合，包含JVM标准实现及Java核心类库。它包括Java虚拟机、Java平台核心类和支持文件。它不包含开发工具(编译器、调试器等)。
- **`JDK(Java Development Kit)`**又称J2SDK(Java2 Software Development Kit)，是**Java开发工具包**，它提供了Java的开发环境(提供了编译器 `javac` 等工具，用于将java文件编译为class文件)和运行环境(提供了JVM和Runtime辅助包，用于解析class文件使其得到运行)。如果你下载并安装了JDK，那么你不仅可以开发Java程序，也同时拥有了运行Java程序的平台。JDK是整个Java的核心，包括了Java运行环境(JRE)，一堆Java工具tools.jar和Java标准类库(rt.jar)。

##### JDK与OpenJDK的区别

- **授权协议的不同**

openjdk采用GPL V2协议放出，而JDK则采用JRL放出。两者协议虽然都是开放源代码的，但是在使用上的不同在于GPL V2允许在商业上使用，而JRL只允许个人研究使用。

- **OpenJDK不包含Deployment(部署)功能**

部署的功能包括：Browser Plugin、Java Web Start、以及Java控制面板，这些功能在Openjdk中是找不到的。

- **OpenJDK源代码不完整**

这个很容易想到，在采用GPL协议的Openjdk中，sun jdk的一部分源代码因为产权的问题无法开放openjdk使用，其中最主要的部份就是JMX中的可选元件SNMP部份的代码。因此这些不能开放的源代码将它作成plug，以供OpenJDK编译时使用，你也可以选择不要使用plug。而Icedtea则为这些不完整的部分开发了相同功能的源代码(OpenJDK6)，促使OpenJDK更加完整。

- **部分源代码用开源代码替换**

由于产权的问题，很多产权不是SUN的源代码被替换成一些功能相同的开源代码，比如说字体栅格化引擎，使用Free Type代替。

- **openjdk只包含最精简的JDK**

OpenJDK不包含其他的软件包，比如Rhino Java DB JAXP……，并且可以分离的软件包也都是尽量的分离，但是这大多数都是自由软件，你可以自己下载加入。

- **不能使用Java商标**

这个很容易理解，在安装openjdk的机器上，输入“java -version”显示的是openjdk，但是如果是使用Icedtea补丁的openjdk，显示的是java。(未验证)

总之，在Java体系中，还是有很多不自由的成分，源代码的开发不够彻底，希望Oracle能够让JCP更自由开放一些，这也是所有Java社区所希望的。

*********************

#### 2、JVM

Java虚拟机(JVM)是运行JVM字节码的虚拟机，JVM有针对不同的操作系统(Windows，Linux，macOS)的特定实现，目的是使用相同的字节码，他们都会给出相同的结果。具体可看[JVM](JVM/JVM.md)。

`字节码`

> 在Java中，JVM可以理解的代码就叫做`字节码`(即扩展名为`.class`的文件)，它不面向任何特定的处理器，只面向虚拟机。

##### Java程序从源程序到运行

![image-20210408212145906](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210408212145906.png)

​		我们需要格外注意的是.class->机器码这一步。在这一步JVM类加载器首先加载字节码文件，然后通过解释器逐行解释执行，这种方式的执行速度会相对比较慢。而且，有些方法和代码块是经常需要被调用的(也就是所谓的热点代码)，所以后面引进了JIT编译器，而JIT属于运行时编译。当JIT编译器完成第一次编译后，其会将字节码对应的机器码保存下来，下次可以直接使用。而我们知道，**机器码的运行效率肯定是高于Java解释器的**。这也解释了我们为什么经常会说Java是编译与解释共存的语言。

> ​		HotSpot 采用了惰性评估(Lazy Evaluation)的做法，根据二八定律，消耗大部分系统资源的只有那一小部分的代码(热点代码)，而这也就是JIT所需要编译的部分。JVM会根据代码每次被执行的情况收集信息并相应地做出一些优化，因此执行的次数越多，它的速度就越快。JDK9引入了一种新的编译模式 AOT(Ahead of Time Compilation)，它是直接将字节码编译成机器码，这样就避免了JIT预热等各方面的开销。JDK支持分层编译和AOT协作使用。但是，AOT编译器的编译质量是肯定比不上JIT编译器的。



**********************

#### 3、Java与C++

- 都是面向对象的语言，都支持继承、封装和多态；
- Java不提供指针来访问内存，程序内存更安全；
- Java的类是单继承的，C++支持多重继承；java类不支持单继承，但是接口可以多继承。
- Java有自动内存管理机制，不需要程序员手动释放无用内存；
- 在C语言中，字符串或者字符数组最后都会有一个额外的字符`'\0'`来表示结束。但是Java语言中没有结束符这一概念。

***************

#### 4、java代码从编译到执行的过程浅析

`java` 从源程序到真正执行需要经历**从 `.java`文件到 `.class` 文件的编译**(前端编译，对应的编译器有javac，eclipse的ECJ)，**`.class` 文件到二进制机器码的解释执行**(后端编译，对应的编译器有JIT)；还有一种能够直接把java文件编译成本地机器代码的静态提前编译器(AOT编译器)。

**编译原理简单过程：词法分析 --> 语法分析 --> 语义分析和中间代码生成 --> 优化 --> 目标代码生成**

Java程序从源文件创建到程序运行要经过两大步骤：

1、Java文件会由编译器编译成class文件(字节码文件)，会经过编译原理简单过程的前三步；

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1051030-20190917212211981-603607635.png)

2、字节码由java虚拟机解释运行，解释执行即为目标代码生成并执行。因为java程序既要编译的同时也要经过JVM的解释运行，所以说Java被称为半解释语言！

*************

#### ==5、String、StringBuffer和StringBuilder==

##### 1、String对象不可变

> String类中使用 **final** 关键字修饰，所以 String 对象不可变。
>
> Java9之前：`private final char value[]`；Java9之后：`priveate final byte[] value`。
>
> ​	首先创建一个String对象s，然后让s的值为 `ABCabc`， 然后又让s的值为 `123456`。 从打印结果可以看出，s的值确实改变了。那么怎么还说String对象是不可变的呢？ 其实这里存在一个误区： s只是一个String对象的引用，并不是对象本身。对象在内存中是一块内存区，成员变量越多，这块内存区占的空间越大。引用只是一个4字节的数据，里面存放了它所指向的对象的地址，通过这个地址可以访问对象。
>
> ​	也就是说，s只是一个引用，它指向了一个具体的对象，当 `s= "123456"`; 这句代码执行过之后，又创建了一个新的对象``123456``， 而引用s重新指向了这个心的对象，原来的对象 `ABCabc` 还在内存中存在，并没有改变。内存结构如下图所示：
>
> ![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20200207140008413.png)
>
> ```java
>      String s1 = "abcdef";
>      String s2 = "abc" + "def";
>      String s3 = new String("abc") + new String("def");
>      String s4 = new String("abcdef");
>      System.out.println(s1 == s2); // true
>      System.out.println(s1 == s3); // false
>      System.out.println(s1 == s4); // false
> ```
>
> ```markdown
> **
> 1. 直接通过双引号""声明字符串的方式, 虚拟机首先会到字符串常量池中查找该字符串是否已经存在. 如果存在会直接返回该引用, 如果不存在则会在堆内存中创建该字符串对象, 然后到字符串常量池中注册该字符串。
> 	所以 s1 == s2 为true
> 2. 使用new关键字创建字符串对象的时候, JVM将不会查询字符串常量池, 它将会直接在堆内存中创建一个字符串对象, 并返回给变量。然后查询常量池上是否存在字符串的字面量
> 	1.1. 如果不存在 在常量池上创建常量
> 	1.2. 如果存在 不做任何操作
> 	所以 s1 == 44 为false
> **
> ```
>
> 具体详见 [博客](https://blog.csdn.net/qq_32907195/article/details/108329683?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161285317216780266257057%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=161285317216780266257057&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2)。

> `StringBuffer`、`StringBuilder`是使用字符数组保存字符串 `byte[] value;`，但是没有`final`关键字修饰，所以这两种对象都是可变的。

##### 2、线程安全性

> `String`对象是不可变的，也可以理解为常量，线程安全。
>
> `StringBuffer`对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的；
>
> `StringBuilder`并没有对方法加同步锁，所以是非线程安全的。

##### 3、String和 StringBuilder 进行字符串拼接时的区别

​		在做字符串拼接时, String是用 "+" 号进行字符串的拼接, 底层是先在堆内存中创建一个 `StringBuilder` 对象, 用来进行字符串拼接, 然后使用 `toString()` 方法把 `StringBuilder` 对象转换成 `String` 对象, 完成字符串的拼接;而 `StringBuilder` 则是直接进行拼接, 所以 `StringBuilder` 的拼接效率比 `String` 的效率高。

##### 4、StringBuilder 底层

底层实现是数组 ：`byte[] value;`。

**扩容：**

```java
// Twice the old capacity, plus 2.
private int newCapacity(int minCapacity) {
        // overflow-conscious code
        int newCapacity = (value.length << 1) + 2;
        if (newCapacity - minCapacity < 0) {
            newCapacity = minCapacity;
        }
        return (newCapacity <= 0 || MAX_ARRAY_SIZE - newCapacity < 0)
            ? hugeCapacity(minCapacity)
            : newCapacity;
    }
```

扩容的大小是新字符串的长度的2倍，然后再加上2。**为什么要加2呢？**

```java
/*
在使用StringBuilder的时候，append()之后，我们一般会在后面在加上一个分隔符，例如逗号，也就是再加上一个char，而char在java中占2个字节，避免了因为添加分隔符而再次引起扩容。
*/
```

##### 5、toString()、String.valueOf、(String)强转有什么区别？

- **基本类型**
  - **基本类型没有 `toString()`方法 **
  - 基本类型转为 string 类型推荐使用 `String.valueOf()`
  - `(String)`是标准的类型转换，将 `Object` 类型转为 `String` 类型，使用 `(String)` 强转时，最好使用`instanceof` 做一个类型检查，以判断是否可以进行强转，否则容易抛出 `ClassCastException` 异常。需要注意的是编写的时候，编译器并不会提示有语法错误，所以这个方法要谨慎的使用。
    - ![image-20210923220202121](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210923220202121.png)
    - ![image-20210923220225972](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210923220225972.png)
- **封装类型**
  - **封装类型有 `toString()`方法** 
    - 如果是 `NULL` 的话，会报空指针异常
  - **`String.valueOf()`遇到 `null` 返回字符串“null”**
  - **封装类型也无法使用(string)强转**

**源码分析**

- `toString()`
  - ![image-20210923225145795](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210923225145795.png)
  - ![image-20210923225200770](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210923225200770.png)
- `String.ValueOf()`
  - **`String.valueOf()` 比 `toString()` 多了一个非空判断。**
  - ![image-20210923225216201](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210923225216201.png)

#### 6、final关键字

final关键字可以用来修饰变量、方法和类。

1. 对于一个final变量，如果是**基本数据类型的变量，则其数值一旦在初始化之后便不能更改**；如果是**引用数据类型，比如对象、数组，则该对象、数组本身可以修改，但指向该对象或数组的地址的引用不能修改。**

2. 当**用final修饰一个类时，表明这个类不能被继承**。**final类中的所有成员方法都会被隐式地指定为final方法**。比如常用的String类就是最终类。

3. 当**使用final修饰方法**时，这个方法将成为最终方法，**无法被子类重写**。但是，该方法仍然可以被继承。

   使用final方法的原因有两个。第一个原因是把方法锁定，以防任何继承类修改它的含义；第二个原因是效率。在早期的Java 实现版本中，会将final方法转为内嵌调用。但是如果方法过于庞大，可能看不到内嵌调用带来的任何性能提升(现在的Java版本已经不需要使用final方法进行这些优化了)。类中所有的private方法都隐式地指定为final。

#### 7、final、finally、finalize的区别

- `final` 是一个关键字，用来修饰类，方法和变量。
  - 当用 `final` 修饰类的时，表明该类不能被其他类所继承。当我们需要让一个类永远不被继承，此时就可以用 `final` 修饰，但要注意：`final` 类中所有的成员方法都会隐式的定义为 `final` 方法。
  - 当使用 `final` 修饰方法时，这个方法将成为最终方法，无法被子类重写。但是，该方法仍然可以被继承。
  - 对于一个 `final` 变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用数据类型，比如对象、数组，则该对象、数组本身可以修改，但指向该对象或数组的地址的引用不能修改。
- `finally` 作为异常处理的一部分，它只能用在 `try/catch` 语句中，并且附带一个语句块。
  - 只有与 `finally` 对应的 `try` 语句块得到执行的情况下，`finally` 语句块才会执行。以上两种情况在执行 `try` 语句块之前已经返回或抛出异常，所以 `try` 对应的 `finally` 语句并没有执行。
- `finalize()` 方法是在垃圾收集器删除对象之前对这个对象调用的，当垃圾回收器(garbage colector)决定回收某对象时，就会运行该对象的 `finalize()` 方法。

****************

#### 8、[深入理解Java面向对象三大特性 封装 继承 多态](https://www.cnblogs.com/ibalintha/p/3897821.html)

##### 封装

> 封装的定义：首先是抽象，把事物抽象成一个类，其次才是封装，将事物拥有的属性和动作隐藏起来，只保留特定的方法与外界联系。
>
> 封装符合[面向对象设计原则](https://www.cnblogs.com/yeya/p/10655760.html)的第一条：单一性原则，一个类把自己该做的事情封装起来，而不是暴露给其他类去处理，当内部的逻辑发生变化时，外部调用不用因此而修改，他们只调用开放的接口，而不用去关心内部的实现。

##### 继承

Java的类可以分为三类：

- 类：使用class定义，没有抽象方法
- 抽象类：使用abstract class定义，可以有也可以没有抽象方法
- 接口：使用interface定义，只能有抽象方法

> - 类可以extends：类、抽象类(必须实现所有抽象方法)，但只能extends一个，可以implements多个接口(必须实现所有接口方法)
> - 抽象类可以extends：类，抽象类(可全部、部分、或者完全不实现父类抽象方法)，可以implements多个接口(可全部、部分、或者完全不实现接口方法)
> - 接口可以extends多个接口

###### 继承与组合有什么区别

组合和继承是面向对象中两种代码复用的方式。

- 组合是指在新类里面创建原有类的对象，重复利用已有类的功能。
- 继承是面向对象的主要特性之一，它允许设计人员根据其它类的实现来定义一个类的实现。



##### 多态

> 同一个行为具有多个不同表现形式或形态的能力。
>
> 多态存在的三个必要条件：继承、重写、父类引用指向子类对象：`Parent p = new Child()`。

- 一个方法可以由：修饰符(如public)、static+返回值+方法名+参数+throw的异常 5部分构成
- 其中只有**方法名和参数是唯一性标识**，意即只要**方法名和参数相同那他们就是相同的方法**
- 所谓参数相同，是指**参数的个数，类型，顺序**一致，其中任何一项不同都是不同的方法

###### 重载(Overload)

- 重载是指==一个类==里面(包括父类的方法)存在**方法名相同，但是参数不一样**的方法，参数不一样可以是不同的参数个数、类型或顺序
- 如果仅仅是修饰符、返回值、throw的异常不同，那这是2个相同的方法，编译都通不过，更不要说重载了

###### 重写(Override)

重写是==子类对父类==的允许访问的方法的实现过程进行重新编写, 返回值和形参都不能改变。**即外壳不变，核心重写！**

- 覆盖描述存在==继承关系==时子类的一种行为
- 子类中存在和父类相同的方法即为覆盖，何谓相同方法请牢记前面的描述，方法名和参数相同，包括参数个数、类型、顺序

> 如果方法的返回类型是void和基本数据类型，则返回值重写时不可修改。但是如果方法的返回值是引用类型，重写时是可以返回该引用类型的子类的。
>
> **哪些方法不能被重写：**
>
> - `final` 修饰的；
> - `static` 修饰的；
> - `private` 修饰的：因为私有的在子类中不可见；
> - 如果跨包的话，修饰符缺省的也不能被重写，因为缺省的挎包不可见；

```java
public class Hero {
	public String name() {
		return "超级英雄";
	}
}
public class SuperMan extends Hero{
	@Override
	public String name() {
		return "超人";
	}
	public Hero hero() {
		return new Hero();
	}
}
public class SuperSuperMan extends SuperMan {
    public String name() {
	return "超级超级英雄";
	}
	@Override
	public SuperMan hero() {
		return new SuperMan();
	}
}
```

###### 多态的实现机制

本质上多态分两种：

> **1、编译时多态(又称静态多态)**
>
> **2、运行时多态(又称动态多态)**

重载(overload)就是编译时多态的一个例子，编译时多态在编译时就已经确定，运行时运行的时候调用的是确定的方法。

**我们通常所说的多态指的都是运行时多态，也就是编译时不确定究竟调用哪个具体方法，一直延迟到运行时才能确定。**这也是为什么有时候多态方法又被称为延迟方法的原因。

**编译时多态**比较简单，不再赘述。

下面简要介绍一下运行时多态(以下简称多态)的机制。

运行时多态有两种实现方法：

- **子类继承父类**
- **类实现接口**

无论是哪种方法，其核心之处就在于对父类方法的改写或对接口方法的实现，以取得在运行时不同的执行效果。

要使用多态，在声明对象时就应该遵循一条法则：**声明的总是父类类型或接口类型，创建的是实际类型。**举例来说，假设我们要创建一个 `ArrayList` 对象，声明就应该采用这样的语句：

```java
List<T> list = new ArrayList();
```

而不是

```java
ArrayList<T> list =new ArrayList();
```

**在定义方法参数时也通常总是应该优先使用父类类型或接口类型**，例如某方法应该写成：

```java
public void doSomething(List list){
}
```

而不是

```java
public void doSomething(ArrayList list){
}
```

这样声明最大的好处在于结构的灵活性：假如某一天我认为 `ArrayList` 的特性无法满足我的要求，我希望能够用 `LinkedList`来代替它，那么只需要在对象创建的地方把 `new ArrayList()` 改为 `new LinkedList` 即可，其它代码一概不用改动。



**底层实现：**

在JVM执行Java字节码时，**类型信息被存放在方法区中**，通常为了优化对象调用方法的速度，方法区的类型信息中增加一个指针，该指针指向一张记录该类方法入口的表(称为方法表)，**表中的每一项都是指向相应方法的指针。**

方法表的构造如下：

由于Java的单继承机制，一个类只能继承一个父类，而所有的类又都继承自Object类。方法表中**最先存放的是Object类的方法，接下来是该类的父类的方法，最后是该类本身的方法。**这里关键的地方在于，**如果子类改写了父类的方法，那么子类和父类的那些同名方法共享一个方法表项，都被认作是父类的方法。**

注意这里只有**非私有**的实例方法才会出现，并且静态方法也不会出现在这里，原因很容易理解：静态方法跟对象无关，可以将方法地址直接引用，而不像实例方法需要间接引用。

更深入地讲，静态方法是由虚拟机指令 `invokestatic` 调用的，私有方法和构造函数则是由 `invokespecial` 指令调用，只有被 `invokevirtual` 和 `invokeinterface` 指令调用的方法才会在方法表中出现。

由于以上方法的排列特性(Object——父类——子类)，使得**方法表的偏移量总是固定的**。例如，对于任何类来说，其方法表中 `equals` 方法的偏移量总是一个定值，所有继承某父类的子类的方法表中，其父类所定义的方法的偏移量也总是一个定值。

前面说过，方法表中的表项都是指向该类对应方法的指针，这里就开始了多态的实现：

假设 `Class A` 是 `Class B` 的子类，并且 `A` 改写了 `B` 的方法 `method()`，那么在 `B` 的方法表中，`method` 方法的指针指向的就是 `B` 的 `method` 方法入口。

而对于 `A` 来说，它的方法表中的 `method` 方法则会指向其自身的 `method` 方法而非其父类的(这在类加载器载入该类时已经保证，同时JVM会保证总是能从对象引用指向正确的类型信息)。

结合**方法指针偏移量是固定的**以及**指针总是指向实际类的方法域**，我们不难发现多态的机制就在这里：

在调用方法时，实际上必须首先完成实例方法的符号引用解析，结果是该符号引用被解析为方法表的偏移量。虚拟机通过对象引用得到方法区中类型信息的入口，查询类的方法表，当将子类对象声明为父类类型时，形式上调用的是父类方法，此时虚拟机会从实际类的方法表(虽然声明的是父类，但是实际上这里的类型信息中存放的是子类的信息)中查找该方法名对应的指针(这里用“查找”实际上是不合适的，前面提到过，方法的偏移量是固定的，所以只需根据偏移量就能获得指针)，进而就能指向实际类的方法了。

我们的故事还没有结束，事实上上面的过程仅仅是利用继承实现多态的内部机制，多态的另外一种实现方式：实现接口相比而言就更加复杂，原因在于，**Java的单继承保证了类的线性关系，而接口可以同时实现多个，这样光凭偏移量就很难准确获得方法的指针。**所以在JVM中，多态的实例方法调用实际上有两种指令：

> **`invokevirtual` 指令用于调用声明为类的方法；**
>
> **`invokeinterface`指令用于调用声明为接口的方法。**

当使用 `invokeinterface`指令调用方法时，就不能采用固定偏移量的办法，只能老老实实挨个找了(当然实际实现并不一定如此，JVM规范并没有规定究竟如何实现这种查找，不同的JVM实现可以有不同的优化算法来提高搜索效率)。我们不难看出，**在性能上，调用接口引用的方法通常总是比调用类的引用的方法要慢。**这也告诉我们，在类和接口之间优先选择接口作为设计并不总是正确的，当然设计问题不在本文探讨的范围之内，但显然具体问题具体分析仍然不失为更好的选择



****************

#### 9、装箱与拆箱

> 装箱：自动将基本数据类型转换为包装器类型；基本类型->包装类
>
> 拆箱：自动将包装器类型转换为基本数据类型。包装类->基本类型
>
> > 装箱过程是通过调用包装器的`valueOf`方法实现的，而拆箱过程是通过调用包装器的` xxxValue`方法实现的。(xxx代表对应的基本数据类型)。
>
> ![image-20210409100230688](image/image-20210409100230688.png)

>![image-20210409100401900](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210409100401900.png)
>
>![image-20210409100411513](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210409100411513.png)
>
>​		==***在通过`valueOf`方法创建`Integer`对象的时候，如果数值在`[-128,127]`之间，便返回指向`IntegerCache.cache`中已经存在的对象的引用；否则创建一个新的`Integer`对象。***==
>
>​		==***上面的代码中i1和i2的数值为100，因此会直接从cache中取已经存在的对象，所以i1和i2指向的是同一个对象，而i3和i4则是分别指向不同的对象。***==
>
>==int 和 Integer在进行比较的时候，Integer会进行拆箱，转为int值与int进行比较。这个时候，'\=='比较的是数值大小==
>
>```java
>Integer i1 = 120;
>int i2 = 120;
>System.out.println(i1 == i2); // true
>
>Integer i1 = 80;
>int i2 = 80;
>System.out.println(i1 == i2); // true
>```
>
>



> ![image-20210409100610129](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210409100610129.png)
>
> 在这里只解释一下为什么Double类的`valueOf`方法会采用与Integer类的`valueOf`方法不同的实现。很简单：在某个范围内的整型数值的个数是有限的，而浮点数却不是。
>
> 　　**注意，Integer、Short、Byte、Character、Long这几个类的`valueOf`方法的实现是类似的。**
>
> 　　　　　**Double、Float的`valueOf`方法的实现是类似的。**

**`Integer i = new Integer(xxx)`和`Integer i = xxx`这两种方式的区别。**

　　当然，这个题目属于比较宽泛类型的。但是要点一定要答上，我总结一下主要有以下这两点区别：

　　1)**第一种方式不会触发自动装箱的过程；而第二种方式会触发**；

　　2)在执行效率和资源占用上的区别。第二种方式的执行效率和资源占用在一般性情况下要优于第一种情况(注意这并不是绝对的)。

> **当 "=="运算符的两个操作数都是 包装器类型的引用，则是比较指向的是否是同一个对象，而如果其中有一个操作数是表达式(即包含算术运算)则比较的是数值(即会触发自动拆箱的过程)。另外，对于包装器类型，equals方法并不会进行类型转换。**
>
> ![image-20210409100752951](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210409100752951.png)
>
> ![image-20210409100805906](image/image-20210409100805906.png)
>
> 第一个和第二个输出结果没有什么疑问。
>
> 第三句：` a+b`包含了算术运算，因此**会触发自动拆箱过程**(会调用`intValue`方法)，因此它们比较的是数值是否相等。
>
> 第四句：而对于`c.equals(a+b)`会先触发自动拆箱过程，再触发自动装箱过程，也就是说a+b，会先各自调用`intValue`方法，得到了加法运算后的数值之后，便调用`Integer.valueOf`方法，再进行`equals`比较。同理对于后面的也是这样。
>
> 不过要注意倒数第二个和最后一个输出的结果(如果数值是`int`类型的，装箱过程调用的是`Integer.valueOf`；如果是`long`类型的，装箱调用的`Long.valueOf`方法)。

*******************

#### 10、Java基本数据类型与包装类类型的区别

- **声明方式的不同：**基本类型无需通过new关键字来创建，而封装类型需new关键字。

- **初始值的不同：**封装类型的初始值为null，基本类型的的初始值视具体的类型而定。比如int类型的初始值为0，boolean类型为false；包装类型可以为 null，而基本类型不可以。

- **存储方式及位置的不同：**基本数据类型是直接存储变量的值，保存在虚拟机栈中。包装类型需要通过引用指向实例，具体的实例保存在堆中。

  - ```java
    Integer chenmo = new Integer(10);
    Integer wanger = new Integer(10);
    
    System.out.println(chenmo == wanger); // false
    System.out.println(chenmo.equals(wanger )); // true
    
    /**
    “==”进行判断的时候，判断的是其指向的地址是否相等
    equals 方法内部比较的是两个 int 值是否相等
    */
    ```

- 使用方式的不同：包装类型可用于泛型，而基本类型不可以; 比如与集合类一起使用时只能使用包装类型。

  - ```java
    List<int> list = new ArrayList<>(); // 提示 Syntax error, insert "Dimensions" to complete ReferenceType
    List<Integer> list = new ArrayList<>(); //这里就不会提示出错
    
    /**
    因为泛型在编译时会进行类型擦除，最后只保留原始类型，而原始类型只能是 Object 类及其子类——基本类型是个特例。
    */
    ```

**************

#### 11、Java泛型

##### 泛型出现的原因

```java
public class GenericTest {

    public static void main(String[] args) {
        List list = new ArrayList();
        list.add("qqyumidi");
        list.add("corn");
        list.add(100);

        for (int i = 0; i < list.size(); i++) {
            String name = (String) list.get(i); // 1
            System.out.println("name:" + name);
        }
    }
}
```

1.当我们将一个对象放入集合中，集合不会记住此对象的类型，当再次从集合中取出此对象时，该对象的编译类型变成了Object类型，但其运行时类型任然为其本身类型。

2.因此，`//1` 处取出集合元素时需要人为的强制类型转化到具体的目标类型，且很容易出现 `java.lang.ClassCastException` 异常。

**那么有没有什么办法可以使集合能够记住集合内元素各类型，且能够达到只要编译时不出现问题，运行时就不会出现 `java.lang.ClassCastException` 异常呢？答案就是使用泛型。**

##### 什么是泛型

**泛型，即“参数化类型”。一提到参数，最熟悉的就是定义方法时有形参，然后调用此方法时传递实参。那么参数化类型怎么理解呢？顾名思义，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式(可以称之为类型形参)，然后在使用/调用时传入具体的类型(类型实参)**

上面代码使用泛型的写法

```java
public class GenericTest {

    public static void main(String[] args) {
        /*
        List list = new ArrayList();
        list.add("qqyumidi");
        list.add("corn");
        list.add(100);
        */

        List<String> list = new ArrayList<String>();
        list.add("qqyumidi");
        list.add("corn");
        //list.add(100);   // 1  提示编译错误

        for (int i = 0; i < list.size(); i++) {
            String name = list.get(i); // 2
            System.out.println("name:" + name);
        }
    }
}
```

采用泛型写法后，在`//1`处想加入一个`Integer`类型的对象时会出现编译错误，通过`List<String>`，直接限定了`list`集合中只能含有`String`类型的元素，从而在`//2`处无须进行强制类型转换，因为此时，集合能够记住元素的类型信息，编译器已经能够确认它是`String`类型了。

结合上面的泛型定义，我们知道在`List<String>`中，`String`是类型实参，也就是说，相应的`List`接口中肯定含有类型形参。且`get()`方法的返回结果也直接是此形参类型(也就是对应的传入的类型实参)。

**优点：使用泛型时，在实际使用之前类型就已经确定了，不需要强制类型转换。**

![image-20211012222255050](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20211012222255050.png)

![image-20211012222308970](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20211012222308970.png)

我们想要使用哪种类型，就在创建的时候指定类型，使用的时候，该类就会自动转换成用户想要使用的类型。

**规则:**

- 子类也是泛型类，那么子类和父类的泛型类型要一致

```java
 public class ResultChild<T> extends Result<T> {}
```

- 子类不是泛型类，那么父类要指定数据类型

```java
 public class ResultChild extends Result<String> {}
```



##### Java泛型的实现原理

###### **擦除**

```java
public class GenericType {
    public static void main(String[] args) {  
        ArrayList<String> arrayString=new ArrayList<String>();   
        ArrayList<Integer> arrayInteger=new ArrayList<Integer>();   
        System.out.println(arrayString.getClass()==arrayInteger.getClass());  
    }  
}

输出：true
```

在这个例子中，我们定义了两个 `ArrayList` 数组，不过一个是 `ArrayList<String>` 泛型类型，只能存储字符串。一个是`ArrayList<Integer>` 泛型类型，只能存储整型。最后，我们通过 `arrayString` 对象和 `arrayInteger` 对象的 `getClass` 方法获取它们的类信息并比较，发现结果为 `true` 。

这是为什么呢，明明我们定义了两种不同的类型？因为，在编译期间，所有的泛型信息都会被擦除，`List<Integer>` 和` List<String>` 类型，在编译后都会变成**List类型(原始类型)**。Java中的泛型基本上都是在编译器这个层次来实现的，这也是Java的泛型被称为“伪泛型”的原因。



###### 原始类型

原始类型就是泛型类型擦除了泛型信息后，在字节码中真正的类型。无论何时定义一个泛型类型，相应的原始类型都会被自动提供。原始类型的名字就是删去类型参数后的泛型类型的类名。擦除 **类型变量**，并替换为 **限定类型**(T为无限定的 **类型变量**，用Object替换)。

```java
//泛型类型
class Pair<T> {  
    private T value;  
    public T getValue() {  
        return value;  
    }  
    public void setValue(T  value) {  
        this.value = value;  
    }  
}


//原始类型
class Pair {  
    private Object value;  
    public Object getValue() {  
        return value;  
    }  
    public void setValue(Object  value) {  
        this.value = value;  
    }  
}
```

##### 突破泛型约束

```java
public class ReflectInGeneric {
    public static void main(String[] args) throws IllegalArgumentException, 
                        SecurityException, IllegalAccessException, InvocationTargetException, NoSuchMethodException {  
        ArrayList<Integer> array=new ArrayList<Integer>();  
        // 这样调用add方法只能存储整形，因为泛型类型的实例为Integer 
        array.add(1); 
        // 通过泛型可以突破泛型类型约束
        array.getClass().getMethod("add", Object.class).invoke(array, "asd");  
        for (int i=0;i<array.size();i++) {  
            System.out.println(array.get(i));  
        }  
    }  
}
输出：
1
asd 
```

为什么呢？我们在介绍泛型时指出向 `ArrayList<Integer>` 中插入 `String` 类型的对象，编译时会报错。现在为什么又可以了呢？

- 我们在程序中定义了一个 `ArrayList<Integer>` 泛型类型，如果直接调用 `add` 方法，那么只能存储整形的数据。
- 不过当我们利用反射调用 `add` 方法的时候，却可以存储字符串。

这说明 `ArrayList<Integer>` 泛型信息在编译之后被擦除了，只保留了原始类型，**类型变量(T)**被替换为**Object**，在运行时，我们可以行其中插入任意类型的对象。

再次应证：==Java中的泛型基本上都是在编译器这个层次来实现的“伪泛型”==。

但是，并不推荐以这种方式操作泛型类型，因为这**违背了泛型的初衷**(减少强制类型转换以及确保类型安全)。当我们从集合中获取元素时，默认会将对象强制转换成泛型参数指定的类型(这里是Integer)，如果放入了非法的对象这个强制转换过程就会出现异常。

**************

#### ==12、Java对象头==

`HotSpot`虚拟机中，对象在内存中存储的布局可以分为三块区域：对象头(Header)、实例数据(Instance Data)和对齐填充(Padding)。

##### 10.1、对象头的形式

JVM中对象头的方式有以下两种(以32位JVM为例)

- **普通对象**
  - ![image-20210815095839602](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210815095839602.png)
- **数组对象**
  - ![image-20210815100706519](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210815100706519.png)

##### 10.2、对象头的组成

- #### Mark Word

  - 用来存储对象自身的运行时数据，如 `hashcode`、``gc分代年龄``等。32位JVM的`Mark word`为32位，64位JVM为64位。
  - ![image-20210815101002985](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210815101002985.png)
  - ![image-20210815100818841](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210815100818841.png)
  - **age**：4位的Java对象年龄。在GC中，如果对象在Survivor区复制一次，年龄增加1。当对象达到设定的阈值时，将会晋升到老年代。默认情况下，并行GC的年龄阈值为15，并发GC的年龄阈值为6。由于age只有4位，所以最大值为15，这就是`-XX:MaxTenuringThreshold`选项最大值为15的原因。

- #### class pointer

  - 这一部分用于存储对象的类型指针，该指针指向它的类元数据，JVM通过这个指针确定对象是哪个类的实例。该指针的位长度为JVM的一个字大小，即32位的JVM为32位，64位的JVM为64位。

  - 如果应用的对象过多，使用64位的指针将浪费大量内存，统计而言，64位的JVM将会比32位的JVM多耗费50%的内存。为了节约内存可以使用选项`+UseCompressedOops`开启指针压缩，其中，oop即ordinary object pointer普通对象指针。开启该选项后，下列指针将压缩至32位：

    1. 每个Class的属性指针(即静态变量)
    2. 每个对象的属性指针(即对象变量)
    3. 普通对象数组的每个元素指针

    当然，也不是所有的指针都会压缩，一些特殊类型的指针JVM不会优化，比如指向PermGen(永久代)的Class对象指针(JDK8中指向元空间的Class对象指针)、本地变量、堆栈元素、入参、返回值和NULL指针等。

- #### array length

  - 如果对象是一个数组，那么对象头还需要有额外的空间用于存储数组的长度，这部分数据的长度也随着JVM架构的不同而不同：32位的JVM上，长度为32位；64位JVM则为64位。64位JVM如果开启`+UseCompressedOops`选项，**该区域长度也将由64位压缩至32位**。

************************

#### 13、接口与抽象类的区别

- **抽象类：**在面向对象的概念中，所有的对象都是通过类来描绘的，但是反过来，并不是所有的类都是用来描绘对象的，如果一个类中没有包含足够的信息来描绘一个具体的对象，这样的类就是抽象类。

  - > 抽象类除了不能实例化对象之外，类的其它功能依然存在，成员变量、成员方法和构造方法的访问方式和普通类一样。
    >
    > 抽象类可以继承抽象类;
    >
    > 抽象类中的方法**不能设置为private**，若为 `private`，则不能给子类继承，子类无法实现该方法，所以无意义。

- **接口：** 除非实现接口的类是抽象类，否则该类要定义接口中的所有方法。接口无法被实例化，但是可以被实现。一个实现接口的类，必须实现接口内所描述的所有方法，否则就必须声明为抽象类。

**区别：**

- 抽象类可以提供成员方法的实现细节；而接口中的方法默认是 `public` ，但是不能有实现；
- 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是 `public static final` 类型的；
- 接口中不能含有静态代码块以及静态方法，而抽象类可以有静态代码块和静态方法；
- 一个类只能继承一个抽象类，而一个类却可以实现多个接口。

**何时使用抽象类，何时使用接口**

- 抽象类表示它是什么。
  - 比如定义一个 `Person` 类，有眼睛、嘴巴等特征。
- 接口表示能干什么。
  - 比如一个人会打篮球。

#### 14、Static方法

> static方法就是没有this的方法。在static方法内部不能调用非静态方法，反过来是可以的。而且可以在没有创建任何对象的前提下，仅仅通过类本身来调用static方法。这实际上正是static方法的主要用途。
>
> **方便在没有创建对象的情况下来进行调用(方法/变量)**

##### static修饰成员方法

static修饰的方法一般称作静态方法，由于静态方法不依赖于任何对象就可以进行访问，因此对于静态方法来说，是没有this的，因为它不依附于任何对象，既然都没有对象，就谈不上this了。并且由于这个特性，在**静态方法中不能访问类的非静态成员变量和非静态成员方法，因为非静态成员方法/变量都必须依赖具体的对象才能够被调用。但是在非静态成员方法中是可以访问静态成员方法/变量的。**

##### static修饰成员变量

static修饰的变量也称为静态变量，静态变量和非静态变量的区别是：静态变量被所有对象共享，在内存中只有一个副本，它**当且仅当在类初次加载时会被初始化。**而非静态变量是对象所拥有的，在创建对象的时候被初始化，存在多个副本，各个对象拥有的副本互不影响。

static成员变量的初始化顺序按照定义的顺序进行初始化。

##### static修饰代码块

static块可以置于类中的任何地方，类中可以有多个static块。在类初次被加载的时候，会**按照static块的顺序来依次执行每个static块，并且只会执行一次。**

static块可以优化程序性能，是因为它的特性：只会在类被初次加载的时候执行一次。

###### 静态方法和实例方法有何不同

1. 在外部调用静态方法时，可以使用"类名字.方法名"的方式，也可以使用“对象名.方法名”的方法。而实例方法只有后面这种方式。也就是说，**调用静态方法无需创建对象**。
2. 静态方法在访问本类的成员时，只允许访问静态成员(即静态成员变量和静态方法)，不允许访问实例成员变量和实例方法；实例方法无此限制。

******************

#### 15、Servlet(Server Applet)

Java Servlet的缩写，称为小服务器程序或服务连接器。

侠义的Servlet是指Java语言的实现的一个接口，广义的Servlet是指实现了这个servlet接口的所有实现类

##### Servlet生命周期

- servlet初始化后调用`init()`方法  	 `init()`方法被设计成只调用一次，第一次创建servlet时被调用
  - servlet调用`service`方法处理客户端请求  `service()`是实际执行任务的方法
- `servlet()`销毁前调用`destroy()`方法，`destroy()`只会被调用一次，在`servlet`生命周期结束时被调用；`destroy`方法可以让`servlet`关闭数据库连接，停止后台线程，把`cookie`列表或者点击计数器写入磁盘，并执行类似的清理活动
- 最后，servlet由JVM的垃圾回收器回收。

*************

#### 16、与equals==

- **==：**判断两个对象是不是同一个对象(基本数据类型`==`比较的是值，引用数据类型`==`比较的是地址)。
- **equals：**若`equals`没有重写，则`equals`等价于`==`；若`equals`被重写，比如String中的equals，则比较的是对象的数值。

#### ==17、HashCode与equals==

**不同：**

- equals()：反映的是对象或变量具体的值，即两个对象里面包含的值--可能是对象的引用，也可能是值类型的值。
- hashCode()：计算出对象实例的哈希码，并返回哈希码，又称为散列函数。根类Object的hashCode()方法的计算依赖于对象实例的D(内存地址)，故每个Object对象的hashCode都是唯一的；当然，当对象所对应的类重写了hashCode()方法时，结果就截然不同了。

##### HashCode()

>  `hashCode()`的作用是获取哈希码，也成为散列值；实际上是返回一个`int`整数，确定该对象在哈希表中的索引位置。
>
> Java中的任何类都包含有`hashCode()`函数。
>
> > `Object `的`hashcode`方法是本地方法，也就是用c语言或c++实现的，该方法通常用来将对象的内存地址转换为整数之后返回。
> >
> > ![image-20210409104547944](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210409104547944.png)
> 
> 散列表存储的是键值对(key-value)，特点是：能根据“键”快速的检索出对应的“值”。这其中就利用了散列码。

##### 为什么要有hashCode()

我们以"`HashSet `如何检查重复”为例子来说明为什么要有`hashCode`？

​		当你把对象加入`Hashset `时，`HashSet `会先计算对象的`hashcode`值来判断对象加入的位置，同时也会与其他已经加入的对象的`hashcode`值作比较，如果没有相符的`hashcode`，`HashSet`会假设对象没有重复出现。但是如果发现有相同`hashcode`值的对象，这时会调用`equals()`方法来检查`hashcode`相等的对象是否真的相同。如果两者相同，`HashSet` 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样我们就大大减少了equals的次数，相应就大大提高了执行速度。

##### 为什么重写equals 时必须重写hashCode 方法？

如果两个对象相等，则`hashcode`一定也是相同的。两个对象相等，对两个对象分别调用`equals`方法都返回`true`。但是，两个对象有相同的`hashcode`值，它们也不一定是相等的。因此，**`equals` 方法被覆盖过，则`hashCode`方法也必须被覆盖。**

> `hashCode()`的默认行为是对堆上的对象产生独特值。如果没有重写`hashCode()`，则该class的两个对象无论如何都不会相等(即使这两个对象指向相同的数据)。

###### 规定

- `equals`相等，`hasCode()`一定相等；
- `equals`不等，`hasCode()`不一定相等；
- `hasCode`不等，`equals`一定不等；
- `hasCode`相等，`equals`不一定相等；

```markdown
**
1. 如果两个对象通过euqals() 方法比较返回true,但这两个对象的 hashCode() 方法返回不同的hashCode 值时，这将导致HashSet 会把这两个对象保存在 Hash 表的不同位置，从而使两个对象都可以添加成功，这就与 Set 集合的规则冲突了。
2. 如果两个对象的 hashCode() 方法返回的 hasCode 值相同，但他们通过 equals() 方法比较返回false 时将更麻烦：因为两个对象的hashCode 值相同，HashSet 将试图 把它们保存在同一个位置，但又不行(否则将只剩下一个对象)，所以实际上会在这个位置用链式结构来保存多个对象；而HashSet 访问集合元素时也是根据元素的 hashCode 值来快速定位的，如果 hashSet 中两个以上的元素具有相同的 HashCode 值时，将会导致性能下降。
```



##### 为什么两个对象有相同的hashcode值，它们也不一定是相等的？

​		因为`hashCode()`所使用的哈希算法也许刚好会让多个对象传回相同的哈希值。越糟糕的哈希算法越容易碰撞，但这也与数据值域分布的特性有关(所谓碰撞也就是指的是不同的对象得到相同的`hashCode`)。

​		我们刚刚也提到了`HashSet`，如果`HashSet `在对比的时候，同样的`hashcode`有多个对象，它会使用`equals()`来判断是否真的相同。也就是说`hashcode `只是用来缩小查找成本。

#### 18、Java中只有值传递

> 按值调用(call by value)表示方法接收的是调用者提供的值，而按引用调用(call by reference)表示方法接收的是调用者提供的变量地址。**一个方法可以修改传递引用所对应的变量值，而不能修改传递值调用所对应的变量值。**它用来描述各种程序设计语言(不只是Java)中方法参数传递方式。
> **Java程序设计语言总是采用按值调用**。也就是说，**方法得到的是所有参数值的一个拷贝**，也就是说，**方法不能修改传递给它的任何参数变量的内容。**

*************

#### 19、Java序列化与反序列化

​	序列化 (Serialization)是将对象的状态信息转换为可以存储或传输的形式的过程。在序列化期间，对象将其当前状态写入到临时或持久性存储区。以后，可以通过从存储区中读取或反序列化对象的状态，重新创建该对象。

- 把对象转换为字节序列的过程称为对象的序列化；
- 把字节序列恢复为对象的过程称为对象的反序列化。

`serialVersionUID`: 字面意思上是序列化的版本号，凡是实现 `Serializable` 接口的类都有一个表示序列化版本标识符的静态变量。

```java
private static final long serialVersionUID
```

**显式地定义 `serialVersionUID` 有两种用途：**
　　1、 在某些场合，希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有相同的 `serialVersionUID`；
　　2、 在某些场合，不希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有不同的 `serialVersionUID`。



*********************

#### 20、Java异常

##### 你遇到过哪些异常

- 空指针异常: `NullPointerException`
- 数组越界: `ArrayIndexOutOfBoundsException`
- 操作数据库异常：`SQLException`
- 并发修改异常: `java.util.ConcurrentModificationException `
- ......

##### 为什么使用异常

代替日渐衰落的error code方法的新法，提供error code所没有的优势。

异常处理分离了接收和处理错误代码。这个功能理清了编程者的思绪，也帮助代码增强了可读性，方便了维护者的阅读和理解。

异常处理(又称为错误处理)功能提供了处理程序运行时出现的任何意外或异常情况的方法。异常处理使用try、catch 和 finally关键字来尝试可能未成功的操作，处理失败，以及在事后清理资源。



![image-20210409120656936](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210409120656936.png)

在Java中，所有的异常都有一个共同的祖先`java.lang `包中的`Throwable`类。`Throwable`类有两个重要的子类`Exception`(异常)和`Error`(错误)。`Exception`能被程序本身处理`(try-catch)`，`Error` 是无法处理的(只能尽量避免)。

`Exception` 和`Error` 二者都是Java异常处理的重要子类，各自都包含大量子类。

- `Exception`：程序本身可以处理的异常，可以通过`catch `来进行捕获。`Exception`又可以分为受检查异常(必须处理)和不受检查异常(可以不处理)。
- `Error `：属于程序无法处理的错误，我们没办法通过`catch`来进行捕获。例如，Java 虚拟机运行错误(`Vitual MachineErro`r)、虚拟机内存不够错误(`OutOfMemoryError`)、类定义错误(`NoClassDefFoundError`)等。这些异常发生时，Java虚拟机(JVM)一般会选择线程终止。

##### 受检查异常

Java代码在编译过程中，如果受检查异常没有被`catch/throw`处理的话，就没办法通过编译。

除了`RuntimeException`及其子类以外，其他的`Exception`类及其子类都属于检查异常。常见的受检查异常有：I0相关的异常、`ClassNotFoundException`、`SQLException`.…。

##### 不受检查异常

Java代码在编译过程中，我们即使不处理不受检查异常也可以正常通过编译。

`RuntimeException `及其子类都统称为非受检查异常，例如：`NulPointExecrption`、`NumberFormatException`(字符串转换为数字)、`ArrayIndexOutOfBoundsException`(数组越界)、`ClassCastException`(类型转换错误)、`ArithmeticException`(算术错误)等。

##### Throwable类常用方法

- **`public string getMessage()`：**返回异常发生时的简要描述
- **`public string toString()`：**返回异常发生时的详细信息
- **`public string getLocalizedMessage()`：**返回异常对象的本地化信息。使用`Throwable`的子类覆盖这个方法，可以生成本地化信息。如果子类没有覆盖该方法，则该方法返回的信息与`getMessage()`返回的结果相同
- **`public void printStackTrace()`：**在控制台上打印Throwable对象封装的异常信息

##### 异常处理总结

- **try块：**用于捕获异常。其后可接零个或多个`catch`块，如果没有`catch`块，则必须跟`一个finally`块。
- **catch块：**用于处理`try`捕获到的异常。
- **finally 块**：无论是否捕获或处理异常，`finally`块里的语句都会被执行。当在`try `块或`catch` 块中遇到`return`语句时，`finally` 语句块将在方法返回之前被执行。

![image-20210409162647943](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210409162647943.png)

***当`try`和`finally`语句中都有`return`语句时，`finally`语句的返回值会覆盖`try`语句块的返回值。***

##### throw和throws的区别

- > throws：用来声明一个方法可能产生的所有异常，不做任何处理而是将异常往上传，谁调用我我就抛给谁。
  >  用在方法声明后面，跟的是**异常类名**
  >  可以跟多个异常类名，用逗号隔开
  >  表示抛出异常，由该方法的调用者来处理
  >  throws表示出现异常的一种可能性，并不一定会发生这些异常

- > throw：则是用来抛出一个**具体的**异常类型。
  > 用在方法体内，跟的是异常对象名
  > **只能抛出一个异常**对象名
  > 表示抛出异常，由方法体内的语句处理
  > throw则是抛出了异常，执行throw则一定抛出了某种异常  

##### 自定义异常

**使用情景：**

​		很多时候项目会出现特有问题，而这些问题并未被 java 所描述并封装成对象，所以对于这些特有的问题可以按照java的对问题封装的思想，将特有的问题进行自定义异常封装。在 Java 中要想创建自定义异常，需要继承 `Throwable` 或者他的子类`Exception`。

**语法：**

```java
class 自定义异常类 extends 异常类型(Exception){

 // 因为父类已经把异常信息的操作都完成了，所在子类只要在构造时，将异常信息传递给父类通过super 语句即可。
  // 重写 有参 和 无参  构造方法
}
```

**例子：**

```java
public class CustomException extends Exception {

	//无参构造方法
	public CustomException(){
		super();
	}
	
	//有参的构造方法
	public CustomException(String message){
		super(message);
	}
	
	// 用指定的详细信息和原因构造一个新的异常
	public CustomException(String message, Throwable cause){
		super(message,cause);
	}
	
	//用指定原因构造一个新的异常
	 public CustomException(Throwable cause) {
		 super(cause);
	 }
}
```

**自定义异常的使用例子:**

自定义 `test1()` 方法，抛出 "我喝酒了"的异常信息，`test2()` 方法调用 `test1()` 方法，并将异常包装成`RuntimeException` 类型的异常，继续抛出，在main方法中调用test2()方法，并尝试捕获异常

```java
public void test2() {
		
		try{
			test1();
		}catch (CustomException e){
		   RuntimeException exception  =  new RuntimeException(e);
		   //exception.initCause(cause)
		   throw  exception;
		}
	}
	
	public void test1() throws CustomException{
		throw new CustomException("我喝酒了");
	}
// main方法
	public static void main(String[] args) {
		
		CustomExceptionInital object =  new  CustomExceptionInital();
		//try{
			object.test2();
		//}catch(Exception e){
		//e.printStackTrace();
		//}
	}
```



******************

#### 21、用键盘输入的两种方法

##### Scanner()

```java
Scanner input = new Scanner(System.in);
String s = input.nextLine();
input.close();
```

##### BufferedReader()

```java
BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
String s = input.readLine();
```

#### ==22、Java中IO流==

##### Java中IO流分为几种

- 按照流的流向分，可以分为输入流和输出流；

- 按照操作单元划分，可以划分为字节流和字符流；

- 按照流的角色划分为节点流和处理流。

  

Java IO 流共涉及40多个类，这些类看上去很杂乱，但实际上很有规则，而且彼此之间存在非常紧密的联系，Javal0流的40多个类都是从如下4个抽象类基类中派生出来的。

- `InputStream/Reader`：所有的输入流的基类，前者是字节输入流，后者是字符输入流。
- `OutputStream/Writer`：所有输出流的基类，前者是字节输出流，后者是字符输出流。

##### 有了字节流，为什么还要有字符流？

问题本质想问：**不管是文件读写还是网络发送接收，信息的最小存储单元都是字节，那为什么I/O流操作要分为字节流操作和字符流操作呢？**
回答：**字符流是由Java虚拟机将字节转换得到的**，问题就出在这个过程还算是非常耗时，并且，如果我们不知道编码类型就很容易出现乱码问题。所以，I/O流就干脆提供了一个直接操作字符的接口，方便我们平时对字符进行流操作。如果音频文件、图片等媒体文件用字节流比较好，如果涉及到字符的话使用字符流比较好。

##### ==BIO，NIO，AIO有什么区别？==

IO模型就是说用什么样的通道进行数据的发送和接收，Java 共支持3种网络编程 IO 模式：`BIO`、`NIO`、`AIO`。Java 中的 BIO、NIO 和 AIO 理解为是 Java 语言对操作系统的各种 IO 模型的封装。我们在使用这些 API 的时候，不需要关心操作系统层面的知识，也不需要根据不同操作系统编写不同的代码。

在讲 BIO、NIO、AIO 之前先回顾几个概念：同步与异步、阻塞与非阻塞、I/O模型。

**同步与异步：**

- **同步**：同步就是发起一个调用后，被调用者未处理完请求之前，调用不返回。
- **异步**：异步就是发一个调用后，立刻得到被调用者的回应表示已接收到请求，但是被调用者并没有返回结果，此时可以处理其他的请求，被调用者通常依靠事件、回调等机制来通知调用者其返回结果。

> 同步和异步的区别最大在于**异步的话调用者不需要等待结果处理**，被调用者会通过回调等机制来通知调用者返回结果。

**阻塞与非阻塞**

阻塞与非阻塞是描述进程在访问某个资源时，数据是否准备就绪的的一种处理方式。当数据没有准备就绪时：

- **阻塞**：阻塞就是发起一个请求，调用者一直等待请求结果返回，也就是当前线程会被挂起，无法从事其他任务，只有当条件就绪才能继续。
- **非阻塞**：非阻塞就是发起一个请求，调用者不用一直等着结果返回，可以先去干其他的事情。

**举例：**

```markdown
1. 老张把水壶放到火上，立等水开。**(同步阻塞)**
2. 老张把水壶放到火上，去客厅看电视，时不时去厨房看看水开没有。**(同步非阻塞)**
3. 老张把响水壶放到火上，立等水开。**(异步阻塞) **
4. 老张把响水壶放到火上，去客厅看电视，水壶响之前不再去看它了，响了再去拿壶。** (异步非阻塞) **
```



- **BIO(BlockIng I/O)：同步阻塞I/O模式**，**数据的读取写入必须阻塞在一个线程内等待其完成**

  基于流模型实现的，交互的方式是同步、阻塞方式，也就是说在读入输入流或者输出流时，在读写动作完成之前，线程会一直阻塞在那里，它们之间的调用是可靠的线性顺序。
  
  在活动连接数不是特别高(小于单机1000)的情况下，这种模型是比较不错的，可以让每一个连接专注于自己的I/O并且编程模型简单，也不用过多考虑系统的过载、限流等问题。线程池本身就是一个天然的漏斗，可以缓冲一些系统处理不了的连接或请求。但是，当面对十万甚至百万级连接的时候，传统的BIO模型是无能为力的。因此，我们需要一种更高效的I/O处理模型来应对更高的并发量。

- **NIO(Non-blocking/New I/O)：**NIO是一种**同步非阻塞的I/0模型**，在Java1.4中引入了NIO框架，对应`java.nio`包，大核心部分：Channel(通道)，Buffer(缓冲区), Selector(多路复用器)。NIO中的N可以理解为Non-blocking，不单纯是New。它支持面向缓冲的，基于==通道==的I/O操作方法。NIO提供了与传统BIO模型中的`Socket` 和`ServerSocket` 相对应的`SocketChannel`和`ServerSocketChannel`两种不同的套接字通道实现，两种通道都支持阻塞和非阻塞两种模式。阻塞模式使用就像传统中的支持一样，比较简单，但是性能和可靠性都不好；非阻塞模式正好与之相反。对于低负载、低并发的应用程序，可以使用同步阻塞I/O来提升开发速率和更好的维护性；对于高负载、高并发的(网络)应用，应使用NIO的非阻塞模式来开发。

  - NIO和传统IO(以下简称IO)之间第一个最大的区别是，**IO是面向流的，NIO是面向缓冲区的。**
  
- **AIO(Asynchronous/O)：**AlO也就是NIO2。在Java 7中引入了NIO的改进版NIO2它是**异步非阻塞的I/0模型**。异步I0是**基于事件和回调机制**实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。AIO是异步IO的缩写，虽然NIO在网络操作中，提供了非阻塞的方法，但是NIO的IO行为还是同步的。对于NIO来说，我们的业务线程是在IO操作准备好时，得到通知，接着就由这个线程自行进行IO操作，IO操作本身是同步的。查阅网上相关资料，我发现就目前来说AIO的应用还不是很广泛。

*************

#### 23、深拷贝与浅拷贝

- 浅拷贝：**创建一个新对象，然后将当前对象的非静态字段复制到该新对象，如果字段是值类型的，那么对该字段执行复制；如果该字段是引用类型的话，则复制引用但不复制引用的对象。因此，原始对象及其副本引用同一个对象。**

  ![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1120165-20180312220935185-85540133.png)

- 深拷贝：**创建一个新对象，然后将当前对象的非静态字段复制到该新对象，无论该字段是值类型的还是引用类型，都复制独立的一份。当你修改其中一个对象的任何内容时，都不会影响另一个对象的内容。**；

  - ![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/1120165-20180313205221201-187008977.png)



- **Object 类提供的 clone 是只能实现 浅拷贝的。**
- 如何实现深拷贝：
  1. 每个引用类型属性内部都重写 `clone()` 方法
     - 但是这种做法有个弊端，这里我们 `Person `类只有一个 `Address `引用类型，而 `Address `类没有，所以我们只用重写 `Address `类的 `clone `方法，但是如果 `Address `类也存在一个引用类型，那么我们也要重写其 `clone `方法，这样下去，有多少个引用类型，我们就要重写多少次，如果存在很多引用类型，那么代码量显然会很大，所以这种方法不太合适。
  2. 利用序列化
     - 序列化是将对象写到流中便于传输，而反序列化则是把对象从流中读取出来。这里写到流中的对象则是原始对象的一个拷贝，因为原始对象还存在 JVM 中，所以我们可以利用对象的序列化产生克隆对象，然后通过反序列化获取这个对象。
     - 每个需要序列化的类都要实现 `Serializable `接口，如果有某个属性不需要序列化，可以将其声明为 `transient`，即将其排除在克隆属性之外。

***********

#### 24、浅克隆与深克隆

克隆：克隆的对象可能包含一些已经修改过的属性, 保留着你想克隆对象的值，而new出来的对象的属性全是一个新的对象，对应的属性没有值，所以我们还要重新给这个对象赋值。即**当需要一个新的对象来保存当前对象的“状态”就靠clone方法了**。那么我把这个对象的临时属性一个一个的赋值给我新 `new `的对象不也行嘛？可以是可以，但是一来麻烦不说，二来，大家通过上面的源码都发现了clone是一个native方法，就是快啊，在底层实现的。

**如何实现克隆**

分三步：

1. 对象的类实现 `Cloneable` 接口；
2. 覆盖 `Object` 类的 `clone()` 方法 (**覆盖 `clone()` 方法，访问修饰符设为public，默认是protected**)；
3. 在 `clone()`方法中调用 `super.clone()`；

**两种不同的克隆方法，浅克隆(ShallowClone)和深克隆(DeepClone)**

**浅克隆**是指拷贝对象时仅仅拷贝对象本身(包括对象中的基本变量)，而不拷贝对象包含的引用指向的对象。

**深克隆**不仅拷贝对象本身，而且拷贝对象包含的引用指向的所有对象。

![img](https://img-blog.csdn.net/2018051911302294?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvdmV6aGFvaGFpbWln/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 25、Java迭代器Iterator

> `Java Iterator`(迭代器)不是一个集合，它是一种用于访问集合的方法，可用于迭代 [ArrayList](https://www.runoob.com/java/java-arraylist.html) 和 [HashSet](https://www.runoob.com/java/java-hashset.html) 等集合。
>
> Iterator 是 Java 迭代器最简单的实现。

迭代器` it` 的基本操作是 `next` 、`hasNext` 和` remove`。

- 调用` it.next()` 会返回迭代器的下一个元素，并且更新迭代器的状态。
- 调用`it.hasNext()` 用于检测集合中是否还有元素。
- 调用` it.remove()` 将迭代器返回的元素删除。

##### Iterator与Iterable

`Iterable`只是返回了`Iterator`接口的一个实例。

> ```java
> public interface Iterable<T> {
>   Iterator<T> iterator();
> }
> 
> public interface Iterator<E> {
>   boolean hasNext();
>   E next();
>   void remove();
> }
> ```

**Iterator 才是那个可以对集合进行迭代的迭代器。** 但不同集合的底层结构不同, 迭代的实现原理也就不同。

例如 `ArrayList` 是数组结构, `LinkedList` 是双向链表。他们就不能使用同一种迭代器。

但针对 `ArrayList` 的迭代器和针对 `LinkedList` 的迭代器有个共同点, 就是他们都需要一些方法: 例如 `hasNext()` 和 `next()`, 我们就可以把这些方法定义在同一个接口 `Iterator` 里, 再分别给 `ArrayList` 和  `LindedList` 找一个实现类来作为各自的迭代器, 分别覆写 `hasNext()` 和 `next()` 。

jdk 中给 `ArrayList` 找的实现类叫作 `Itr`, 他实现了 `Iterator` 接口, 针对 `ArrayList` 的结构覆写了 `hasNext()` 和 `next()`, 这个类除了迭代 `ArrayList` 之外没有其他作用, 所以 jdk 中把 `Itr` 写成了一个 `ArrayList` 私有的内部类。

既然私有, 就需要提供一个对外可以获取实例的方法, 这个方法叫作 `iterator()`。同样的, `LinkedList`, `TreeSet`, `HashSet` 等其他集合也需要通过这种方法获取迭代器实例, **我们就把这个方法定义在  `Iterable `接口中**, 让以上集合直接或间接实现 `Iterable` 接口, 覆写 `iterator()` 方法。

##### collections为什么不直接继承iterator接口

因为 `Iterator` 接口的核心方法 `next()` 或者 `hasNext()` 是**依赖于迭代器的当前迭代位置的。**如果Collection直接继承`Iterator` 接口，势必导致集合对象中包含当前迭代位置的数据(指针)。当集合在不同方法间被传递时，由于当前迭代位置不可预置，那么 `next()` 方法的结果会变成不可预知。除非再为 `Iterator` 接口添加一个 ·reset() 方法，用来重置当前迭代位置。但即使这样，`Collection` 也只能同时存在一个当前迭代位置。而 `Iterable` 则不然，每次调用都会返回一个从头开始计数的迭代器。多个迭代器是互不干扰的。

#### 26、jdbc使用的是什么jar包，以及jdbc中使用了哪些类？

- **`java.sql`**包：所有与JDBC访问数据库相关的数据和接口都在此包之中。
  - DriverManager;是一个类 , 驱动管理功能,注册驱动,获取数据库链接对象
  - Connection;是一个接口 , 连接对象,用于创建执行sql对象Statement, PreparedStatement对象
  - Statement;是一个接口, sql语句执行对象,用于将sql语句发送个数据库服务器
  - PreparedStatement: sql语句执行对象
  - ResultSet :是一个接口,结果集,把数据库查询出来的数据封装到集合里面
- **`javax.sql`**包:用到数据库连接池,是数据库扩展包,提供数据库一些额外操作,比如:连接池

**[介绍JDBC快速入门使用到的包和类](https://www.cnblogs.com/tushao/articles/14243357.html)**

```java
package jdbc;

import java.sql.*;

/**
 * @author LYJ
 * @create 2021-06-20 11:01
 * 我的第一个JDBC程序
 */
public class jdbcDemo1 {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        /**
         * 1、加载驱动
         * 固定写法
         * MySQL 8.0以上: Class.forName("com.mysql.cj.jdbc.Driver")
         *      以下: Class.forName("com.mysql.jdbc.Driver")
         */
        Class.forName("com.mysql.cj.jdbc.Driver");

        /**
         * 2、用户信息和url
         * useUnicode=true&characterEncoding=utf8&useSSL=true&serverTimezone=UTC
         */
        String url = "jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true";
        String username = "root";
        String password = "7012+2";

        /**
         * 3、连接成功，创建数据库对象
         * connection 代表数据库
         */
        Connection connection = DriverManager.getConnection(url, username, password);

        /**
         * 4、执行SQL对象
         * statement执行SQL语句
         */
        Statement statement = connection.createStatement();

        /**
         * 5、执行SQL语句
         */
        String sql ="SELECT * FROM users";
        ResultSet resultSet = statement.executeQuery(sql);
        while (resultSet.next()){
            System.out.println("id=\t" + resultSet.getObject("id"));
            System.out.println("name=\t" + resultSet.getObject("NAME"));
            System.out.println("password=\t" + resultSet.getObject("PASSWORD"));
            System.out.println("email=\t" + resultSet.getObject("email"));
            System.out.println("birthday=\t" + resultSet.getObject("birthday"));
            System.out.println("========================");
        }

        /**
         * 6、释放连接
         */
        resultSet.close();;
        statement.close();
        connection.close();
    }
}
```

#### 27、`Arrays.sort()` 用的什么排序算法

- 数组大小小于 `MAX_INSERTION_SORT_SIZE`(47) 时用插入排序；
- 数组大小大于 `MAX_INSERTION_SORT_SIZE`  小于 `QUICKSORT_THRESHOLD`(286) 时用快速排序
- 大于 `QUICKSORT_THRESHOLD` 时用归并排序；
  - `Check if the array is nearly sorted`。这里第一个作用是先梳理一下数据方便后续的归并排序，第二个作用就是即便大于286，但在降序组太多的时候(被判断为没有结构的数据，The array is not highly structured,use Quicksort instead of merge sort.)，要转回快速排序。

#### 28、Java中new和反射创建对象的区别

###### 28.1、new方法

```java
A a = new A();
```

在JVM中会帮你做的事情有以下：

- JVM把类的 `.java` 文件编译为一个 `.class` 的字节码文件
- 类加载器把 `.class` 文件加载进 jvm 的内存中，一个 Class 对象生成，并放入方法区中，这 Class 对象对于任何类都是唯一一个。

做完这些之后，才是new字段的工作：

- 判断内存中是否已经有那个唯一的Class对象
- 如果没有，则进行上述操作。
- 如果有，则在内存中申请空间开辟，即根据Class对象获取有关的构造器进行实例化，在这里我们假设是一个无参数构造，那么只需要``newInstance()``。

###### 28.2、反射方法

```java
Class c = Class.forName("A的全类名");
```

当JVM编译到这段代码的时候，他的步骤是：
1、使用类加载，将对应.class加载入内存的方法区中，并返回Class对象。

这时候，我们可以查看这个类对象里面的构造器方法，并使用无参数构造器进行构造实例,即如下代码

```java
Constructor constructor = c.getConstructor();
Object obj = constructor.newInstance();
```

到这里，我们几乎可以知道**无论是反射，还是 `new`，其实都是通过类加载器对 `.class`文件加载进内存中，创建了 `Class` 对象。**‘’



==Java中反射属于动态编译，而new属于静态编译。==

1、静态编译相当于把所有需要的东西都在初始化的时候加载了，如果程序一大，就很有可能会跑得慢。

2、动态编译，在编译的时候，需要的模块都没有编译进去，启动程序的时候，模块不会被加载而是在运行的时候，需要哪个模块就调用哪个模块。

上面的过程告诉我们，我们如果用new，那么我们要创建的类都是已经“写死”在.class文件里面了，我们无法控制JVM帮我们加载类的这一项工作。

但是如果我们用反射创建类对象，我们是相当于亲自“指导”JVM，我们“按需加载”`.class` 文件，如果内存里面没有这个类的.class文件，那么我们用 `Class.forName()`去叫类加载器帮忙加载就行了，而不是把程序停下来，再打一段代码，再让类加载器进行加载，从而体现出了Java的“动态性”
