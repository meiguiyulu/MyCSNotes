

### Java集合

#### List, Set, Map

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20180612094225630)

- List , Set, Map 都是接口，前两个继承至Collection接口，Map为独立接口;
- Set下有HashSet，LinkedHashSet，TreeSet;
- List下有ArrayList，Vector，LinkedList;
- Map下有Hashtable，LinkedHashMap，HashMap，TreeMap;
- Collection接口下还有个Queue接口，有PriorityQueue类。

**注意:**

- Queue接口与List、Set同一级别，都是继承了Collection接口。
  看图你会发现,LinkedList既可以实现Queue接口,也可以实现List接口。只不过呢, LinkedList实现了Queue接口。Queue接口窄化了对LinkedList的方法的访问权限(即在方法中的参数类型如果是Queue时，就完全只能访问Queue接口所定义的方法 了，而不能直接访问 LinkedList的非Queue的方法)，以使得只有恰当的方法才可以使用。
- SortedSet是个接口，它里面的(只有TreeSet这一个实现可用)中的元素一定是有序的。

##### **总结:**

**List 有序,可重复**

>  - **ArrayList**
>    - 优点: 底层数据结构是数组，查询快，增删慢。
>    - 缺点: 线程不安全，效率高
>- **Vector**
>    - 优点: 底层数据结构是数组，查询快，增删慢。
>  - 缺点: 线程安全，效率低
>  - **LinkedList**
>    - 优点: 底层数据结构是链表，查询慢，增删快。
>    - 缺点: 线程不安全，效率高

**Set 无序,唯一**

> - **HashSet**
>   - 底层数据结构是哈希表。(无序,唯一)
>   - 如何来保证元素唯一性?
>     - 依赖两个方法：hashCode()和equals()
> - **LinkedHashSet**
>   - 底层数据结构是链表和哈希表。(FIFO插入有序,唯一)
>     - 由链表保证元素有序
>     - 由哈希表保证元素唯一
> - **TreeSet**
>   - 底层数据结构是红黑树。(唯一，有序)
>   - **如何保证元素排序的呢?**
>     - 自然排序
>     - 比较器排序
>   - **如何保证元素唯一性的呢?**
>     - 根据比较的返回值是否是0来决定

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20180612135157564)

Map接口有三个比较重要的实现类，分别是HashMap、TreeMap和HashTable。

TreeMap是有序的，HashMap和HashTable是无序的。==**HashMap散列图、HashTable散列表是按“有利于随机查找的散列(hash)的顺序”排列的。并非按输入顺序。遍历时只能全部输出，而没有顺序**==。甚至可以rehash()重新散列，来获得更利于随机存取的内部顺序。

> ==TreeMap是按键排序的，LinkedHashMap(LinkedHaseSet)是按插入顺序排队的。==

**Hashtable的方法是同步的，HashMap的方法不是同步的。这是两者最主要的区别。**

这就意味着:

> - Hashtable是线程安全的，HashMap不是线程安全的。
> - HashMap效率较高，Hashtable效率较低。
> - 如果对同步性或与遗留代码的兼容性没有任何要求，建议使用HashMap。 查看Hashtable的源代码就可以发现，除构造函数外Hashtable的所有 public 方法声明中都有 synchronized关键字，而HashMap的源码中则没有。
> - Hashtable不允许null值，HashMap允许null值(key和value都允许)
> - 父类不同：Hashtable的父类是Dictionary，HashMap的父类是AbstractMap

> ![image-20210410093645153](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410093645153.png)
>
> ![image-20210410093654567](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410093654567.png)

#### Java如何确保集合不被修改

可以使用 `Collections.unmodifiableCollection(Collection c)` 方法来创建一个==只读==集合，这样改变集合的任何操作都会抛出 `Java.lang.UnsupportedOperationException` 异常。

- `Collections.unmodifiableList`；
- `Collections.unmodifiableMap`；
- `Collections.unmodifiableSet`。

#### List、Set、Map线程不安全

==`java.util.ConcurrentModificationException` 并发修改异常==

##### List

> ```java
> 解决方案:
> 1. List<String> list = new Vector<>(); (Vector使用了synchronized，不推荐)
> 2. List<String> list = Collections.synchronizedList(new ArrayList<>());
> 3. List<String> list = new CopyOnWriteArrayList<>();
> ```

##### Set

> ```java
> 解决方案:
> 1. Collections.synchronizedSet()  通过工具类转化为synchronized
>  	Set<String> set = Collections.synchronizedSet(new HashSet<>());
> 2. CopyOnWriteArraySet  写入时复制
> 	Set<String> set = new CopyOnWriteArraySet<>();
> ```

##### Map

> ```java
> 解决方案:
> 	1. HashTable() (不推荐使用)
> 	2. Collections.synchronizedMap() (不推荐使用)
> 	3. Map<String, String> map = new ConcurrentHashMap<>();
> ```

*************************

#### `java.util.ConcurrentModificationException`

下面这个代码会抛出异常：

```java
import java.util.ArrayList;
import java.util.List;

public class TestList {
    public static void main(String[] args) {
        List<User> list = new ArrayList<>();
        list.add(new User("name1", 24));
        list.add(new User("name2", 24));
        list.add(new User("name3", 24));
        list.add(new User("name4", 24));
        list.add(new User("name5", 24));

        for (User user: list){
            if (user.getName().equals("name2")){
                list.remove(user);
            }
            System.out.println(user);
        }
        
        // 或者
        Iterator<User> iterator = users.iterator();
        while(iterator.hasNext()) {
            User user = iterator.next();
            if(user.getName().equals("name2")) {
                users.remove(user);
            }
            System.out.println(user);
        }
    }
}
```

![image-20210621190242774](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210621190242774.png)

##### 分析

**简单来说：**

​		遍历的方式是用Itr去遍历的，这个Itr是ArrayList实现的一个遍历接口、内部类；但是我在删除的时候是通过ArrayList的remove方法去操作的，不是Itr内部的那个删除方法去操作的。ArrayList的remove方法修改的变量是继承自AbstractList的变量modeCount；而Itr的remove方法修改的是自身的变量expectedModCount。

​		所以，在用ArrayList的remove方法进行删除操作以后，Itr里面的expectedModCount会与ArrayList的modCount进行比较，二者不相等，所以会抛错。

**详细分析：**

​		首先看 `ArrayList` 的 `iterator()` 方法的具体实现，查看源码发现在 `ArrayList` 的源码中并没有 `iterator()` 这个方法，那么很显然这个方法应该是其父类或者实现的接口中的方法，我们在其父类 `AbstractList` 中找到了 `iterator()` 方法的具体实现，下面是其实现代码：

```java
private class Itr implements Iterator<E> {
    int cursor = 0;
    int lastRet = -1;
    int expectedModCount = modCount;
    public boolean hasNext() {
           return cursor != size();
    }
    public E next() {
           checkForComodification();
        try {
        E next = get(cursor);
        lastRet = cursor++;
        return next;
        } catch (IndexOutOfBoundsException e) {
        checkForComodification();
        throw new NoSuchElementException();
        }
    }
    public void remove() {
        if (lastRet == -1)
        throw new IllegalStateException();
           checkForComodification();
 
        try {
        AbstractList.this.remove(lastRet);
        if (lastRet < cursor)
            cursor--;
        lastRet = -1;
        expectedModCount = modCount;
        } catch (IndexOutOfBoundsException e) {
        throw new ConcurrentModificationException();
        }
    }
 
    final void checkForComodification() {
        if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
    }
}
```

首先我们看一下它的几个成员变量：

- `cursor`：表示下一个要访问的元素的索引，从next()方法的具体实现就可看出
- `lastRet`：表示上一个访问的元素的索引
- `expectedModCount`：表示对ArrayList修改次数的期望值，它的初始值为modCount。
- `modCount`是AbstractList类中的一个成员变量

```java
protected transient int modCount = 0;
// 该值表示对List的修改次数，查看ArrayList的add()和remove()方法就可以发现，每次调用add()方法或者remove()方法就会对modCount进行加1操作。
```

到这里我们再看看上面的程序：

当调用 `list.iterator()` 返回一个 `Iterator` 之后，通过I `terator` 的 `hashNext()` 方法判断是否还有元素未被访问，我们看一下 `hasNext()` 方法，`hashNext()` 方法的实现很简单：

```java
public boolean hasNext() {
    return cursor != size();
}
```

如果下一个访问的元素下标不等于ArrayList的大小，就表示有元素需要访问，这个很容易理解，如果下一个访问元素的下标等于ArrayList的大小，则肯定到达末尾了。

然后通过Iterator的next()方法获取到下标为0的元素，我们看一下next()方法的具体实现：

```java
public E next() {
    checkForComodification();
 try {
    E next = get(cursor);
    lastRet = cursor++;
    return next;
 } catch (IndexOutOfBoundsException e) {
    checkForComodification();
    throw new NoSuchElementException();
 }
}
```

首先在next()方法中会调用checkForComodification()方法，然后根据cursor的值获取到元素，接着将cursor的值赋给lastRet，并对cursor的值进行加1操作。初始时，cursor为0，lastRet为-1，那么调用一次之后，cursor的值为1，lastRet的值为0。注意此时，modCount为0，expectedModCount也为0。

接着往下看，程序中判断当前元素的值是否为2，若为2，则调用list.remove()方法来删除该元素。

```java
public boolean remove(Object o) {
    if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                fastRemove(index);
                return true;
            }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}
 
 
private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                numMoved);
    elementData[--size] = null; // Let gc do its work
}
```

通过 `remove` 方法删除元素最终是调用的 `fastRemove()` 方法，在 `fastRemove()` 方法中，首先对 `modCount` 进行加1操作(因为对集合修改了一次)，然后接下来就是删除元素的操作，最后将size进行减1操作，并将引用置为null以方便垃圾收集器进行回收工作。

