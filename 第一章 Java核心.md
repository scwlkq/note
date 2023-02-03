## 第一章 Java核心

> 1. 集合
>
> 2. 泛型
> 3. 反射
> 4. 注解

### 1.1数据存储容器核心

#### 一、数据结构

不同的数据结构，性能不一样

##### 1.数组

![image-20220703135308934](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703135308934.png)

![image-20220703135258004](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703135258004.png)

增删改查的时候都是根据改变的数组，**新建**一个数组然后加上变化的，所以很耗时!

##### 2.链表

**添加或者删除**比较方便

![image-20220703135324504](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703135324504.png)

**查询效率低**

![image-20220702221041501](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220702221041501.png)

因为存储不连续

删除：

![image-20220702221114421](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220702221114421.png)

然后那个节点就会被**GC**

插入：

![image-20220702221218394](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220702221218394.png)

双向链表：

![image-20220809215555433](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220809215555433.png)

##### 3.树

整合数组和链表两个优点：

1，查询效率比较高

2，增加删除效率也比较高

![image-20220703123012477](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703123012477.png)

查询的时候有点像**二分查找**

**不平衡的二叉树查询效率不高**

![image-20220703123315572](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703123315572.png)

![image-20220703123350775](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703123350775.png)

![image-20220703123711420](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703123711420.png)

##### 4.红黑树RBT（上）

节点要么是红色的要么是黑色的

效果：

![image-20220703124117829](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703124117829.png)

![image-20220703124126259](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703124126259.png)

![image-20220703124139639](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703124139639.png)

![image-20220703124148353](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703124148353.png)

![image-20220703124157082](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703124157082.png)

![image-20220703124210522](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703124210522.png)

![image-20220703124227259](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703124227259.png)

![image-20220703124244724](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703124244724.png)![image-20220703124307498](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703124307498.png)

会进行动态变化（会自动变“平衡“）

![image-20220703125248578](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703125248578.png)

（叶子节点是黑色，上面的没显示叶子节点是黑色的，因为没有显示空叶子节点）

![image-20220703124516387](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703124516387.png)

每一个节点下去找都有相同数量的黑节点

![image-20220703124932601](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703124932601.png)

![image-20220703125340007](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703125340007.png)

**插入进来的默认是红色节点**

##### 5.红黑树RBT（下）

###### 插入

![image-20220703125653511](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703125653511.png)

插入的时候不同情况如下：

![image-20220703130839233](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703130839233.png)

有点复杂：

前两种情况没啥问题，直接插入

![image-20220703131257559](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703131257559.png)

第三种情况

![image-20220703140917552](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703140917552.png)

这是第三种情况的第一种：

叔叔节点不为空

![image-20220703140518377](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703140518377.png)

把上面层变色就行（插入的默认是红色）

第二种：

叔叔节点不存在或者为黑的

3.2.1.P是PP的左节点，插入节点是P的左节点

![image-20220703140626378](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703140626378.png)

（叔叔节点就是父节点的兄弟节点）

（动画分为红笔讲解+动画讲解）

![动画 (3)](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/ts动画3.gif)

3.2.2.P是PP的左节点，插入节点是P的右节点

​           先父节点左旋，变成3.2.1，（变成一条线）再祖父节点右旋

![动画 (4)](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/ts动画4.gif)

3.2.3.P是PP的右节点，插入节点是P的左节点

​			先右旋，变成3.2.4（变成一条线），再左旋

![动画 (9)](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/ts动画9.gif)

3.2.4.P是PP的右节点，插入节点是P的左节点

​		   和3.2.1一样

![动画 (8)](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/ts动画8.gif)

还有一些口诀啥的可以看下面的博客(ctrl+click)

<https://blog.csdn.net/baidu_35800355/article/details/105831154?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165682770816782391814372%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165682770816782391814372&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-105831154-null-null.142^v30^pc_rank_34,185^v2^tag_show&utm_term=%E5%8F%94%E5%8F%94%E8%8A%82%E7%82%B9&spm=1018.2226.3001.4187L>

红黑树拿下！

具体有什么用，以后再讲！后面也会有红黑树的专门章节

#### 二、集合

##### （List接口）

##### 6.集合初探

![image-20220703143917906](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703143917906.png)

很多集合类

##### 6.5. 类与类之间的关系

补充：类图类与类之间的关系

![image-20220612140633093](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220612140633093.png)

![image-20220612140753093](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220612140753093.png)

![image-20220612140928651](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220612140928651.png)

![image-20220612141035276](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220612141035276.png)

![image-20220612141227732](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220612141227732.png)



![image-20220612141339860](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220612141339860.png)

![image-20220612141347397](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220612141347397.png)

![image-20220703144703154](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703144703154.png)

![image-20220703144823146](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703144823146.png)

![image-20220703150353860](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703150353860.png)

![image-20220703150512406](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703150512406.png)

先有个比较基础的认知

#### （List)

##### 7.图解ArrayList（上）

![image-20220703150943690](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703150943690.png)

![image-20220703151056477](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703151056477.png)

![image-20220912142139652](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220912142139652.png)

![image-20220703151204843](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703151204843.png)

![image-20220703151418397](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703151418397.png)

集合

为什么能一直添加呢？（怎么实现扩容的）

ArrayList本质上就是数组

源码：

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable{
    
}
```

内部属性：

```java
private static final long serialVersionUID = 8683452581122892189L;

    /**
     * Default initial capacity.
     	默认数组的长度
     */
    private static final int DEFAULT_CAPACITY = 10;

    /**
     * Shared empty array instance used for empty instances.空数组
     */
    private static final Object[] EMPTY_ELEMENTDATA = {};

    /**
     * Shared empty array instance used for default sized empty instances. We
     * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
     * first element is added.
     */
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    /**
     * The array buffer into which the elements of the ArrayList are stored.
     * The capacity of the ArrayList is the length of this array buffer. Any
     * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
     * will be expanded to DEFAULT_CAPACITY when the first element is added.
     集合中存储数据的数组对象
     */ 
    transient Object[] elementData; // non-private to simplify nested class access

    /**
     * The size of the ArrayList (the number of elements it contains).
     * 集合中元素的个数
     * @serial
     */
    private int size;