那么注意此时各个变量的值：对于iterator，其expectedModCount为0，cursor的值为1，lastRet的值为0。

对于list，其modCount为1，size为0。

接着看程序代码，执行完删除操作后，继续while循环，调用hasNext方法()判断，由于此时cursor为1，而size为0，那么返回true，所以继续执行while循环，然后继续调用iterator的next()方法：

注意，此时要注意next()方法中的第一句：checkForComodification()。

在checkForComodification方法中进行的操作是：

```java
final void checkForComodification() {
    if (modCount != expectedModCount)
    throw new ConcurrentModificationException();
}
```

如果modCount不等于expectedModCount，则抛出ConcurrentModificationException异常。

很显然，此时modCount为1，而expectedModCount为0，因此程序就抛出了ConcurrentModificationException异常。

到这里，想必大家应该明白为何上述代码会抛出ConcurrentModificationException异常了。

关键点就在于**：调用list.remove()方法导致modCount和expectedModCount的值不一致。**

##### 解决方法

**单线程下：**

使用迭代器的 `remove` 方法

```java
public class Test {
    public static void main(String[] args)  {
        ArrayList<Integer> list = new ArrayList<Integer>();
        list.add(2);
        Iterator<Integer> iterator = list.iterator();
        while(iterator.hasNext()){
            Integer integer = iterator.next();
            if(integer==2)
                iterator.remove();   //注意这个地方
        }
    }
}
```

Itr类中也给出了一个 `remove()` 方法：

```java
public void remove() {
    if (lastRet == -1)
    throw new IllegalStateException();
       checkForComodification();
 
    try {
    AbstractList.this.remove(lastRet);
    if (lastRet < cursor)
        cursor--;
    lastRet = -1;
    expectedModCount = modCount; // 多了这么一行操作
    } catch (IndexOutOfBoundsException e) {
    throw new ConcurrentModificationException();
    }
}
```

**多线程下：**

```java
public class Test {
    static ArrayList<Integer> list = new ArrayList<Integer>();
    public static void main(String[] args)  {
        list.add(1);
        list.add(2);
        list.add(3);
        list.add(4);
        list.add(5);
        Thread thread1 = new Thread(){
            public void run() {
                Iterator<Integer> iterator = list.iterator();
                while(iterator.hasNext()){
                    Integer integer = iterator.next();
                    System.out.println(integer);
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            };
        };
        Thread thread2 = new Thread(){
            public void run() {
                Iterator<Integer> iterator = list.iterator();
                while(iterator.hasNext()){
                    Integer integer = iterator.next();
                    if(integer==2)
                        iterator.remove(); 
                }
            };
        };
        thread1.start();
        thread2.start();
    }
}
```

有可能有朋友说ArrayList是非线程安全的容器，换成Vector就没问题了，实际上换成Vector还是会出现这种错误。

　　原因在于，虽然Vector的方法采用了synchronized进行了同步，但是实际上通过Iterator访问的情况下，每个线程里面返回的是不同的iterator，也即是说expectedModCount是每个线程私有。假若此时有2个线程，线程1在进行遍历，线程2在进行修改，那么很有可能导致线程2修改后导致Vector中的modCount自增了，线程2的expectedModCount也自增了，但是线程1的expectedModCount没有自增，此时线程1遍历时就会出现expectedModCount不等于modCount的情况了。

**因此一般有2种解决办法：**

　　1)在使用iterator迭代的时候使用synchronized或者Lock进行同步；

　　2)使用并发容器CopyOnWriteArrayList代替ArrayList和Vector。

***********************

#### ArrayList与LinkedList

1. **是否保证线程安全：**`ArrayList `和`LinkedList `都是**不同步的，也就是不保证线程安全**；

2. **底层数据结构：`Arraylist` 底层使用的是Object数组**; **`LinkedList` 底层使用的是双向链表**。

3. **插入和删除是否受元素位置的影响：**

   - `ArrayList` 采用数组存储，所以插入和删除元素的时间复杂度受元素位置的影响。比如：执行`add(E e)`方法的时候，`ArrayList` 会默认在将指定的元素追加到此列表的未尾，这种情况时间复杂度就是`O(1)`。但是如果要在指定位置 `i` 插入和删除元素的话`(add(int index，Eelement))`时间复杂度就为`o(n-i)`。因为在进行上述操作的时候集合中第 `i` 和第 `i` 个元素之后的`(n-i)`个元素都要执行向后位/向前移一位的操作。
   - `LinkedList` 采用链表存储，所以对于`add(E e)`方法的插入，删除元素时间复杂度不受元素位置的影响，近似`O(1)`，如果是要在指定位置 `i` 插入和删除元素的话`(add(int index，E element)` 时间复杂度近似为`o(n)`，因为需要先移动到指定位置再插入。

4. **是否支持快速随机访问**：**`LinkedList `不支持高效的随机元素访问，而 `ArrayList `支持。**快速随机访问就是通过元素的序号快速获取元素对象(对应于 `get(int index)` 方法)。

5. **内存空间占用：**`ArrayList `的空间浪费主要体现在在list列表的结尾会预留一定的容量空间，而`LinkedList`的空间花费则体现在它的每一个元素都需要消耗比 `ArrayList `更多的空间(因为要存放直接后继和直接前驱以及数据)。

6. **使用场景**

   ```markdown
   **
   > 如果应用场景对数据有较多的随机访问，ArrayList 要优于 LinkedList;
   > 如果应用场景有更多的插入或者删除操作，较少的随机访问操作，LinkedList 要优于 ArrayList
   # 不过ArrayList的插入、删除不一定比LinkedList慢，如果在ArrayList靠近末尾的地方插入，那么ArrayList只需要移动较少的元素，而LinkedList则需要一直查找到列表尾部，反而耗费较多时间。
   ```

#### 使用ArrayList时需要注意的点

1. 尽量使用 `new ArrayList` 创建 `List `对象，不要用 `Arrays.asList` 创建 `List` 对象，因为 `Arrays.asList` 创建 `List` 对象是通过 `Arrays` 的内部类继承 `AbstractList` 类获取 `List `对象，但是没有对 `AbstractList `类中的 `add`、`remove `等方法进行重写，当使用这些方法时会报 `UnsupportedOperationException()` 异常。
2. **循环遍历的过程中删除集合中的元素**时候，如果遍历方式使用的是 `ltr` ，那么删除的时候不要使用 `ArrayList` 的 `remove` 方法，否则报出 `java.util.ConcurrentModificationException` 异常。

#### ArrayList与Vector

- `Vector`所有方法都是同步的。可以由两个线程安全地访问一个`Vector`对象，但是一个线程访问`Vector`的话代码要在同步操作上耗费大量的时间。
- `ArrayList`不是同步的，所以**不需要保证线程安全时建议使用`ArrayList`**。

#### ArrayList的扩容机制

　`ArrayList `有三种方式来初始化，构造方法源码如下：

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410094118798.png" alt="image-20210410094118798" style="zoom:100%;" />

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410094201349.png" alt="image-20210410094201349" style="zoom:100%;" />

<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410094216725.png" alt="image-20210410094216725" style="zoom:100%;" />



以无参数构造方法创建 ArrayList 时，**实际上初始化赋值的是一个空数组。当真正对数组进行添加元素操作时，才真正分配容量。**即向数组中添加第一个元素时，数组容量扩为10。

以无参构造函数创建的 ArrayList 为例

- <img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410094018347.png" alt="image-20210410094018347" style="zoom:100%;" />

- <img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410094034323.png" alt="image-20210410094034323" style="zoom:100%;" />

- <img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410094328980.png" alt="image-20210410094328980" style="zoom:100%;" />

  ![image-20210410094438604](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410094438604.png)

- <img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410094545249.png" alt="image-20210410094545249" style="zoom:67%;" />

  <img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410094604921.png" alt="image-20210410094604921" style="zoom:80%;" />

- <img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410094653437.png" alt="image-20210410094653437" style="zoom:100%;" />

- ![image-20210410094909609](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410094909609.png)

  ![image-20210410095616190](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410095616190.png)

  > 联系： 看两者源代码可以发现` copyOf() `内部实际调用了` System.arraycopy()` 方法 
  >
  > 区别：` arraycopy() `需要目标数组，将原数组拷贝到你自己定义的数组里或者原数组，而且可以选择拷贝的起点和长度以及放入新数组中的位置；` copyOf()` 是系统自动在内部新建一个数组，并返回该数组。

- <img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410095753873.png" alt="image-20210410095753873" style="zoom:67%;" />

最好在 add 大量元素之前用 ensureCapacity 方法，以减少增量重新分配的次数。

#### 数组和ArrayList的区别

- 数组和 `ArrayList` 的本质区别在于前者是类型安全的，而后者是类型不安全的( `ArrayList` 中插入不同类型的数据是允许).
- ArrayList为了兼容所有类型对象,使用了Object数组，在使用元素的时候会有装箱和拆箱的操作,降低了程序的性能.
- ArrayList会动态扩充容量, 容量为原来的2倍.
- ArrayList只有把元素添加进去之后才可以通过下标访问相应的元素.
- 数组在创建的时候就已经确定了数据类型,并且它的长度是固定的，只能通过下标改变各个元素的值和访问.

****************

#### HashMap和HashTable