```

modCount见下一章节

###### **初始化**源码

1）无参构造：

把default那个赋值给elementData

```JAVA
/**
     * Constructs an empty list with an initial capacity of ten.
     */
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
```

2）有参构造：

![image-20220703152805486](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703152805486.png)

![image-20220703153057714](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220703153057714.png)

###### add方法源码

1）无参数构造器

###### 第一次添加

假设初始size为0

```java

    /**
     * Appends the specified element to the end of this list.
     *
     * @param e element to be appended to this list
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        //确定容量的 动态扩容 假设size初始0
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        //将要添加的元素添加到数组中 element[0]=1--->size = 1 
        elementData[size++] = e;
        return true;
    }
```

```java
 private void ensureCapacityInternal(int minCapacity) {
       //cal无参的话返回就是10
       //传进来的参数是size+1
     	ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }
```

```java
    // elementData是{},minCapacity是1
	private static int calculateCapacity(Object[] elementData, int minCapacity) {
        // 无参构造
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }
```

```java
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;//操作次数 
	
    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```

```java
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:数组复制，返回一个新的数组，长度为newCapacity
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

![image-20220704164052632](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220704164052632.png)

2）有参数构造器

###### 第二次添加

![image-20220704171412721](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220704171412721.png)

![image-20220704172101832](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220704172101832.png)

##### 8.图解ArrayList（下）

###### 第十一次添加

前9次的添加（2~10）都不会触发grow（）了

![image-20220704172257982](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220704172257982.png)

10>>1:
10的十进制是1010

正数左边补0，负数左边补1

变为0101，为5

![image-20220704172539625](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220704172539625.png)

![image-20220704172623607](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220704172623607.png)

**动态扩容1.5倍**

######  get方法源码

```java
public E get(int index) {
    rangeCheck(index);

    return elementData(index);
}
```

```java
// 下标越界的异常 
private void rangeCheck(int index) {
        if (index >= size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    } 
```

###### set方法

```java
public E set(int index, E element) {
    rangeCheck(index);

    E oldValue = elementData(index);
    elementData[index] = element;
    return oldValue;
}
```

###### remove方法

```java
public E remove(int index) {
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work

        return oldValue;
    }
```

![image-20220704173607863](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220704173607863.png)

##### 9.并发场景下的FailFast机制详解

就是上面那个modCount

 先举个例子：

添加线程

```java
import java.util.List;

public class ThreadAdd extends Thread{
    private List list;

    public ThreadAdd(List list) {
        this.list = list;
    }

    @Override
    public void run(){
        for(int i=0;i<100;i++){
            System.out.println("循环次数"+i );
            try {
                Thread.sleep(5);
                list.add(i);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

```

遍历线程:

```java
import java.util.Iterator;
import java.util.List;

public class ThreadIterator extends Thread{
    private List list;

    public ThreadIterator(List list) {
        this.list = list;
    }

    @Override
    public void run(){
        while(true){
            for(Iterator iteratorTmp = list.iterator();iteratorTmp.hasNext();){
                iteratorTmp.next();
                try {
                    Thread.sleep(5);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```

main方法：

``` java
import java.util.ArrayList;
import java.util.List;

public class test01 {

    private static List list = new ArrayList();

    public static void main(String[] args) {
        // 对list做遍历
        new ThreadIterator(list).start();
        // 对list做添加操作
        new ThreadAdd(list).start();
    }
}

```

![image-20220705140402986](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705140402986.png)

ConcurrentModificationException 并发修改异常

![image-20220705140615278](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705140615278.png)

我们点进去看看

![image-20220705140708667](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705140708667.png)

```java
final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
```

![image-20220705141315469](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705141315469.png)

修改的话，次数+1

这样就能理解上面那个例子了

比如在我读A的时候，这时候加进来一个B，modCount就变成了6，和我itr.next中的5不一样，读取的时候就会抛出异常。

![image-20220705141134227](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705141134227.png)

![image-20220705141248392](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705141248392.png)

##### 10.LinkedList

LinkedList是通过双向链表去实现的，他的数据结构具有双向链表的优缺点，既然是双向链表，那么他的顺序访问的效率会非常高，随机访问效率比较低，它包含一个非常重要的私有内部静态类：Node

```java
 transient int size = 0;

    /**
     * Pointer to first node.
     * Invariant: (first == null && last == null) ||
     *            (first.prev == null && first.item != null)
     */
    transient Node<E> first;

    /**
     * Pointer to last node.
     * Invariant: (first == null && last == null) ||
     *            (last.next == null && last.item != null)
     */
    transient Node<E> last;
```

Node:

```java
private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

现在举个例子：

```java
public static void main(String[] args) {
    LinkedList a = new LinkedList();
    a.push("a1");
    a.push("b1");
    a.add(1);
}
```



push：

```java
public void push(E e) {
    addFirst(e);
}
```

```java
public void addFirst(E e) {
    linkFirst(e);
}
```

```java
private void linkFirst(E e) {
    final Node<E> f = first;
    final Node<E> newNode = new Node<>(null, e, f);
    first = newNode;
    if (f == null)
        last = newNode;
    else
        f.prev = newNode;
    size++;
    modCount++;
}
```

图示：

原来的双向链表

![image-20220705153818966](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705153818966.png)

添加一个节点

![image-20220705154055805](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705154055805.png)

add方法（尾部添加）：

```java
public boolean add(E e) {
    linkLast(e);
    return true;
}
```

```java
void linkLast(E e) {
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if (l == null)
        first = newNode;
    else
        l.next = newNode;
    size++;
    modCount++;
}
```

get:

```java
public E get(int index) {
    checkElementIndex(index);
    return node(index).item;
}
```

本质上是分一半，然后遍历列表中的数据

![image-20220705154634635](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705154634635.png)

set方法：

```java
public E set(int index, E element) {
    checkElementIndex(index);// 检查下标是否合法
    Node<E> x = node(index);// 根据下标获取对应的值
    E oldVal = x.item;// 记录原来的值
    x.item = element;// 赋予新的值
    return oldVal;// 返回修改之前的值
}
```

**其实源码实现起来并没有那么复杂！**

##### 11.为啥不推荐使用Vector？

和 ArrayList 很类似，都是以动态数组的形式来存储数据

Vector是线程安全的

举例：

```java
public static void main(String[] args) {
    Vector a = new Vector();
    a.add(1);
}
```

add:

```java
 public synchronized boolean add(E e) {
        modCount++;
        ensureCapacityHelper(elementCount + 1);
        elementData[elementCount++] = e;
        return true;
    }
```

**注意synchronized同步关键词**

但是，把每个方法都加上synchronized，对性能有很大影响！

所以慢慢就被放弃了。

ArrayList不是同步的，但是也可以让它是同步的

```java
public static void main(String[] args) {
    ArrayList list = new ArrayList();
    List synList = Collections.synchronizedList(list);
}
```

实现原理类似于下面：

![image-20220705160221392](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705160221392.png)

![image-20220705160240767](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705160240767.png)

有同步需求的话Collections可以增加代码的灵活度

##### （Set接口）

##### 12. HashSet 和 TreeSet

##### HashSet

HashSet实现Set接口，由哈希表支持，它不保证set的迭代顺序，特别是它不保证该顺序永久不变，**允许使用null**

```java
public HashSet(){
    map = new HashMap();
}
```

![image-20220913161616031](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220913161616031.png)

add方法：

```java
public boolean add(E e){
    return map.put(e,PRESENT)==null;
}
```

本质上是将数据保持在HashMap中，key就是我们添加的内容，value就是我们定义的一个Object对象

> 特点

底层数据结构是哈希表，`HashSet`的本质是一个“没有重复元素”的集合，他是通过`HashMap`实现的。`HashSet`中含有一个`HashMap`类型的成员变量map

##### TreeSet

基于`TreeMap`的`NavigableSet`实现。使用元素的自然顺序对元素进行排序，或者根据创建set是提供的Comparator进行排序，具体取决于使用的构造方法。

```java
public TreeSet(){
    this(new TreeMap<E, Object>());
}
```

本质是将数据保存在TreeMap中，key是我们添加的内容， value是定义的一个Object对象。和上面HashSet一样。

##### （Map接口）

> Map集合的特点
>
> 1. 能够存储唯一的列的数据（唯一、不可重复）Set（key）
> 2. 能够存储重复的数据List
> 3. 值的顺序取决于键的顺序
> 4. 键和值都是可以存储null元素的

##### TreeMap

本质上就是红黑树的实现

```java
static final class Entry<K,V> implements Map.Entry<K,V> {
    K key;//key
    V value;//value
    Entry<K,V> left;//左子节点
    Entry<K,V> right;//右子节点
    Entry<K,V> parent;//父节点
    boolean color = BLACK;//节点颜色（父节点默认黑色）

    /**
     * Make a new cell with given key, value, and parent, and with
     * {@code null} child links, and BLACK color.
     */
    Entry(K key, V value, Entry<K,V> parent) {
        this.key = key;
        this.value = value;
        this.parent = parent;
    }
}
```

红黑树插入节点的实现

![image-20220810120440657](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810120440657.png)

以put为例子：