1. **线程是否安全：HashMap是非线程安全的，HashTable是线程安全的，因为HashTable内部的方法基本都经过synchronized修饰。**(如果你要保证线程安全的话就使用ConcurrentHashMap吧！)；
2. **效率：**因为线程安全的问题，HashMap要比HashTable效率高一点。另外，**HashTable基本被淘汰，不要在代码中使用它**；
3. **对Null key和Null value的支持**：**HashMap可以存储null的key和value，但null作为键只能有一个，null作为值可以有多个；HashTable不允许有null 键和null值**，否则会抛出NullPointerException。
4. **初始容量大小和每次扩充容量大小的不同**：①创建时如果不指定容量初始值，**Hashtable默认的初始大小为11，之后每次扩充，容量变为原来的2n+1。HashMap默认的初始化大小为16。之后每次扩充，容量变为原来的2倍**。②创建时如果给定了容量初始值，那么Hashtable会直接使用你给定的大小，而HashMap会将其扩充为2的幂次方大小(HashMap中的`tableSizeFor()`方法保证，下面给出了源代码)。也就是说**HashMap总是使用2的幂作为哈希表的大小**，后面会介绍到为什么是2的幂次方。
5. **底层数据结构：**JDK1.8以后的HashMap在解决哈希冲突时有了较大的变化，**当链表长度大于阈值(默认为8)(将链表转换成红黑树前会判断，如果当前数组的长度小于64，那么会选择先进行数组扩容，而不是转换为红黑树)时，将链表转化为红黑树，以减少搜索时间。Hashtable 没有这样的机制，底层实现是数组+链表。**

************

#### HashMap和HashSet