```java
public V put(K key, V value) {
    // 将root赋值给局部变量 null
    Entry<K,V> t = root;
    // if里面就是一开始，map为空（没有root结点）put的过程
    if (t == null) {
        // 初始操作
        // 检查key是否为空
        compare(key, key); // type (and possibly null) check
		// 将key和value封装成一个对象并且赋值给root
        root = new Entry<>(key, value, null);
        size = 1;
        modCount++;
        return null;
    }
    // 下面是map不为空添加的逻辑
    int cmp;
    Entry<K,V> parent;// 父节点
    // split comparator and comparable paths
    Comparator<? super K> cpr = comparator;// 获取比较器
    // 如果比较器不为空
    if (cpr != null) {
        // 注意这里是一个循环，举个例子来说就是一直二分查找，直到找到要插入节点的父节点
        // 恰好最后一次循环之后，parent节点就真成了parent节点
        //这里的循环只是找到parent节点，并没有插入
        do {
            parent = t;
            cmp = cpr.compare(key, t.key);
            if (cmp < 0)
                t = t.left;
            else if (cmp > 0)
                t = t.right;
            else
                return t.setValue(value);
        } while (t != null);
    }
    // else同理，就是比较了一下比较器存不存在
    else {
        if (key == null)
            throw new NullPointerException();
        @SuppressWarnings("unchecked")
        Comparable<? super K> k = (Comparable<? super K>) key;
        do {
            parent = t;
            cmp = k.compareTo(t.key);
            if (cmp < 0)
                t = t.left;
            else if (cmp > 0)
                t = t.right;
            else
                return t.setValue(value);
        } while (t != null);
    }
    // 找到父节点之后进行最后一次二分比较
    // 封装起来
    Entry<K,V> e = new Entry<>(key, value, parent);
    if (cmp < 0)
        parent.left = e;
    else
        parent.right = e;
    // 这个函数就是调整插入值之后的红黑树的平衡用的
    fixAfterInsertion(e);
    size++;
    modCount++;
    return null;
}
```

如果上面红黑树理解的比较好，下面的函数理解就很简单

```java
private void fixAfterInsertion(Entry<K,V> x) {
	// 设置添加的颜色为红色
    x.color = RED;
	// 循环的条件 添加的节点不为空、不是root节点、父节点的颜色为红色 
    while (x != null && x != root && x.parent.color == RED) {
        // 父节点是否是 祖父节点的左侧节点
        if (parentOf(x) == leftOf(parentOf(parentOf(x)))) {
            // 获取父节点的 兄弟节点 叔叔节点
            Entry<K,V> y = rightOf(parentOf(parentOf(x)));
            if (colorOf(y) == RED) { // 叔叔节点是红色
                // 变色
                setColor(parentOf(x), BLACK);// 设置父节点的颜色是黑色
                setColor(y, BLACK);// 设置叔叔节点的颜色为 黑色
                setColor(parentOf(parentOf(x)), RED);// 设置 祖父节点的颜色是 红色
                // 将祖父节点你设置为插入节点，开启新一轮的循环
                x = parentOf(parentOf(x));
            } else {// 叔叔节点是黑色
                // 判断x是不是父节点的右节点
                if (x == rightOf(parentOf(x))) {
                    x = parentOf(x);
                    // 逻辑是先左旋后右旋
                    // 剩下三种情况一样逻辑
                    rotateLeft(x);
                }
                setColor(parentOf(x), BLACK);
                setColor(parentOf(parentOf(x)), RED);
                rotateRight(parentOf(parentOf(x)));
            }
        } else {
            Entry<K,V> y = leftOf(parentOf(parentOf(x)));
            if (colorOf(y) == RED) {
                setColor(parentOf(x), BLACK);
                setColor(y, BLACK);
                setColor(parentOf(parentOf(x)), RED);
                x = parentOf(parentOf(x));
            } else {
                if (x == leftOf(parentOf(x))) {
                    x = parentOf(x);
                    rotateRight(x);
                }
                setColor(parentOf(x), BLACK);
                setColor(parentOf(parentOf(x)), RED);
                rotateLeft(parentOf(parentOf(x)));
            }
        }
    }
    root.color = BLACK;
}
```

旋转函数举例：

```java
private void rotateRight(Entry<K,V> p) {
    if (p != null) {
        Entry<K,V> l = p.left;
        p.left = l.right;
        if (l.right != null) l.right.parent = p;
        l.parent = p.parent;
        if (p.parent == null)
            root = l;
        else if (p.parent.right == p)
            p.parent.right = l;
        else p.parent.left = l;
        l.right = p;
        p.parent = l;
    }
}
```

##### HashMap

###### 概念