​	`HashSet`底层是基于`HashMap`实现的(Hashset的源码非常非常少，因为除了`clone()`、`writeObject()`、`readObject()`是 `HashSet` 自己不得不实现之外，其他方法都是直接调用 `HashMap` 中的方法。

![image-20210409211817982](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210409211817982.png)

*****************

#### HashSet

##### 1、底层实现

```java
	// HashSet的底层使用的是HashMap
    private transient HashMap<E,Object> map;
    // 由于Set只使用到了HashMap的key，所以此处定义一个静态的Object类常量，来充当HashMap的value
    private static final Object PRESENT = new Object();
```

`HashSet` 是用 `HashMap` 来保存数据，而主要使用到的就是 `HashMap `的 `key`。

```markdown
**
- 这里使用一个静态的Object类对象来充当HashMap的value，既然这里map的value是没有意义的，为什么不直接使用null值来充当value呢？
比如 ` private final Object PRESENT = null ; `
**
**
1. 我们都知道的是，Java首先将变量PRESENT分配在栈空间，而将new出来的Object分配到堆空间，这里的new Object()是占用堆内存的(一个空的Object对象占用8byte)，而null值我们知道，是不会在堆空间分配内存的。那么想一想这里为什么不使用null值。
2. 想到什么吗，看一个异常类 `java.lang.NullPointerException` ， 噢买尬，这绝对是Java程序员的一个噩梦，这是所有Java程序猿都会遇到的一个异常，你看到这个异常你以为很好解决，但是有些时候也不是那么容易解决，Java号称没有指针，但是处处碰到 `NullPointerException`。
3. 为了从根源上避免 `NullPointerException` 的出现，浪费8个byte又怎么样，在下面的代码中我再也不会写这样的代码啦if (xxx == null) { … } else {….}，好爽。
```

##### 2、构造函数

```java
	/**
     * 使用HashMap的默认容量大小16和默认加载因子0.75初始化map，构造一个HashSet
     */
    public HashSet() {
        map = new HashMap<E,Object>();
    }

    /**
     * 构造一个指定Collection参数的HashSet，这里不仅仅是Set，只要实现Collection接口的容器都可以
     */
    public HashSet(Collection<? extends E> c) {
        map = new HashMap<E,Object>(Math. max((int) (c.size()/.75f) + 1, 16));
       // 使用Collection实现的Iterator迭代器，将集合c的元素一个个加入HashSet中
       addAll(c);
    }

    /**
     * 使用指定的初始容量大小和加载因子初始化map，构造一个HashSet
     */
    public HashSet( int initialCapacity, float loadFactor) {
        map = new HashMap<E,Object>(initialCapacity, loadFactor);
    }

    /**
     * 使用指定的初始容量大小和默认的加载因子0.75初始化map，构造一个HashSet
     */
    public HashSet( int initialCapacity) {
        map = new HashMap<E,Object>(initialCapacity);
    }

    /**
     * 不对外公开的一个构造方法(默认default修饰)，底层构造的是LinkedHashMap，dummy只是一个标示参数，无具体意义
     */
    HashSet(int initialCapacity, float loadFactor, boolean dummy) {
        map = new LinkedHashMap<E,Object>(initialCapacity, loadFactor);
}
```

##### 3、增加、删除

```java
	/**
     * 利用HashMap的put方法实现add方法
     */
    public boolean add(E e) {
        return map.put(e, PRESENT)== null;
    }

    /**
     * 利用HashMap的remove方法实现remove方法
     */
    public boolean remove(Object o) {
        return map.remove(o)==PRESENT;
    }

    /**
     * 添加一个集合到HashSet中，该方法在AbstractCollection中
     */
    public boolean addAll(Collection<? extends E> c) {
        boolean modified = false;
       // 取得集合c迭代器Iterator
       Iterator<? extends E> e = c.iterator();
       // 遍历迭代器
        while (e.hasNext()) {
           // 将集合c的每个元素加入到HashSet中
           if (add(e.next()))
              modified = true;
       }
        return modified;
    }
    
    /**
     * 删除指定集合c中的所有元素，该方法在AbstractSet中
     */
    public boolean removeAll(Collection<?> c) {
        boolean modified = false;

        // 判断当前HashSet元素个数和指定集合c的元素个数，目的是减少遍历次数
        if (size() > c.size()) {
            // 如果当前HashSet元素多，则遍历集合c，将集合c中的元素一个个删除
            for (Iterator<?> i = c.iterator(); i.hasNext(); )
                modified |= remove(i.next());
        } else {
            // 如果集合c元素多，则遍历当前HashSet，将集合c中包含的元素一个个删除
            for (Iterator<?> i = iterator(); i.hasNext(); ) {
                if (c.contains(i.next())) {
                    i.remove();
                    modified = true;
                }
            }
        }
        return modified;
}
```

##### 4、是否包含

```java
	/**
     * 利用HashMap的containsKey方法实现contains方法
     */
    public boolean contains(Object o) {
        return map.containsKey(o);
    }
      
    /**
     * 检查是否包含指定集合中所有元素，该方法在AbstractCollection中
     */
    public boolean containsAll(Collection<?> c) {
       // 取得集合c的迭代器Iterator
       Iterator<?> e = c.iterator();
       // 遍历迭代器，只要集合c中有一个元素不属于当前HashSet，则返回false
        while (e.hasNext())
           if (!contains(e.next()))
               return false;
        return true;
}
```

既然 `HashSet` 的 `contains` 方法调用的是 `Hashmap` 的 `containsKey`，那么 `HashSet` 的 `contains` 的时间复杂度应该跟`Hashmap` 的 `containsKey`是一样的。那么`Hashmap` 的 `containsKey`的是多少呢？

```java
// containsKey方法
public boolean containsKey(Object key) {
        return getNode(hash(key), key) != null; // 调用了getNode方法
}

final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
			// 直接命中
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
			// 未命中
            if ((e = first.next) != null) {
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }

```

```markdown
**
所以 ` tab[(n - 1) & hash] ` 执行了如下操作：
  # 1. 指针first指向那一行数组的引用(那一行数组是通过table下标范围n-1和key的hash值计算出来的)，若命中，则通过下标访问数组，时间复杂度为O(1)
  #  2. 如果没有直接命中(key进行hash时，产生相同的位运算值)，存储方式变为红黑树，那么遍历树的时间复杂度为O(lgn
```

##### 5、HashSet如何检保证元素不重复

我们先看一下 `HashSet` 的 `add()` 方法：

```java
	/**
     * 利用HashMap的put方法实现add方法
     */
    public boolean add(E e) {
        return map.put(e, PRESENT)== null;
    }
```

调用的是 `HashMap` 的 `put()` 方法：

```java
 public V put(K key, V value) {
        if (key == null)
            return putForNullKey(value);
        int hash = hash(key.hashCode());
        int i = indexFor(hash, table.length);
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }
        modCount++;
        addEntry(hash, key, value, i);
        return null;
    }
```

​		**总结：**当你把对象加入`HashSet`时，`HashSet` 会先计算对象的`hashcode`值来判断对象加入的位置，同时也会与其他加入的对象的`hashcode`值作比较：

- 如果没有相符的`hashcode`，说明是一个新元素，保存。
- 如果发现有相同` hashcode `值的对象，这时会调用`equals()`方法来检查`hashcode` 相等的对象是否真的相同。
  - 如果两者相同，说明元素已经存在，`HashSet `就不会让加入操作成功。
  - 如果两者不同，说明是新的元素，保存。

###### 5.1、`hashCode()`与`equals()`的相关规定：

1. 如果两个对象相等，则`hashcode()`一定也是相同的
2. 两个对象相等，对两个`equals()`方法返回 `true`
3. 两个对象有相同的`hashcode()`值，它们也不一定是相等的
4. `equals()`方法被覆盖过，则`hashCode()`方法也必须被覆盖
5. `hashCode()`的默认行为是对堆上的对象产生独特值。如果没有重写`hashCode()`，则该class的两个对象无论如何都不会相等(即使这两个对象指向相同的数据)。

###### 5.2、==与equals的区别

- 对于基本类型来说，`== `比较的是值是否相等；
- 对于引用类型来说，`==`比较的是两个引用是否指向同一个对象地址(两者在内存中存放的地址(堆内存地址)是否指向同一个地方)；
- 对于引用类型(包括包装类型)来说，`equals`如果没有被重写，对比它们的地址是否相等；如果`equals()`方法被重写(例如String)，则比较的是地址里的内容。

#### ==HashMap==

##### 1、底层数据结构

> **数组 + 链表 + 红黑树**

**红黑树的特性**:

1. **每个节点或者是黑色，或者是红色。**
2. **根节点是黑色。**
3. **叶子节点(NIL)是黑色。 [注意：这里叶子节点，是指为空(NIL或NULL)的叶子节点！]**
4. **如果一个节点是红色的，则它的子节点必须是黑色的。**
5. **从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点**

主要存储有序的数据，==时间复杂度是O(lgn)，效率非常之高。==

##### 2、HashMap中的元素类型

`HashMap` 中是以 **哈希桶：`Node[] table`**来存储的。

```java
transient Node<K,V>[] table;
```

**`table`** 是一个`Node`类 的数组。

###### Node类

Node类，翻译过来就是节点，是定义在HashMap中的一个内部类，实现了`Map.Entry`接口。

Node类的基本属性有：

```markdown
**
1. hash ：key的哈希值
2. key：节点的key，类型和定义HashMap时的key相同
3. value：节点的value，类型和定义HashMap时的value相同
4. next：该节点的下一节点
```

table在==第一次往HashMap中put元素的时候初始化(懒加载机制)==。

当链表数组的容量超过初始容量的0.75时，再散列将链表数组扩大2倍，把原链表数组的搬移到新的数组中。**默认大小为16，加载因子为0.75**

```java
    /**
     * The default initial capacity - MUST be a power of two.
     */
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

    /**
     * The maximum capacity, used if a higher value is implicitly specified
     * by either of the constructors with arguments.
     * MUST be a power of two <= 1<<30.
     */
    static final int MAXIMUM_CAPACITY = 1 << 30;

    /**
     * The load factor used when none specified in constructor.
     */
    static final float DEFAULT_LOAD_FACTOR = 0.75f;

    /**
     * The bin count threshold for using a tree rather than list for a
     * bin.  Bins are converted to trees when adding an element to a
     * bin with at least this many nodes. The value must be greater
     * than 2 and should be at least 8 to mesh with assumptions in
     * tree removal about conversion back to plain bins upon
     * shrinkage.
     */
// 为什么是8? 当hashCode离散性很好的时候，红黑树用到的概率非常小，因为数据均匀分布在每个数组的每个元素(bin)中，几乎不会有bin中链表长度会达到阈值。但是在随机hashCode下，离散性可能会变差，然而JDK又不能阻止用户实现这种不好的hash算法，因此就可能导致不均匀的数据分布。不过理想情况下随机hashCode算法下数组中所有节点的分布频率会遵循泊松分布，我们可以看到，一个bin中链表长度达到8个元素的概率为0.00000006，几乎是不可能事件。
    static final int TREEIFY_THRESHOLD = 8;

    /**
     * The bin count threshold for untreeifying a (split) bin during a
     * resize operation. Should be less than TREEIFY_THRESHOLD, and at
     * most 6 to mesh with shrinkage detection under removal.
     */
    static final int UNTREEIFY_THRESHOLD = 6;

    /**
     * The smallest table capacity for which bins may be treeified.
     * (Otherwise the table is resized if too many nodes in a bin.)
     * Should be at least 4 * TREEIFY_THRESHOLD to avoid conflicts
     * between resizing and treeification thresholds.
     */
    static final int MIN_TREEIFY_CAPACITY = 64;
```

****



JDK 1.8之前`HashMap`底层是**数组**和**链表**结合在一起使用也就是链表散列。`HashMap`通过`key`的`hashCode`经过扰动函数处理过后得到hash值，然后通过`(n-1)&hash`判断当前元素存放的位置(这里的n指的是数组的长度)，如果当前位置存在元素的话就判断该元素与插入的元素的hash值以及key 是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决。

> 所谓扰动函数指的就是HashMap的hash 方法。使用hash方法也就是扰动函数是为了防止一些实现比较差的hashCode()方法。换句话说使用扰动函数之后可以减少碰撞。

> 所谓“拉链法”就是：将链表和数组相结合。也就是说创建一个链表数组，数组中每一格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可。

![image-20210410090719587](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410090719587.png)

JDK 1.8以后，在解决哈希冲突时有了较大的变化，当**链表长度大于阈值(默认为8**)(将链表转换成红黑树前会判断，如果当前数组的长度小于**64**，那么会选择先进行数组扩容，而不是转换为红黑树)时，将链表转化为红黑树，以减少搜索时间。



> TreeMap、TreeSet以及JDK1.8之后的HashMap底层都用到了红黑树。红黑树就是为了解决二叉查找树的缺陷，因为二叉查找树在某些情况下会退化成一个线性结构。 

![image-20210410090735441](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410090735441.png)

##### 3、HashMap的长度为什么是2的幂次方

​	`HashMap` 为了存取高效，要尽量较少碰撞，就是要尽量把数据分配均匀，每个链表长度大致相同，这个实现就是把数据存到哪个链表中的算法。即取模运算，`hash%length`，计算机中直接求余效率不如位运算，源码中做了优化`hash&(length-1)`，`hash%length==hash&(length-1)`的前提是 `length` 是 `2` 的n次方；这也就解释了 `HashMap` 的长度为什么是 `2` 的幂次方。

##### 4、HashMap多线程操作导致死循环

​	`JDK1.7` 插入元素使用的是**头插法**，并发下的·**扩容Rehash**·会造成元素之间会形成一个循环链表。不过，`JDK1.8` 后解决了这个问题，但是还是不建议在多线程下使用`HashMap`，因为多线程下使用`HashMap `还是会存在其他问题比如数据丢失。并发环境下推荐使用 `ConcurrentHashMap`。

参考博文[HashMap多线程操作导致死循环](https://www.cnblogs.com/dxflqm/p/12082081.html)。

##### 5、1.7 与1.8 的区别

- **JDK1.7的时候使用的是数组 + 单链表的数据结构。但是在JDK1.8及之后时，使用的是数组+链表+红黑树的数据结构(当链表的深度达到8的时候，也就是默认阈值，就会自动扩容把链表转成红黑树的数据结构来把时间复杂度从O(n)变成O(logN)提高了效率)**
- **JDK1.7用的是头插法，而JDK1.8及之后使用的都是尾插法，那么他们为什么要这样做呢？因为JDK1.7是用单链表进行的纵向延伸，当采用头插法时会容易出现逆序且环形链表死循环问题。但是在JDK1.8之后是因为加入了红黑树使用尾插法，能够避免出现逆序且链表死循环的问题**
- **扩容后数据存储位置的计算方式也不一样** 
  - 在JDK1.7的时候是直接**用hash值和需要扩容的二进制数进行&**(这里就是为什么扩容的时候为啥一定必须是2的多少次幂的原因所在，因为如果只有2的n次幂的情况时最后一位二进制数才一定是1，这样能最大程度减少hash碰撞)(hash值 & length-1).
  - 而在JDK1.8的时候直接用了JDK1.7的时候计算的规律，也就是**扩容前的原始位置+扩容的大小值=JDK1.8的计算方式**，而不再是JDK1.7的那种异或的方法。但是这种方式就相当于只需要判断Hash值的新增参与运算的位是0还是1就直接迅速计算出了扩容后的储存方式。

**************

##### 6、HashMap 中的哈希值计算问题

###### hash 计算

`JDK1.8`

`hashcode()`值的**高16位与低16位进行异或操作**。

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

![image-20210828161403486](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210828161403486.png)



##### 7、HaseMap Resize

**JDK 1.7以前**

**`Resize()`**

> ```java
> void resize (int newCapacity){   //传入新的容量
>        Entry[] oldTable = table;    //引用扩容前的Entry数组
>        int oldCapacity = oldTable.length;
>        if (oldCapacity == MAXIMUM_CAPACITY) {  //扩容前的数组大小如果已经达到最大(2^30)了
>            threshold = Integer.MAX_VALUE; //修改阈值为int的最大值(2^31-1)，这样以后就不会扩容了
>            return;
>        }
> 
>        Entry[] newTable = new Entry[newCapacity];  //初始化一个新的Entry数组
>        transfer(newTable);                         //！！将数据转移到新的Entry数组里
>        table = newTable;                           //HashMap的table属性引用新的Entry数组
>        threshold = (int) (newCapacity * loadFactor);//修改阈值
> }
> ```
>
> 使用一个容量更大的数组来代替已有的容量小的数组，`transfer()` 方法将原有 Entry 数组的元素拷贝到新的 Entry 数组里。

**`transfer()`**

> ```java
> void transfer(Entry[] newTable) {
>        Entry[] src = table;                   //src引用了旧的Entry数组
>        int newCapacity = newTable.length;
>        for (int j = 0; j < src.length; j++) { //遍历旧的Entry数组
>            Entry<K, V> e = src[j];             //取得旧Entry数组的每个元素
>            if (e != null) {
>                src[j] = null;//释放旧Entry数组的对象引用(for循环后，旧的Entry数组不再引用任何对象)
>                do {
>                    Entry<K, V> next = e.next;
>                    int i = indexFor(e.hash, newCapacity); //！！重新计算每个元素在数组中的位置
>                    e.next = newTable[i]; //标记[1]
>                    newTable[i] = e;      //将元素放在数组上
>                    e = next;             //访问下一个Entry链上的元素
>                } while (e != null);
>            }
>        }
> ```

**`indexFor()`**

> ```java
> static int indexFor(int h, int length) {
>     return h & (length - 1);
> }
> ```



**JDK 1.8以后**

> **经过rehash之后，元素的位置要么是在原位置，要么是在原位置再移动2次幂的位置**。
>
> `length * 2`，即新增的 `bit` 位是1，在 `(n - 1) & hash` 时，只需要判断新增加的这一个bit位，如果是0的话，说明索引不变，如果变成1了，索引变成 原索引+扩容前的容量大小
>
> ```java
> final Node<K,V>[] resize() {
>  Node<K,V>[] oldTab = table;
>  int oldCap = (oldTab == null) ? 0 : oldTab.length;
>  int oldThr = threshold;
>  int newCap, newThr = 0;
>  if (oldCap > 0) {
>      // 超过最大值就不再扩充了，就只好随你碰撞去吧
>      if (oldCap >= MAXIMUM_CAPACITY) {
>          threshold = Integer.MAX_VALUE;
>          return oldTab;
>      }
>      // 没超过最大值，就扩充为原来的2倍
>      else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
>                oldCap >= DEFAULT_INITIAL_CAPACITY)
>           newThr = oldThr << 1; // double threshold
>   }
>   else if (oldThr > 0) // initial capacity was placed in threshold
>       newCap = oldThr;
>   else {               // zero initial threshold signifies using defaults
>       newCap = DEFAULT_INITIAL_CAPACITY;
>       newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
>   }
>   // 计算新的resize上限
>   if (newThr == 0) {
> 
>       float ft = (float)newCap * loadFactor;
>       newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
>                 (int)ft : Integer.MAX_VALUE);
>   }
>   threshold = newThr;
>   @SuppressWarnings({"rawtypes"，"unchecked"})
>       Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
>  table = newTab;
>  if (oldTab != null) {
>      // 把每个bucket都移动到新的buckets中
>      for (int j = 0; j < oldCap; ++j) {
>          Node<K,V> e;
>          if ((e = oldTab[j]) != null) {
>              oldTab[j] = null;
>              if (e.next == null)
>                  newTab[e.hash & (newCap - 1)] = e;
>              else if (e instanceof TreeNode) //如果是红黑树节点
>                  ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
>              else { // 链表优化重hash的代码块
>                  Node<K,V> loHead = null, loTail = null;
>                  Node<K,V> hiHead = null, hiTail = null;
>                  Node<K,V> next;
>                  do {
>                      next = e.next;
>                      // 原索引
>                      if ((e.hash & oldCap) == 0) {
>                          if (loTail == null)
>                              loHead = e;
>                          else
>                              loTail.next = e;
>                          loTail = e;
>                      }
>                      // 原索引+oldCap
>                      else {
>                          if (hiTail == null)
>                              hiHead = e;
>                          else
>                              hiTail.next = e;
>                          hiTail = e;
>                      }
>                  } while ((e = next) != null);
>                  // 原索引放到bucket里
>                  if (loTail != null) {
>                      loTail.next = null;
>                      newTab[j] = loHead;
>                  }
>                  // 原索引+oldCap放到bucket里
>                  if (hiTail != null) {
>                      hiTail.next = null;
>                      newTab[j + oldCap] = hiHead;
>                  }
>              }
>          }
>      }
>  }
>  return newTab;
> }
> ```
>
> 1 如果 `table == null` , 则为HashMap的初始化, 生成空table返回即可;
> 2 如果 `table` 不为空, 需要重新计算 `table` 的长度, `newLength = oldLength << 1` (注, 如果原 `oldLength` 已经到了上限, 则 `newLength = oldLength`); 
> 3 遍历 `oldTable`:
> 	3.2 首节点为空, 本次循环结束;
> 	3.1 无后续节点, 重新计算 `hash` 位, 本次循环结束;
> 	3.2 当前是红黑树, 走红黑树的重定位;
> 	3.3 当前是链表, JAVA7时还需要重新计算hash位, 但是JAVA8做了优化, 通过$ (e.hash \& oldCap) == 0$ 来判断是否需要移位; 如果为真则在原位不动, 否则则需要移动到当前hash槽位 + oldCap的位置;
>
> ![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20170123110634173)
>
> 元素在重新计算 `hash` 之后，因为 n 变为2倍，那么n-1的mask范围在高位多1bit(红色)，因此新的index就会发生这样的变化：
>
> ![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20170123110716285)
>
> 因此，我们在扩充HashMap的时候，不需要像JDK1.7的实现那样重新计算hash，只需要看看原来的hash值新增的那个bit是1还是0就好了，是0的话索引没变，是1的话索引变成“原索引+oldCap”。

##### 8、HaseMap Put

**JDK8中的put方法**

![img](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/20180814125917596)

**`put()`**

> ```java
> public V put(K key, V value) {
>     return putVal(hash(key), key, value, false, true);
> }
> ```

**hash函数**

> ==key的 `hashcode`值的高16位与低16位异或==
>
> ```java
> static final int hash(Object key) {
>     int h;
>     return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
> }
> ```

**`putVal`函数**

> ```java
> // 参数onlyIfAbsent表示是否替换原值
> // 参数evict我们可以忽略它，它主要用来区别通过put添加还是创建时初始化数据的
> final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
>                 boolean evict) {
>     Node<K,V>[] tab; Node<K,V> p; int n, i;
>     // 空表，需要初始化
>     if ((tab = table) == null || (n = tab.length) == 0)
>         // resize()不仅用来调整大小，还用来进行初始化配置
>         n = (tab = resize()).length;
>     // (n - 1) & hash这种方式也熟悉了吧？都在分析ArrayDeque中有体现
>     //这里就是看下在hash位置有没有元素，实际位置是hash % (length-1)
>     if ((p = tab[i = (n - 1) & hash]) == null)
>         // 将元素直接插进去
>         tab[i] = newNode(hash, key, value, null);
>     else {
>         //这时就需要链表或红黑树了
>         // e是用来查看是不是待插入的元素已经有了，有就替换
>         Node<K,V> e; K k;
>         // p是存储在当前位置的元素
>         if (p.hash == hash &&
>             ((k = p.key) == key || (key != null && key.equals(k))))
>             e = p; //要插入的元素就是p，这说明目的是修改值
>         // p是一个树节点
>         else if (p instanceof TreeNode)
>             // 把节点添加到树中
>             e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
>         else {
>             // 这时候就是链表结构了，要把待插入元素挂在链尾
>             for (int binCount = 0; ; ++binCount) {
>                 //向后循环
>                 if ((e = p.next) == null) {
>                     p.next = newNode(hash, key, value, null);
>                     // 链表比较长，需要树化，
>                     // 由于初始即为p.next，所以当插入第8个元素才会树化
>                     if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
>                         treeifyBin(tab, hash);
>                     break;
>                 }
>                 // 找到了对应元素，就可以停止了
>                 if (e.hash == hash &&
>                     ((k = e.key) == key || (key != null && key.equals(k))))
>                     break;
>                 // 继续向后
>                 p = e;
>             }
>         }
>         // e就是被替换出来的元素，这时候就是修改元素值
>         if (e != null) { // existing mapping for key
>             V oldValue = e.value;
>             if (!onlyIfAbsent || oldValue == null)
>                 e.value = value;
>             // 默认为空实现，允许我们修改完成后做一些操作
>             afterNodeAccess(e);
>             return oldValue;
>         }
>     }
>     ++modCount;
>     // size太大，达到了capacity的0.75，需要扩容
>     if (++size > threshold)
>         resize();
>     // 默认也是空实现，允许我们插入完成后做一些操作
>     afterNodeInsertion(evict);
>     return null;
> }
> ```

`HashMap` 在 `put` 方法中，它使用 `hashCode()` 和 `equals()` 方法。当我们通过调用 `put` 方法传递 `<key-value>` 对的时候，`HashMap` 使用 `Key hashCode()` 和哈希算法来找出存储 `<key-value>` 对的索引。如果索引处为空，则直接插入到对应的数组中，否则，判断是否是红黑树，若是，则红黑树插入，否则遍历链表，若长度不小于 `8` ，则将链表转为红黑树，转成功之后 再插入。

##### 9、HashMap Get

**JDK 1.7**

首先判断输入的key是否为空,如果为空,从hashmap数组下标为0的位置获取值返回；

如果不为空,根据key的值,从hashmap数组中获取对应的entry对象,判断这个对象是否为空,为空返回null,不为空返回对应的value值, 获取value的方法中key为空和不为空时的方法里都先判断数组中的元素是否为0 ,如果不为0,才继续查找。

```java
// hashmap的get方法
public V get(Object key) {
	// 判断key 是否为null
      if (key == null)
      	// 如果为null 获取key为null的值
          return getForNullKey();
      // 获取对应key为要查询的key的entry
      Entry<K,V> entry = getEntry(key);
      // 判断是否获取到entry,如果没有,返回null,如果不为null,返回对应entry的value值
      return null == entry ? null : entry.getValue();
  }

  // 当key为null时获取value的值
  private V getForNullKey() {
  	// 判断hashmap中总的entry的数量,如果为0,说明hashmap中还没有值,返回null
      if (size == 0) {
          return null;
      }
      // 如果size 不为0 , 获取entry[] 数组中 下标为0的位置的链表
      for (Entry<K,V> e = table[0]; e != null; e = e.next) {
      	// 如果有entry对应的key的值为null ,返回对应的value
          if (e.key == null)
              return e.value;
      }
    	// 如果没有,返回空
      return null;
  }

  // 如果key不为null,获取key对应的value
  final Entry<K,V> getEntry(Object key) {
  	// 如果key不为null,判断hashmap中entry的数量是否为0 如果为0 返回null
      if (size == 0) {
          return null;
      }

      // 获取key的value值,如果key为null,返回hash值为0,反之,计算key对应的hash值
      int hash = (key == null) ? 0 : hash(key);
      // 遍历指定下标的entry数组元素链表
      for (Entry<K,V> e = table[indexFor(hash, table.length)];
           e != null;
           e = e.next) {
          Object k;
      	// 判断key的hash值与entry中的hash值是否相同,并且key通过== 和 equal 比较,
      	// 都为true时,返回这个 entry 对象 
          if (e.hash == hash &&
              ((k = e.key) == key || (key != null && key.equals(k))))
              return e;
      }
      // 如果指定下标key中的entry没有满足条件的,返回null
      return null;
  }

  // 计算 hash值
  final int hash(Object k) {
      int h = hashSeed;
      if (0 != h && k instanceof String) {
          return sun.misc.Hashing.stringHash32((String) k);
      }
      h ^= k.hashCode();
      h ^= (h >>> 20) ^ (h >>> 12);
      return h ^ (h >>> 7) ^ (h >>> 4);
  }

  // 通过hash值以及数组长度的位运算,获取entry的下标
  static int indexFor(int h, int length) {
      // assert Integer.bitCount(length) == 1 : "length must be a non-zero power of 2";
      return h & (length-1);
  }
```

**JDK 1.8**

get方法

对输入的key的值计算hash值,

首先判断hashmap中的数组是否为空和数组的长度是否为0,如果为空和为0,则直接放回null

如果不为空和0,计算key对应的数组下标,判断对应位置上的第一个node是否满足条件,如果满足条件,直接返回

如果不满足条件,判断当前node是否是最后一个,如果是,说明不存在key,则返回null

如果不是最后一个,判断是否是红黑树,如果是红黑树,则使用红黑树的方式获取对应的key,

如果不是红黑树,遍历链表是否有满足条件的,如果有,直接放回,否则返回null

```java
public V get(Object key) {
    Node<K,V> e;
    // 计算hash值,与key一起找key的值
    return (e = getNode(hash(key), key)) == null ? null : e.value;
```

调用 `getNode()`

```java
final Node<K,V> getNode(int hash, Object key) {
    // 设置一些局部变量
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    // 首先获取hashmap中的数组和长度,并判断是否为空,如果为空,返回null
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        // 获取key对应的下标对应的链表对象, 并比较第一个是否满足条件
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            // 第一个如果满足条件,则直接返回
            return first;
        // 判断当前对象是否是最后一个,如果是,说明没有找到对应的key的值
        if ((e = first.next) != null) {
            // 如果不为空,判断是否是红黑树,如果是红黑树,使用红黑树获取对应key的值
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            // 如果不是红黑树, 遍历链表,找到对应hash和key的node对象
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

##### 10、Object能作为Hashmap的key吗？

可以。但是 `Hashmap` 不允许有重复的key，所以要重写它的 `hashcode` 和 `equals`方法，以便确认 `key` 是否重复。

**如果两个对象通过 `euqals()` 方法比较返回 `true`, 但这两个对象的 `hashCode()` 方法返回不同的 `hashCode `值时，这将导致 `HashMap `会把这两个对象保存在 table 数组两个不同链表中，这与`HashMap`的 要求相冲突；**

**重写 `HashCode`，不重写 `equals`，则在table数组的同一个链表中；只有两个函数都重写，才能保证唯一性。**

**************

#### ==ConcurrentHashMap==



<img src="https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410092139327.png" alt="image-20210410092139327" style="zoom:80%;" />![image-20210410092322103](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410092322103.png)



##### ConcurrrentHashMap源码分析

###### JDK1.8一些改进

**`ForwardingNode`**

```java
static final class ForwardingNode<K,V> extends Node<K,V> {
    final Node<K,V>[] nextTable;
    ForwardingNode(Node<K,V>[] tab) {
        // MOVED = -1，ForwardingNode的哈希值为-1
        super(MOVED, null, null, null);
        this.nextTable = tab;
    }
}
```

除了普通的`Node`和`TreeNode`之外，`ConcurrentHashMap`还引入了一个新的数据类型`ForwardingNode`，我们这里只展示他的构造方法，`ForwardingNode`的作用有两个：

- 在动态扩容的过程中标志某个桶已经被复制到了新的桶数组中
- 如果在动态扩容的时候有`get`方法的调用，则`ForwardingNode`将会把请求转发到新的桶数组中，以避免阻塞`get`方法的调用，`ForwardingNode`在构造的时候会将扩容后的桶数组`nextTable`保存下来。

**`UNSAFE.compareAndSwap`**

这是在`Java 8`版本的`ConcurrentHashMap`中实现`CAS`的工具，以`int`类型为例其方法定义如下：

```java
/**
* Atomically update Java variable to <tt>x</tt> if it is currently
* holding <tt>expected</tt>.
* @return <tt>true</tt> if successful
*/
public final native boolean compareAndSwapInt(Object o, long offset,
                                              int expected,
                                              int x);

```

如果对象`o`起始地址偏移量为`offset`的值等于`expected`，则将该值设为`x`，并返回`true`表明更新成功，否则返回`false`，表明`CAS`失败。

**初始化**

```java
public ConcurrentHashMap(int initialCapacity, float loadFactor, int concurrencyLevel) {
    if (!(loadFactor > 0.0f) || initialCapacity < 0 || concurrencyLevel <= 0) // 检查参数
        throw new IllegalArgumentException();
    if (initialCapacity < concurrencyLevel)
        initialCapacity = concurrencyLevel;
    long size = (long)(1.0 + (long)initialCapacity / loadFactor);
    int cap = (size >= (long)MAXIMUM_CAPACITY) ?
        MAXIMUM_CAPACITY : tableSizeFor((int)size); 
    	// tableSizeFor，求不小于size的 2^n的算法
    this.sizeCtl = cap; 
}

static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

- `concurrencyLevel`在`Java 7`中是`Segment`数组的长度，由于在`Java 8`中已经废弃了`Segment`，因此`concurrencyLevel`只是一个保留字段，无实际意义；

- `sizeCtl`这个值第一次出现，这个值如果等于 `-1` 则表明系统正在初始化，如果是其他负数则表明系统正在扩容，在扩容时`sizeCtl`二进制的低十六位等于扩容的线程数加一，高十六位(除符号位之外)包含桶数组的大小信息。

  - ```markdown
    sizeCtl: 默认为0，用来控制table的初始化和扩容操作
    # sizeCtl==-1:表示当前的散列表正在做初始化，由于ConcurrentHashMap它是懒惰初始化的，即使用时创建，但它要保证在并发的条件下，散列表结构只能被创建一次，当多个线程都执行到 initTable 逻辑的时候，就会使用CAS的方式取修改这个sizeCtl的值。CAS采用的期望初始值是0，更新之后是-1。CAS修改成功的线程，去真正执行创建散列表的逻辑。CAS失败的线程，会进行一个自旋检查，检查这个table是否被创建出来，每次进行自旋检查之后，会让线程短暂的释放它所占用的CPU，让当前线程重新竞争CPU资源，把CPU资源让给其它更饥饿的线程去使用。
    
    # sizeCtl>0：sizeCtl表示下次触发扩容的阈值，比如sizeCtl=12的时候，当插入新数据的时候，检查容量，当发现>=12,就会触发扩容操作。
    
    # sizeCtl<0且sizeCtl≠-1：它表示当前散列表正处于扩容状态，高16位表示扩容表示戳，低16位表示参与扩容工作的线程数量+1。扩容表示戳非常的特殊，它必须保证每个线程计算出来的值是一致的。扩容标识戳的计算方式：它要保证每个线程在扩容，散列表从小到大，每次翻倍计算出来的值是一致的。扩容标识戳和老表table长度是强相关的，即不同的长度计算出来的戳是不一样的。(注意：Java中表示负数最高位要设置为1)
    ```

**`put`方法**

```java
public V put(K key, V value) {
    return putVal(key, value, false);
}
```

`put`方法会调用`putVal`方法：

```java
final V putVal(K key, V value, boolean onlyIfAbsent) {
    if (key == null || value == null) throw new NullPointerException();
    int hash = spread(key.hashCode());
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh;
        // 【A】延迟初始化
        if (tab == null || (n = tab.length) == 0)
            tab = initTable();
        // 【B】当前桶是空的，直接更新
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            if (casTabAt(tab, i, null,
                            new Node<K,V>(hash, key, value, null)))
                break;                   // no lock when adding to empty bin
        }
        // 【C】如果当前的桶的第一个元素是一个ForwardingNode节点，则该线程尝试加入扩容
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);
        // 【D】否则遍历桶内的链表或树，并插入
        else {
            // 暂时折叠起来，后面详细看
        }
    }
    // 【F】流程走到此处，说明已经put成功，map的记录总数加一
    addCount(1L, binCount);
    return null;
}
```

**桶数组的初始化**

```java
private final Node<K,V>[] initTable() {
    Node<K,V>[] tab; int sc;
    while ((tab = table) == null || tab.length == 0) {
        if ((sc = sizeCtl) < 0)
            // 说明已经有线程在初始化了，本线程开始自旋
            Thread.yield(); // lost initialization race; just spin
        else if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
            // CAS保证只有一个线程能走到这个分支
            try {
                if ((tab = table) == null || tab.length == 0) {
                    int n = (sc > 0) ? sc : DEFAULT_CAPACITY;
                    @SuppressWarnings("unchecked")
                    Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
                    table = tab = nt;
                    // sc = n - n/4 = 0.75n
                    sc = n - (n >>> 2);
                }
            } finally {
                // 恢复sizeCtl > 0相当于释放锁
                sizeCtl = sc;
            }
            break;
        }
    }
    return tab;
}
```

在初始化桶数组的过程中，系统如何保证不会出现并发问题呢，关键点在于自旋锁的使用，当有多个线程都执行`initTable`方法的时候，`CAS`可以保证只有一个线程能够进入到真正的初始化分支，其他线程都是自旋等待。

- 依照前文所述，当有线程开始初始化桶数组时，会通过`CAS`将`sizeCtl`置为-1，其他线程以此为标志开始自旋等待
- 当桶数组初始化结束后将`sizeCtl`的值恢复为正数，其值等于0.75倍的桶数组长度，这个值的含义和之前`HashMap`中的`THRESHOLD`一致，是系统触发扩容的临界点
- 在`finally`语句中对`sizeCtl`的操作并没有使用`CAS`是因为`CAS`保证只有一个线程能够执行到这个地方

https://blog.csdn.net/DD_Dddd/article/details/116176441

**扩容的代码**

```java
//调用该扩容方法的地方有：
//java.util.concurrent.ConcurrentHashMap#addCount        向集合中插入新数据后更新容量计数时发现到达扩容阈值而触发的扩容
//java.util.concurrent.ConcurrentHashMap#helpTransfer    扩容状态下其他线程对集合进行插入、修改、删除、合并、compute 等操作时遇到 ForwardingNode 节点时触发的扩容
//java.util.concurrent.ConcurrentHashMap#tryPresize      putAll批量插入或者插入后发现链表长度达到8个或以上，但数组长度为64以下时触发的扩容
private final void transfer(Node<K,V>[] tab, Node<K,V>[] nextTab) {
    int n = tab.length, stride;
    //计算每条线程处理的桶个数，每条线程处理的桶数量一样，如果CPU为单核，则使用一条线程处理所有桶
    //每条线程至少处理16个桶，如果计算出来的结果少于16，则一条线程处理16个桶
    if ((stride = (NCPU > 1) ? (n >>> 3) / NCPU : n) < MIN_TRANSFER_STRIDE)
        stride = MIN_TRANSFER_STRIDE; // subdivide range
    if (nextTab == null) {            // 初始化新数组(原数组长度的2倍)
        try {
            @SuppressWarnings("unchecked")
            Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n << 1];
            nextTab = nt;
        } catch (Throwable ex) {      // try to cope with OOME
            sizeCtl = Integer.MAX_VALUE;
            return;
        }
        nextTable = nextTab;
        //将 transferIndex 指向最右边的桶，也就是数组索引下标最大的位置
        transferIndex = n;
    }
    int nextn = nextTab.length;
    //新建一个占位对象，该占位对象的 hash 值为 -1 该占位对象存在时表示集合正在扩容状态，key、value、next 属性均为 null ，nextTable 属性指向扩容后的数组
    //该占位对象主要有两个用途：
    //   1、占位作用，用于标识数组该位置的桶已经迁移完毕，处于扩容中的状态。
    //   2、作为一个转发的作用，扩容期间如果遇到查询操作，遇到转发节点，会把该查询操作转发到新的数组上去，不会阻塞查询操作。
    ForwardingNode<K,V> fwd = new ForwardingNode<K,V>(nextTab);
    //该标识用于控制是否继续处理下一个桶，为 true 则表示已经处理完当前桶，可以继续迁移下一个桶的数据
    boolean advance = true;
    //该标识用于控制扩容何时结束，该标识还有一个用途是最后一个扩容线程会负责重新检查一遍数组查看是否有遗漏的桶
    boolean finishing = false; // to ensure sweep before committing nextTab
    //这个循环用于处理一个 stride 长度的任务，i 后面会被赋值为该 stride 内最大的下标，而 bound 后面会被赋值为该 stride 内最小的下标
    //通过循环不断减小 i 的值，从右往左依次迁移桶上面的数据，直到 i 小于 bound 时结束该次长度为 stride 的迁移任务
    //结束这次的任务后会通过外层 addCount、helpTransfer、tryPresize 方法的 while 循环达到继续领取其他任务的效果
    for (int i = 0, bound = 0;;) {
        Node<K,V> f; int fh;
        while (advance) {
            int nextIndex, nextBound;
            //每处理完一个hash桶就将 bound 进行减 1 操作
            if (--i >= bound || finishing)
                advance = false;
            else if ((nextIndex = transferIndex) <= 0) {
                //transferIndex <= 0 说明数组的hash桶已被线程分配完毕，没有了待分配的hash桶，将 i 设置为 -1 ，后面的代码根据这个数值退出当前线的扩容操作
                i = -1;
                advance = false;
            }
            //只有首次进入for循环才会进入这个判断里面去，设置 bound 和 i 的值，也就是领取到的迁移任务的数组区间
            else if (U.compareAndSwapInt(this, TRANSFERINDEX, nextIndex, nextBound = (nextIndex > stride ? nextIndex - stride : 0))) {
                bound = nextBound;
                i = nextIndex - 1;
                advance = false;
            }
        }
        if (i < 0 || i >= n || i + n >= nextn) {
            int sc;
            //扩容结束后做后续工作，将 nextTable 设置为 null，表示扩容已结束，将 table 指向新数组，sizeCtl 设置为扩容阈值
            if (finishing) {
                nextTable = null;
                table = nextTab;
                sizeCtl = (n << 1) - (n >>> 1);
                return;
            }
            //每当一条线程扩容结束就会更新一次 sizeCtl 的值，进行减 1 操作
            if (U.compareAndSwapInt(this, SIZECTL, sc = sizeCtl, sc - 1)) {
                //(sc - 2) != resizeStamp(n) << RESIZE_STAMP_SHIFT 成立，说明该线程不是扩容大军里面的最后一条线程，直接return回到上层while循环
                if ((sc - 2) != resizeStamp(n) << RESIZE_STAMP_SHIFT)
                    return;
                //(sc - 2) == resizeStamp(n) << RESIZE_STAMP_SHIFT 说明这条线程是最后一条扩容线程
                //之所以能用这个来判断是否是最后一条线程，因为第一条扩容线程进行了如下操作：
                //    U.compareAndSwapInt(this, SIZECTL, sc, (rs << RESIZE_STAMP_SHIFT) + 2)
                //除了修改结束标识之外，还得设置 i = n; 以便重新检查一遍数组，防止有遗漏未成功迁移的桶
                finishing = advance = true;
                i = n; // recheck before commit
            }
        }
        else if ((f = tabAt(tab, i)) == null)
            //遇到数组上空的位置直接放置一个占位对象，以便查询操作的转发和标识当前处于扩容状态
            advance = casTabAt(tab, i, null, fwd);
        else if ((fh = f.hash) == MOVED)
            //数组上遇到hash值为MOVED，也就是 -1 的位置，说明该位置已经被其他线程迁移过了，将 advance 设置为 true ，以便继续往下一个桶检查并进行迁移操作
            advance = true; // already processed
        else {
            synchronized (f) {
                if (tabAt(tab, i) == f) {
                    Node<K,V> ln, hn;
                    //该节点为链表结构
                    if (fh >= 0) {
                        int runBit = fh & n;
                        Node<K,V> lastRun = f;
                        //遍历整条链表，找出 lastRun 节点
                        for (Node<K,V> p = f.next; p != null; p = p.next) {
                            int b = p.hash & n;
                            if (b != runBit) {
                                runBit = b;
                                lastRun = p;
                            }
                        }
                        //根据 lastRun 节点的高位标识(0 或 1)，首先将 lastRun设置为 ln 或者 hn 链的末尾部分节点，后续的节点使用头插法拼接
                        if (runBit == 0) {
                            ln = lastRun;
                            hn = null;
                        }
                        else {
                            hn = lastRun;
                            ln = null;
                        }
                        //使用高位和低位两条链表进行迁移，使用头插法拼接链表
                        for (Node<K,V> p = f; p != lastRun; p = p.next) {
                            int ph = p.hash; K pk = p.key; V pv = p.val;
                            if ((ph & n) == 0)
                                ln = new Node<K,V>(ph, pk, pv, ln);
                            else
                                hn = new Node<K,V>(ph, pk, pv, hn);
                        }
                        //setTabAt方法调用的是 Unsafe 类的 putObjectVolatile 方法
                        //使用 volatile 方式的 putObjectVolatile 方法，能够将数据直接更新回主内存，并使得其他线程工作内存的对应变量失效，达到各线程数据及时同步的效果
                        //使用 volatile 的方式将 ln 链设置到新数组下标为 i 的位置上
                        setTabAt(nextTab, i, ln);
                        //使用 volatile 的方式将 hn 链设置到新数组下标为 i + n(n为原数组长度) 的位置上
                        setTabAt(nextTab, i + n, hn);
                        //迁移完成后使用 volatile 的方式将占位对象设置到该 hash 桶上，该占位对象的用途是标识该hash桶已被处理过，以及查询请求的转发作用
                        setTabAt(tab, i, fwd);
                        //advance 设置为 true 表示当前 hash 桶已处理完，可以继续处理下一个 hash 桶
                        advance = true;
                    }
                    //该节点为红黑树结构
                    else if (f instanceof TreeBin) {
                        TreeBin<K,V> t = (TreeBin<K,V>)f;
                        //lo 为低位链表头结点，loTail 为低位链表尾结点，hi 和 hiTail 为高位链表头尾结点
                        TreeNode<K,V> lo = null, loTail = null;
                        TreeNode<K,V> hi = null, hiTail = null;
                        int lc = 0, hc = 0;
                        //同样也是使用高位和低位两条链表进行迁移
                        //使用for循环以链表方式遍历整棵红黑树，使用尾插法拼接 ln 和 hn 链表
                        for (Node<K,V> e = t.first; e != null; e = e.next) {
                            int h = e.hash;
                            //这里面形成的是以 TreeNode 为节点的链表
                            TreeNode<K,V> p = new TreeNode<K,V>
                                (h, e.key, e.val, null, null);
                            if ((h & n) == 0) {
                                if ((p.prev = loTail) == null)
                                    lo = p;
                                else
                                    loTail.next = p;
                                loTail = p;
                                ++lc;
                            }
                            else {
                                if ((p.prev = hiTail) == null)
                                    hi = p;
                                else
                                    hiTail.next = p;
                                hiTail = p;
                                ++hc;
                            }
                        }
                        //形成中间链表后会先判断是否需要转换为红黑树：
                        //1、如果符合条件则直接将 TreeNode 链表转为红黑树，再设置到新数组中去
                        //2、如果不符合条件则将 TreeNode 转换为普通的 Node 节点，再将该普通链表设置到新数组中去
                        //(hc != 0) ? new TreeBin<K,V>(lo) : t 这行代码的用意在于，如果原来的红黑树没有被拆分成两份，那么迁移后它依旧是红黑树，可以直接使用原来的 TreeBin 对象
                        ln = (lc <= UNTREEIFY_THRESHOLD) ? untreeify(lo) :
                        (hc != 0) ? new TreeBin<K,V>(lo) : t;
                        hn = (hc <= UNTREEIFY_THRESHOLD) ? untreeify(hi) :
                        (lc != 0) ? new TreeBin<K,V>(hi) : t;
                        //setTabAt方法调用的是 Unsafe 类的 putObjectVolatile 方法
                        //使用 volatile 方式的 putObjectVolatile 方法，能够将数据直接更新回主内存，并使得其他线程工作内存的对应变量失效，达到各线程数据及时同步的效果
                        //使用 volatile 的方式将 ln 链设置到新数组下标为 i 的位置上
                        setTabAt(nextTab, i, ln);
                        //使用 volatile 的方式将 hn 链设置到新数组下标为 i + n(n为原数组长度) 的位置上
                        setTabAt(nextTab, i + n, hn);
                        //迁移完成后使用 volatile 的方式将占位对象设置到该 hash 桶上，该占位对象的用途是标识该hash桶已被处理过，以及查询请求的转发作用
                        setTabAt(tab, i, fwd);
                        //advance 设置为 true 表示当前 hash 桶已处理完，可以继续处理下一个 hash 桶
                        advance = true;
                    }
                }
            }
        }
    }
}
```



##### ConcurrentHashMap线程安全的具体实现方式/底层实现方式

###### JDK 1.7

> 数组+链表(`Segment`数组结构和`HashEntry`数组)

![image-20210922225306313](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210922225306313.png)

首先将数据分为一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据时，其他段的数据也能被其他线程访问。

**`ConcurrentHashMap` 是由 `Segment` 数组结构和 `HasEntry` 数组结构组成。**

`Segment `实现了`ReentrantLock`，所以`Segment `是一种可重入锁，扮演锁的角色。`HashEntry`用于存储键值对数据。

```java
static class Segment<K，V> extends ReentrantLock implements Serializable
{
}
```

一个`ConcurrentHashMap`里包含一个`Segment `数组。`Segment`的结构和`HashMap`类似，是一种数组和链表结构，一个`Segment` 包含一个`HashEntry `数组，每个`HashEntry` 是一个链表结构的元素，每个`Segment` 守护着一个`HashEntry`数组里的元素，当对`HashEntry`数组的数据进行修改时，必须首先获得对应的`Segment`的锁。

- **不同Segment的并发写入**

  > 不同Segment的写入是可以并发执行的。
  >
  > ![](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/v2-e967fdf4e671fa2afa8ca97f8e600ca9_r.jpg)

- **同一Segment的一写一读**

  > 同一Segment的写和读是可以并发执行的。
  >
  > ![](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/v2-6317419ee2e6a40d8cdd0f15ca202573_720w.jpg)

- **同一Segment的并发写入**

  > Segment的写入是需要上锁的，因此对同一Segment的并发写入会被阻塞。
  >
  > ![](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/v2-4343518fc5b0feecc827555043cb8b3c_r.jpg)

**读写的详细过程**

**Get方法：**

1.为输入的Key做Hash运算，得到hash值。

2.通过hash值，定位到对应的Segment对象

3.再次通过hash值，定位到Segment当中数组的具体位置。

**Put方法：**

1.为输入的Key做Hash运算，得到hash值。

2.通过hash值，定位到对应的Segment对象

3.获取可重入锁

4.再次通过hash值，定位到Segment当中数组的具体位置。

5.插入或覆盖HashEntry对象。

6.释放锁。

**Size方法**

1.遍历所有的Segment。

2.把Segment的元素数量累加起来。

3.把Segment的修改次数累加起来。

4.判断所有Segment的总修改次数是否大于上一次的总修改次数。如果大于，说明统计过程中有修改，重新统计，尝试次数+1；如果不是。说明没有修改，统计结束。

5.如果尝试次数超过阈值，则对每一个Segment加锁，再重新统计。

6.再次判断所有Segment的总修改次数是否大于上一次的总修改次数。由于已经加锁，次数一定和上次相等。

7.释放锁，统计结束。



###### **JDK 1.8**

> **数组+链表/红黑二叉树**(==`CAS`和`synchronized`==)

![image-20210410092908501](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410092908501.png)

![image-20210410092923531](https://gitee.com/yun-xiaojie/blog-image/raw/master/img/image-20210410092923531.png)

​		`ConcurrentHashMap`取消了`Segment` 分段锁，采用**==`CAS`和`synchronized`==**来保证并发安全。数据结构跟`HashMap`1.8 的结构类似，**数组+链表/红黑二叉树**。Java8在链表长度超过一定阈值(8)时将链表(寻址时间复杂度为O(N))转换为红黑树(寻址时间复杂度为O(log(N)))

> **`synchronized` 只锁当前链表或红黑二叉树的首节点，这样只要 `hash` 不冲突，就不会产生并发，效率又提升N倍。**

##### ConcurrentHashMap 读操作(get)为什么不需要加锁？

`get` 操作的源码

```java
//会发现源码中没有一处加了锁
public V get(Object key) {
    Node[] tab; Nodee, p; int n, eh; K ek;
    int h = spread(key.hashCode()); //计算hash
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (e = tabAt(tab, (n - 1) & h)) != null) {//读取首节点的Node元素
        if ((eh = e.hash) == h) { //如果该节点就是首节点就返回
            if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                return e.val;
        }
        //hash值为负值表示正在扩容，这个时候查的是ForwardingNode的find方法来定位到nextTable来
        //eh=-1，说明该节点是一个ForwardingNode，正在迁移，此时调用ForwardingNode的find方法去nextTable里找。
        //eh=-2，说明该节点是一个TreeBin，此时调用TreeBin的find方法遍历红黑树，由于红黑树有可能正在旋转变色，所以find里会有读写锁。
        //eh>=0，说明该节点下挂的是一个链表，直接遍历该链表即可。
        else if (eh < 0)
            return (p = e.find(h, key)) != null ? p.val : null;
        while ((e = e.next) != null) {//既不是首节点也不是ForwardingNode，那就往下遍历
            if (e.hash == h &&
                ((ek = e.key) == key || (ek != null && key.equals(ek))))
                return e.val;
        }
    }
    return null;
}
```

> get源码中没有一处加了锁，ConcurrentHashMap是如何保证读到的数据不是脏数据的呢？ 

**get操作可以无锁是由于Node的元素val和指针next是用volatile修饰的，在多线程环境下线程A修改结点的val或者新增节点的时候是对线程B可见的。**

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    //可以看到这些都用了volatile修饰
    volatile V val;
    volatile Nodenext;
 
    Node(int hash, K key, V val, Nodenext) {
        this.hash = hash;
        this.key = key;
        this.val = val;
        this.next = next;
    }
 
    public final K getKey()       { return key; }
    public final V getValue()     { return val; }
    public final int hashCode()   { return key.hashCode() ^ val.hashCode(); }
    public final String toString(){ return key + "=" + val; }
    public final V setValue(V value) {
        throw new UnsupportedOperationException();
    }
 
    public final boolean equals(Object o) {
        Object k, v, u; Map.Entry e;
        return ((o instanceof Map.Entry) &&
                (k = (e = (Map.Entry)o).getKey()) != null &&
                (v = e.getValue()) != null &&
                (k == key || k.equals(key)) &&
                (v == (u = val) || v.equals(u)));
    }
 
    /**
     * Virtualized support for map.get(); overridden in subclasses.
     */
    Nodefind(int h, Object k) {
        Nodee = this;
        if (k != null) {
            do {
                K ek;
                if (e.hash == h &&
                    ((ek = e.key) == k || (ek != null && k.equals(ek))))
                    return e;
            } while ((e = e.next) != null);
        }
        return null;
    }
}
```

另外，我们发现，数组上也加了 `volatile`

```java
/**
     * The array of bins. Lazily initialized upon first insertion.
     * Size is always a power of two. Accessed directly by iterators.
     */
    transient volatile Node[] table;
```

目的是：为了使得 `Node` 数组在扩容的时候对其他线程具有可见性。

##### ConcurrentHashMap和Hashtable的区别

主要体现在**实现线程安全的方法上不同**。

- **底层数据结构：**JDK1.7 的`ConcurrentHashMap`底层采用分段的**数组+链表**(Segment数组结构和HashEntry数组结构)实现，JDK 1.8采用的数据结构跟`HashMap`1.8的结构一样，**数组+链表/红黑二叉树**。Hashtable和JDK1.8之前的HashMap的底层数据结构类似都是采用**数组+链表**的形式，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的；
- **实现线程安全的方式：**①在JDK1.7的时候，ConcurrentHashMap(分段锁)对整个桶数组进行了分割分段(Segment)，每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。到了JDK1.8的时候已经摒弃了Segment 的概念，而是直接用**Node数组+链表+红黑树的数据结构来实现，并发控制使用synchronized 和CAS来操作。**(JDK1.6以后对synchronized锁做了很多优化)整个看起来就像是优化过且线程安全的HashMap，虽然在JDK1.8中还能看到Segment 的数据结构，但是已经简化了属性，只是为了兼容旧版本；②**Hashtable(同一把锁)：使用synchronized来保证线程安全，效率非常低下。**当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用put添加元素，另一个线程不能使用put 添加元素，也不能使用get，竞争会越来越激烈效率越低。

#### 比较HashSet、LinkedHashSet 和TreeSet三者的异同

HashSet是Set 接口的主要实现类，HashSet的底层是HashMap，线程不安全的，可以存储nul值；

LinkedHashSet 是HashSet的子类，能够按照添加的顺序遍历；

TreeSet 底层使用红黑树，能够按照添加元素的顺序进行遍历，排序的方式有自然排序和定制排序。