![image-20220810131735412](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810131735412.png)

![image-20220810131916150](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810131916150.png)

![image-20220810132306744](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810132306744.png)

HashMap数组没有64位的话，即使链表有8个结点以上了也不会转

![image-20220810132510283](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810132510283.png)

###### put方法

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```

![image-20220810133912387](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810133912387.png)

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
  	// 初始判断
    if ((tab = table) == null || (n = tab.length) == 0)
        // resize（）初始数组 扩容（resize详解在下面）
        n = (tab = resize()).length;
    // 确定插入的key在数组中的下标（原理是&1为原值）
    if ((p = tab[i = (n - 1) & hash]) == null)
        // 通过hash值找到数组的下标  里面没有内容就直接赋值
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        if (p.hash == hash &&// hash值相同
            ((k = p.key) == key || (key != null && key.equals(k))))
            // 插入的值的key和数组的当前位置的key是同一个，那么直接修改里面的内容
            e = p;
        else if (p instanceof TreeNode)
            // 表示 数组中存放的节点是一个 红黑树节点
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            // 表示节点就是普通的链表
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    // 到了链表的尾部
                    p.next = newNode(hash, key, value, null);
                    // 将节点添加到链表尾部
                    // 判断是否满足转红黑树的条件
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        // 转红黑树
                        // 下面有
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

```java
final Node<K,V>[] resize() {
    // table = null
    Node<K,V>[] oldTab = table;
    // oldCap = 0
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    // 原来的扩容因子
    int oldThr = threshold;
    int newCap, newThr = 0;
    if (oldCap > 0) {// 初始不执行0
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        // 初始数组容量为16
        newCap = DEFAULT_INITIAL_CAPACITY;
        // 新的扩容因子0.75*16 =12
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    // 更新了 扩容的临界值 12
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    // 创建了一个容量为16的数组
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    // 更新了table
    table = newTab;
    if (oldTab != null) {
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

###### 链表和红黑树的转化

链表转红黑树

```java
final void treeifyBin(Node<K,V>[] tab, int hash) {
    int n, index; Node<K,V> e;
    // tab为空 或者 数组长度小于64
    if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
        resize(); // 扩容
    else if ((e = tab[index = (n - 1) & hash]) != null) {
        // 链表转红黑树的逻辑
        TreeNode<K,V> hd = null, tl = null;
        do {
            TreeNode<K,V> p = replacementTreeNode(e, null);
            if (tl == null)
                hd = p;
            else {
                p.prev = tl;
                tl.next = p;
            }
            tl = p;
        } while ((e = e.next) != null);
        if ((tab[index] = hd) != null)
            hd.treeify(tab);
    }
}
```

这时候的扩容resize（）：

```java
final Node<K,V>[] resize() {
    // 假设【1,2,3,4,5,6,7,8,，9，10,11，，，，，，】
    Node<K,V>[] oldTab = table;
    // 16
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    // 12
    int oldThr = threshold;
    int newCap, newThr = 0;
    if (oldCap > 0) {
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        // 扩容一倍
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            // 扩容临界值变成原来的两倍 24
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    // 新创建了一个32的数组
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {// 初始数组是不需要复制的
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    // 如果原来的数组就一个值（没有next），就直接赋值
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    // 移动红黑树
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    // 普通的链表的移动
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

图示：

![image-20220913170914723](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220913170914723.png)

### 1.2 泛型

#### 1. 介绍

本质：参数化类型

泛型的擦除：

​	泛型只在编译阶段有效，编译之后JVM会采取去泛型化的措施

​	泛型在运行阶段是没有效果的

![image-20220810135221022](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810135221022.png)

![image-20220810135228370](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810135228370.png)

#### 2. 泛型通配符

![image-20220810135418690](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810135418690.png)

> 无边界通配符

<?>实现

![image-20220810135848674](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810135848674.png)

> 上边界通配符

这里的String由于不是Number的子类，于是就会报错

用？extends Number实现

![image-20220810161205138](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810161205138.png)

> 下边界通配符

必须是Integer到Object类型的对象

<? super Integer> 

![image-20220810161542748](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810161542748.png)

#### 3. 具体使用

![image-20220810162144253](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220810162144253.png)

免去向上向下转型的问题

**先声明再使用**

![image-20220811091030789](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811091030789.png)

> 泛型类

新建一个Person类

```java
/**
 * @Author: sc
 * @Date: 2022/8/11 10:26
 */
public class Person {
    private String username;

    private String password;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

再新建一个PersonBean类

```java
/**
 * @Author: sc
 * @Date: 2022/8/11 10:26
 */
public class PersonBean <T> {
    private T t;

    public PersonBean(T t) {
        this.t = t;
    }

    public T getT() {
        return t;
    }

    public void setT(T t) {
        this.t = t;
    }
}
```

测试类

```java
/**
 * @Author: sc
 * @Date: 2022/8/11 10:32
 */
public class test {

    public static void main(String[] args) {
        Person person = new Person();
        person.setUsername("sc");
        person.setPassword("666");

        PersonBean<Person> personBean = new PersonBean<>(person);
        PersonBean<String> personBean1 = new PersonBean<>("hello");

    }
}
```

可以增加代码的灵活度



> 泛型方法

```java
/**
 * @Author: sc
 * @Date: 2022/8/11 10:42
 */
public class Demo <K, V>{

    /**
     * 普通方法 可以使用 类中定义的泛型
     * @param k
     * @param v
     * @return
     */
    public K method1(K k, V v){
        return (K)null;
    }

    /**
     * 普通方法 使用方法中定义的泛型
     * @param t
     * @param v
     * @param <T>
     * @return
     */
    public <T> T method2(T t, V v){
        return (T)null;
    }

    /**
     * 在静态方法中我们没法使用 类中定义的泛型
     * 因为静态方法，在没有new这个对象之前也可以使用，但是如果用类中定义的泛型这个时候就没的用
     * @param <K>
     * @return
     */
    public static <K> K method3(){
        return null;
    }

}
```



> 泛型接口

比如这样写就会有点约束

```java
/**
 * @Author: sc
 * @Date: 2022/8/11 10:42
 */
public interface Demo {
    
    int add(int a, int b);
    
}
```

比如我需要传入double类型的，就要再写一个方法，

但是这个时候我们可以使用泛型解决这个问题

```java
/**
 * @Author: sc
 * @Date: 2022/8/11 10:42
 */
public interface Demo <T>{

    T add(T a, T b);
    
}
```

这时候可以看到自动生成的是直接生成double类型的数据类型

```java
/**
 * @Author: sc
 * @Date: 2022/8/11 10:50
 */
public class DemoImpl implements Demo<Double>{
    @Override
    public Double add(Double a, Double b) {
        return null;
    }
}
```

### 1.3 反射

#### 1. 定义

![image-20220811110007708](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811110007708.png)

反向探知，在程序运行过程中动态的获取类的相关属性

> 这种动态获取类的内容以及动态调用对象的方法和获取属性的机制叫做JAVA的反射机制

```java
/**
 * @Author: sc
 * @Date: 2022/8/11 10:32
 */
public class test {

    public static void main(String[] args) throws Exception {
        // 获取一个类对象
        Class<Person> personClass = Person.class;

        // 获取类对象对应的属性或者方法
        System.out.println(personClass.getName());
        System.out.println(personClass.getPackage());
        System.out.println(personClass.getClassLoader());
        System.out.println(personClass.getSuperclass());

        // 获取一个实例
        Person person = personClass.newInstance();
        // 获取类中的方法
        Method method = personClass.getDeclaredMethod("getUsername");
        // 通过反射执行方法
        method.invoke(person);
    }

}
```

执行结果：

![image-20220811112106416](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811112106416.png)

![image-20220811111420256](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811111420256.png)

也可以通过传入key来选择建立对应的类

![image-20220811140427195](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811140427195.png)

![image-20220811140337230](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811140337230.png)

![image-20220811140358182](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811140358182.png)

>  反射到底慢在哪里？

1. 调用了native方法
2. 每次创建一个对象都需要安全检查

#### 2. 反射基础操作

##### 获取对象的四种方法和对象属性

![image-20220811150136054](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811150136054.png)



##### field操作

可以看到jdk文档里，modifier的值

![image-20220811142659783](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811142659783.png)

就是都是二的进制，意思就是有没有对应的constant field 比如PRIVATE、NATIVE关键字，有就是1，反之0

![image-20220811143133750](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811143133750.png)



也可以增加类中的属性

应用： 这个对象互补类

```java
public class ClassExamine {

    /**
     * 对象字段互补。传入一个同类型被补充对象和完整对象，如果被补充对象中有字段为null或者字符串为空，就用完整对象对应的字段值补上去；如果被补充对象中某字段不为空则保留它自己的值。
     *
     * @param origin       被补充对象
     * @param intactObject 完整对象
     * @param <T>          传入对象类型
     */
    public static <T> void objectOverlap(T origin, T intactObject) throws Exception {
        Field[] fields = origin.getClass().getDeclaredFields();
        for (Field field : fields) {
            field.setAccessible(true);
            if (field.getType() == String.class) {
                if (StringUtils.isEmpty((String) field.get(origin))) {
                    field.set(origin, field.get(intactObject));
                }
            } else {
                if (field.get(origin) == null) {
                    field.set(origin, field.get(intactObject));
                }
            }
        }
    }

}
```

![image-20220811144217760](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811144217760.png)

![image-20220811144223444](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811144223444.png)

#### 3. 单例模式的bug

**反射可以调用私有的构造器造成的**

写一个单例的类

```java
/**
 * @Author: sc
 * @Date: 2022/8/11 10:26
 */
public class Person {

   private static Person instance;

   private Person(){

   }

   public static Person getInstance(){
       if(instance == null){
           instance = new Person();
       }
       return instance;
   }
}
```

再写一个测试类

```java
/**
 * @Author: sc
 * @Date: 2022/8/11 10:32
 */
public class test {

    public static void main(String[] args) throws Exception {
        Person p1 = Person.getInstance();
        Person p2 = Person.getInstance();
        Person p3 = Person.getInstance();

        System.out.println(p1);
        System.out.println(p2);
        System.out.println(p3);
    }

}
```

可以看到这里一直是一个对象

![image-20220811150641957](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811150641957.png)

这里使用反射获取构造器再新建

```java
/**
 * @Author: sc
 * @Date: 2022/8/11 10:32
 */
public class test {

    public static void main(String[] args) throws Exception {
        Person p1 = Person.getInstance();
        Person p2 = Person.getInstance();
        Person p3 = Person.getInstance();

        System.out.println(p1);
        System.out.println(p2);
        System.out.println(p3);
        Constructor<? extends Person> declareConstructor = p1.getClass().getDeclaredConstructor();
        declareConstructor.setAccessible(true);
        System.out.println(declareConstructor.newInstance());
    }

}
```

可以看到这里创建了另外一个对象

![image-20220811150852696](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811150852696.png)

那么应该怎么解决呢？

可以将私有构造函数加一点东西

```java
private Person() throws Exception {
    if(instance == null){
        throw new Exception("实例已经存在，不允许再创建。。。");
    }
}
```

![image-20220811151104464](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811151104464.png)

就没问题了

#### 4. 反射应用的场景

1. jdbc封装
2. SpringIOC
3. JDBCTemplate
4. Mybatis
5. ......

应用很少，**但是在底层框架的中很常用**

### 1.4注解

#### 1. 基本注解

![image-20220811170531950](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811170531950.png)

![image-20220811170717683](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811170717683.png)

#### 2. 自定义注解

![image-20220811171001448](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811171001448.png)

![image-20220811171018582](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811171018582.png)

![image-20220811171243953](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811171243953.png)

#### 3. 元注解

![image-20220811171409710](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811171409710.png)



## 复习

![image-20230128163340911](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128163340911.png)

![image-20230128163703554](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128163703554.png)

![image-20230128164816171](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128164816171.png)































































