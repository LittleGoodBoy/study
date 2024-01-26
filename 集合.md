---
typora-root-url: pic
typora-copy-images-to: pic
---

# 集合

### 1、Collection

​	Collection 接口的主要作用是使得程序可以更方便地处理和操作集合中的对象。提供了添加、删除、遍历和查找元素的方法，以及获取集合大小和判断集合是否为空的方法

```java
int size();						//返回集合中元素的数量。
boolean isEmpty();				//判断集合是否为空。
boolean contains(Object var1);	//判断集合是否包含指定的元素。
Iterator<E> iterator();			//返回迭代器，用于遍历集合中的元素。
Object[] toArray();				//将集合中的元素转换为数组。
<T> T[] toArray(T[] var1);		//将集合中的元素转换为数组。
boolean add(E var1);			//向集合中添加指定的元素，返回是否添加成功。
boolean remove(Object var1);	//从集合中移除指定的元素，返回是否移除成功。
//判断集合是否包含另一个集合中的所有元素。
boolean containsAll(Collection<?> var1);
////将另一个集合中的所有元素添加到当前集合中，返回是否添加成功。
boolean addAll(Collection<? extends E> var1);	
//移除当前集合中与另一个集合中相同的所有元素，返回是否有元素被移除。
boolean removeAll(Collection<?> var1);
//仅保留与另一个集合相同的元素，移除其他元素，返回是否有元素被移除。
boolean retainAll(Collection<?> var1);
void clear();					//清空集合，移除所有元素。
boolean equals(Object var1);
int hashCode();

default boolean removeIf(Predicate<? super E> var1);
default Spliterator<E> spliterator();
default Stream<E> stream();//返回一个流，可用于进行流式操作。
default Stream<E> parallelStream();
```

#### ① AbstractCollection

​	AbstractCollection实现了Collection中一大部分方法

```java
// 在AbstractCollection已经实现的方法
public boolean isEmpty();
public boolean contains(Object var1);
public Object[] toArray();
public <T> T[] toArray(T[] var1);
public boolean remove(Object var1);
public boolean containsAll(Collection<?> var1);
public boolean addAll(Collection<? extends E> var1);
public boolean removeAll(Collection<?> var1);
public boolean retainAll(Collection<?> var1);
public void clear();
public String toString();

// 实现了，但是没有完全实现的方法
public boolean add(E var1) {
    throw new UnsupportedOperationException();
}


// 未实现的方法
public abstract Iterator<E> iterator();
public abstract int size();

```



### 2、List

​	List接口存储一组有序、不唯一的对象。List 接口实现了动态数组（ArrayList）和链表（LinkedList）等常用数据结构，可以根据实际需求选择不同的实现类。

​	List 接口的特点是可以通过索引访问和操作元素，可以按照元素的插入顺序进行遍历，允许存储重复的元素。与其他集合接口相比，List 接口更适合需要通过索引进行元素操作的场景，如需要在特定位置插入、删除元素的情况。

​	1、ArrayList 是一个长度可变的数组，可遍历元素和使用下标随机访问元素。

​	2、LinkedList 用链表存储方式实现，用于需要频繁插入和删除中间节点的操作。

​	3、Vector与 ArrayList 类似，但是是线程安全的，性能较差。在多线程环境中使用，或者在性能要求不高的情况下使用。

​	4、Stack基于 Vector 实现的栈（后进先出）数据结构。

​	5、CopyOnWriteArrayList 线程安全的 ArrayList 变体，适用于读多写少的场景，因为写操作会复制整个数据集。

#### ① ArrayList

##### 构造方法

```java
// ArrayList
// 1、最大容量 MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
// 一些vm可能会在数组中保留一些header信息，分配更大的长度可能会导致OutOfMemoryError异常。这里这样做的原因是为了尽可能的避免因为vm使用了数据保存header的信息而导致分配更大的长度产生OutOfMemoryError异常。但是并不一定超出这个长度一定会异常。这只是为了尽可能的去避免。但是假使当一个vm使用了数组保存一些header，并且这些header使用的长度大于8时那么当数组扩容到2^31-1再减去header的信息长度时依旧会发生OutOfMemoryError异常

// 构造方法
// 1、无参构造
// 默认无参的构造方法就是事先创建好一个空数组,当向该数组中添加第一个元素的时候,初始化容量为10
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = new Object[0];
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}

// 2、有参构造
private static final int DEFAULT_CAPACITY = 10;
public ArrayList(int var1) {
    if (var1 > 0) {
        this.elementData = new Object[var1]; // 如果指定的容量大于0, 就初始化一个指定容量的数组
    } else {
        if (var1 != 0) { // 如果容量不大于0, 并且不等于0就会抛异常
            throw new IllegalArgumentException("Illegal Capacity: " + var1);
        }
        this.elementData = EMPTY_ELEMENTDATA;
    }
}

// 3、根据另外一个集合初始化
public ArrayList(Collection<? extends E> var1) {
    Object[] var2 = var1.toArray();
    if ((this.size = var2.length) != 0) {
        if (var1.getClass() == ArrayList.class) {
            this.elementData = var2;
        } else {
            this.elementData = Arrays.copyOf(var2, this.size, Object[].class);
        }
    } else {
        this.elementData = EMPTY_ELEMENTDATA;
    }
}

// 以无参数构造方法创建 ArrayList 时,实际上初始化赋值的是一个空数组.当真正对数组进行添加元素操作时,才真正分配容量.即向数组中添加第一个元素时,数组容量扩为 10.

// 以有参构造方法创建 ArrayList 时,根据指定创建一个数组,后续添加元素的时候,如果添加后的大小超出实际数组的大小时,按照1.5倍进行扩容.

// ArrayList的默认数组大小为什么是10？
	据说是因为sun的程序员对一系列广泛使用的程序代码进行了调研，结果就是10这个长度的数组是最常用的最有效率的。也有说就是随便起的一个数字，8个12个都没什么区别，只是因为10这个数组比较的圆满而已。
```

##### 添加元素-add

```java
/**
 * 将指定的元素追加到此列表的末尾。
 */
public boolean add(E var1) {
    // // 添加元素之前，先调用ensureCapacityInternal方法，用于确认容量，插入元素之前，会检查是否需要扩容
	this.ensureCapacityInternal(this.size + 1); // 确认容量, 判断是否需要扩容
	this.elementData[this.size++] = var1; 		// 添加新增的元素到数据中, 就是数组元素的赋值
	return true;
}

// 确认扩容的逻辑
private void ensureCapacityInternal(int var1) {
    this.ensureExplicitCapacity(calculateCapacity(this.elementData, var1));
}

// 该方法就是确认一下此时需要的空间大小
private static int calculateCapacity(Object[] elementData, int minCapacity) {
    // 如果elementData为空，则返回默认容量和minCapacity中的最大值
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    // 否则直接返回minCapacity
    return minCapacity;
}


// 判断是否需要扩容，并调用扩容方法
private void ensureExplicitCapacity(int minCapacity) {
    // 修改次数自增
    modCount++;
    // overflow-conscious code
    // 判断是否需要扩容
    if (minCapacity - elementData.length > 0)
        //调用grow方法进行扩容，调用此方法代表已经开始扩容了
        grow(minCapacity);
}

/**
 * ArrayList扩容的核心方法。
 */
private void grow(int minCapacity) {
    // oldCapacity为旧容量，newCapacity为新容量
    int oldCapacity = elementData.length;
    // 将oldCapacity 右移一位，其效果相当于oldCapacity /2，
    // 我们知道位运算的速度远远快于整除运算，整句运算式的结果就是将新容量更新为旧容量的1.5倍，
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    // 然后检查新容量是否大于最小需要容量，若还是小于最小需要容量，那么就把最小需要容量当作数组的新容量，
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    // 如果新容量大于 MAX_ARRAY_SIZE,进入(执行) `hugeCapacity()` 方法来比较 minCapacity 和 MAX_ARRAY_SIZE，
    // 如果minCapacity大于最大容量，则新容量则为`Integer.MAX_VALUE`，否则，新容量大小则为 MAX_ARRAY_SIZE 即为 `Integer.MAX_VALUE - 8`。
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    // 将旧数据拷贝到新数组中，新数组的长度为newCapacity
    elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    // 对minCapacity和MAX_ARRAY_SIZE进行比较
    // 若minCapacity大，将Integer.MAX_VALUE作为新数组的大小
    // 若MAX_ARRAY_SIZE大，将MAX_ARRAY_SIZE作为新数组的大小
    // MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
    return (minCapacity > MAX_ARRAY_SIZE) ? Integer.MAX_VALUE : MAX_ARRAY_SIZE;
}


下面让我们来简单分析一下添加元素时发生了什么：
	当我们要 add 进第 1 个元素到 ArrayList 时，此时elementData.length 为 0 （因为还是一个空的 list），因为执行了 ensureCapacityInternal() 方法 ，所以 minCapacity 此时为 10。此时，minCapacity - elementData.length > 0成立，所以会进入 grow(minCapacity) 方法。
	当 add 第 2 个元素时，minCapacity 为 2，此时 elementData.length(容量)在添加第一个元素后扩容成 10 了。此时，minCapacity - elementData.length > 0 不成立，所以不会进入 （执行）grow(minCapacity) 方法。
	添加第 3、4....到第 10 个元素时，依然不会执行 grow 方法，数组容量都为 10。
直到添加第 11 个元素，minCapacity(此时为11)比 elementData.length（此时为 10）要大。所以继续进入 grow 方法进行扩容。

    
int newCapacity = oldCapacity + (oldCapacity >> 1)，所以 ArrayList 每次扩容之后容量都会变为原来的 1.5 倍左右（oldCapacity 为偶数就是 1.5 倍，否则是 1.5 倍左右）！ 奇偶不同，比如 ：10+10/2 = 15, 33+33/2=49。如果是奇数的话会丢掉小数.
    
如果新容量大于 MAX_ARRAY_SIZE，进入(执行) hugeCapacity() 方法来比较 minCapacity 和 MAX_ARRAY_SIZE，如果 minCapacity 大于最大容量，则新容量则为Integer.MAX_VALUE，否则，新容量大小则为 MAX_ARRAY_SIZE 即为 Integer.MAX_VALUE - 8。由此可见ArrayList最大的大小就是Integer.MAX_VALUE。

总结：
    1、ArrayList创建对象时，若未指定集合大小，那么初始化大小为0；若已指定大小，集合大小为指定的大小；
	2、当第一次调用add方法时，如果一开始未指定集合大小，那么就创建一个长度为10的数组，也就是将集合长度扩为10。如果第一次添加数据是调用addAll就选择10和加入的集合大小之间的较大值作为扩容大小来进行扩容；
	3、之后如果向集合中添加元素再次导致数组满了触发扩容，那么先判断将集合扩大1.5倍后是否够用，如果仍然不够，就将真正所需要最小容量（minCapacity）作为扩容大小。
```

##### 添加元素到指定位置-add

```java
public void add(int var1, E var2) {
    this.rangeCheckForAdd(var1); // 判断下标是否在范围
    this.ensureCapacityInternal(this.size + 1);	// 容量确认, 超过则扩容
    // 数组的拷贝, 指定下标位置开始到结束, 整体复制到下标位置+1到结束
    System.arraycopy(this.elementData, var1, this.elementData, var1 + 1, this.size - var1);
    this.elementData[var1] = var2; // 下标位置的值替换为新值, 原来下标位置的值后移了1位
    ++this.size; // 修改当前数组的大小
}
```

##### 删除元素

```java
    public E remove(int var1) {
        this.rangeCheck(var1);
        ++this.modCount;
        Object var2 = this.elementData(var1);
        int var3 = this.size - var1 - 1;
        if (var3 > 0) {
            System.arraycopy(this.elementData, var1 + 1, this.elementData, var1, var3);
        }
        this.elementData[--this.size] = null;
        return var2;
    }

    public boolean remove(Object var1) {
        int var2;
        if (var1 == null) {
            for(var2 = 0; var2 < this.size; ++var2) {
                if (this.elementData[var2] == null) {
                    this.fastRemove(var2);
                    return true;
                }
            }
        } else {
            for(var2 = 0; var2 < this.size; ++var2) {
                if (var1.equals(this.elementData[var2])) {
                    this.fastRemove(var2);
                    return true;
                }
            }
        }
        return false;
    }
```



##### System.arraycopy() 和 Arrays.copyOf()方法

​	阅读源码的话，会发现 ArrayList 中大量调用了这两个方法。比如：扩容操作以及add(int index, E element)、grow(int minCapacity)、构造方法ArrayList(Collection<? extends E> c)中调用的toArray()等方法中都用到了这两个方法！

```java

// 我们发现 arraycopy 是一个 native 方法,接下来我们解释一下各个参数的具体意义
/**
* 复制数组
* @param src 源数组
* @param srcPos 源数组中的起始位置
* @param dest 目标数组
* @param destPos 目标数组中的起始位置
* @param length 要复制的数组元素的数量
*/
public static native void arraycopy(Object src,  int  srcPos,
                                    Object dest, int destPos,
                                    int length);

// 使用场景
/**
 * 在此列表中的指定位置插入指定的元素。
 * 先调用 rangeCheckForAdd 对index进行界限检查；然后调用 ensureCapacityInternal 方法保证capacity足够大；
 * 再将从index开始之后的所有成员后移一个位置；将element插入index位置；最后size加1。
 */
public void add(int index, E element) {
    rangeCheckForAdd(index);
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    // arraycopy()方法实现数组自己复制自己
    // elementData:源数组；index:源数组中的起始位置；elementData：目标数组；
    // index + 1：目标数组中的起始位置； size - index：要复制的数组元素的数量；
    // 其实就是相当于将elementData数组下标index~size-1位置上的数据复制到elementData数组的index+1~size下标位置上去，也就是实现了从index开始之后的所有成员后移一个位置
    System.arraycopy(elementData, index, elementData, index + 1, size - index);
    // 最后将element插入到空出来的index位置，将size++
    elementData[index] = element;
    size++;
}

/**
 * 给原有数组扩容
 */
public static int[] copyOf(int[] original, int newLength) {
    // 申请一个新的数组
    int[] copy = new int[newLength];
    // 调用System.arraycopy,将源数组中的数据拷贝到新创建的数组中,并返回新的数组
    System.arraycopy(original, 0, copy, 0, Math.min(original.length, newLength));
    return copy;
}

扩展：
    System.arraycopy()标识为native意味着该方法为JDK的本地库，不可避免的会进行IO操作，如果频繁的对ArrayList进行扩容，毫不疑问会降低ArrayList的使用性能，因此当我们确定添加元素的个数的时候，我们可以事先知道并指定ArrayList的可存储元素的个数，这样当我们向ArrayList中加入元素的时候，就可以避免ArrayList的自动扩容，从而提高ArrayList的性能。

区别：
	arraycopy() 需要目标数组，将原数组拷贝到你自己定义的数组里或者原数组，而且可以选择拷贝的起点和长度以及放入新数组中的位置。
	copyOf() 是系统自动在内部新建一个数组，并将旧数组中的数据拷贝到新数组中，并返回新数组。
```



#### ② LinkedList









### Set接口及其实现类

​	Set接口存储一组无序、唯一的对象。它是处理去重数据的常用选择，并提供了高效的查找和判断元素存在性的功能。常见的 Set 接口的实现类包括 HashSet、TreeSet 和 LinkedHashSet 等。

​	1、HashSet 基于散列表（哈希表）实现，存储唯一元素，不保证元素的顺序。      

​	2、LinkedHashSet 在 HashSet 的基础上，使用链表维护插入顺序，因此能够保持插入顺序。

​	3、TreeSet 基于红黑树实现，存储唯一元素，并按照元素的自然顺序（或者提供的比较器）进行排序。

​	4、EnumSet：专门用于存储枚举类型的集合，基于位向量实现，非常高效。

​	5、CopyOnWriteArraySet 线程安全的 Set 变体，底层基于 CopyOnWriteArrayList，适用于读多写少的场景。


```java
//1、创建 EnumSet
// 创建一个包含所有枚举值的 EnumSet
EnumSet<Weekday> set1 = EnumSet.allOf(Weekday.class);
// 创建一个空的 EnumSet，并指定枚举类型
EnumSet<Weekday> set2 = EnumSet.noneOf(Weekday.class);
// 创建一个包含指定枚举值的 EnumSet
EnumSet<Weekday> set3 = EnumSet.of(Weekday.SATURDAY, Weekday.SUNDAY);
// 创建一个范围内的 EnumSet，包含起始和结束之间的所有枚举值：
EnumSet<Weekday> set4 = EnumSet.range(Weekday.MONDAY, Weekday.FRIDAY);
 
 
// 2、添加和删除元素
EnumSet<Weekday> set5 = EnumSet.noneOf(Weekday.class);
// 添加一个枚举值到 EnumSet：
set5.add(Weekday.MONDAY);
set5.add(Weekday.TUESDAY);
set5.add(Weekday.WEDNESDAY);
// 删除一个枚举值从 EnumSet：
set5.remove(Weekday.MONDAY);

// 3、检查元素是否存在
// 检查 EnumSet 是否包含指定枚举值
boolean contains = set5.contains(Weekday.MONDAY);

// 4、大小和清空操作
// 获取 EnumSet 的大小（包含的枚举值数量）
int size = set5.size();
// 清空 EnumSet 中的所有元素：
// set5.clear();

// 5、遍历 EnumSet

// 使用迭代器遍历 EnumSet：
Iterator<Weekday> iterator = set5.iterator();
while (iterator.hasNext()) {
    Weekday value = iterator.next();
    System.out.println(value);
}

// 使用增强的 for 循环遍历 EnumSet：
for (Weekday value : set5) {
    System.out.println(value);
}
```

### Map接口及其实现类

​	用于存储键值对（key-value）的数据结构。Map 接口提供了一种将键映射到值的方式，它不允许键重复，并且可以根据键快速找到对应的值。常见的 Map 接口的实现类包括 HashMap、TreeMap 和 LinkedHashMap 等。

​	1、HashMap 基于散列表（哈希表）实现，存储键值对，键是唯一的。不保证键值对的顺序。

​	2、LinkedHashMap 在 HashMap 的基础上，使用链表维护插入顺序或访问顺序，能够保持插入顺序或按照访问顺序遍历键值对。可以使用LinkedHashMap实现LRU缓存。

​	3、TreeMap 基于红黑树实现，存储键值对，并按照键的自然顺序（或者提供的比较器）进行排序。

​	4、WeakHashMap 与 HashMap 类似，但对于没有被其他对象引用的键，不会阻止垃圾回收。

​	5、EnumMap 专门用于存储枚举类型的键的集合，内部基于数组实现，非常高效。

​	6、IdentityHashMap 使用引用相等而不是值相等来比较键，对于对象引用的比较非常严格。

​	7、ConcurrentHashMap线程安全的 HashMap 变体，支持并发访问，分段锁机制提高了并发性能。

​	8、Properties一个特殊的 Map 实现，用于处理属性文件，键和值都必须为字符串类型。

​	9、HashTable 与 HashMap 类似，但是是线程安全的，性能较差。在多线程环境中使用，或者在性能要求不高的情况下使用。

#### ① HashMap

​	在JDK1.7中，HashMap数据结构为数组+链表；

​	JDK1.8之后增加了数组+链表+红黑树变换；

​	HashMap存储的键值对Key-Value，Key具有唯一性，采用了链地址法来处理哈希冲突，当往 HashMap 中添加元素时，会计算 key 的 hash 值取余得出元素在数组中的的存放位置。

​	HashMap 是线程不安全的

​	在 1.8 版本的中 hash() 和 resize( ) 方法也有了很大的改变，提升了性能

​	Key和Value都可存放null，Key只能存放一个null

##### 哈希表默认参数

```java
// HashMap的默认初始长度16
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; 
// HashMap的最大长度2的30次幂
static final int MAXIMUM_CAPACITY = 1 << 30;
// HashMap的默认加载因子0.75
static final float DEFAULT_LOAD_FACTOR = 0.75f;
// HashMap链表升级成红黑树的临界值
static final int TREEIFY_THRESHOLD = 8;
// HashMap红黑树退化成链表的临界值
static final int UNTREEIFY_THRESHOLD = 6;
// HashMap链表升级成红黑树第二个条件：HashMap数组(桶)的长度大于等于64
static final int MIN_TREEIFY_CAPACITY = 64;

```

- 默认初始容量为16
- 最大长度为2的30次幂
- 默认加载因子为0.75
- 当链表节点小于等于6，自动退化成链表
- 当链表节点大于等于8，长度大于64时进行变化成红黑树
- 扩容阈值，当你的hashmap中的元素个数超过这个阈值，便会发生扩容
- 阈值计算方式：threshold = capacity * loadFactor   (容量*加载因子)



##### 无参构造

```java
public HashMap() {
	this.loadFactor = 0.75F;
}
// 只初始化了负载因子为0.75，还没有初始化容量，阈值也为0

// 在第一次put时，进行第一次resize
public V put(K var1, V var2) {
    return this.putVal(hash(var1), var1, var2, false, true);
}
final V putVal(int var1, K var2, V var3, boolean var4, boolean var5) {
    HashMap.Node[] var6;
    int var8;
    if ((var6 = this.table) == null || (var8 = var6.length) == 0) {
        var8 = (var6 = this.resize()).length;  // 初始化容量
    }
    Object var7;
    int var9;
    if ((var7 = var6[var9 = var8 - 1 & var1]) == null) {
        var6[var9] = this.newNode(var1, var2, var3, (HashMap.Node)null);
    } else {
        Object var10;
        Object var11;
        if (((HashMap.Node)var7).hash == var1 && ((var11 = ((HashMap.Node)var7).key) == var2 || var2 != null && var2.equals(var11))) {
            var10 = var7;
        } else if (var7 instanceof HashMap.TreeNode) {
            var10 = ((HashMap.TreeNode)var7).putTreeVal(this, var6, var1, var2, var3);
        } else {
            int var12 = 0;
            while(true) {
                if ((var10 = ((HashMap.Node)var7).next) == null) {
                    ((HashMap.Node)var7).next = this.newNode(var1, var2, var3, (HashMap.Node)null);
                    if (var12 >= 7) {
                        this.treeifyBin(var6, var1);
                    }
                    break;
                }
                if (((HashMap.Node)var10).hash == var1 && ((var11 = ((HashMap.Node)var10).key) == var2 || var2 != null && var2.equals(var11))) {
                    break;
                }
                var7 = var10;
                ++var12;
            }
        }
        if (var10 != null) {
            Object var13 = ((HashMap.Node)var10).value;
            if (!var4 || var13 == null) {
                ((HashMap.Node)var10).value = var3;
            }
            this.afterNodeAccess((HashMap.Node)var10);
            return var13;
        }
    }
    ++this.modCount;
    if (++this.size > this.threshold) { // 当大小超过阈值时, 调用resize扩容
        this.resize();
    }
    this.afterNodeInsertion(var5);
    return null;
}

// resize方法
final HashMap.Node<K, V>[] resize() {
        HashMap.Node[] var1 = this.table;
        int var2 = var1 == null ? 0 : var1.length; // 第一次put的时候, this.table为null, 所以var1=null, var2也就是0
        int var3 = this.threshold; // 阈值, 无参构造只初始化了加载因子, 阈值为默认值0
        int var5 = 0;
        int var4;
        if (var2 > 0) { // 首次put var2=0
            if (var2 >= 1073741824) {
                this.threshold = 2147483647;
                return var1;
            }
			// 
            if ((var4 = var2 << 1) < 1073741824 && var2 >= 16) {
                var5 = var3 << 1;
            }
        } else if (var3 > 0) {
            var4 = var3;
        } else {  // 首次put的时候, 会进入到该分支, 赋值默认容量和默认的阈值
            var4 = 16;  // DEFAULT_INITIAL_CAPACITY
            var5 = 12;  // DEFAULT_INITIAL_CAPACITY * DEFAULT_LOAD_FACTOR = 16*0.75 = 12
        }
		
        if (var5 == 0) {
            float var6 = (float)var4 * this.loadFactor;
            var5 = var4 < 1073741824 && var6 < 1.07374182E9F ? (int)var6 : 2147483647;
        }

        this.threshold = var5;
        HashMap.Node[] var14 = (HashMap.Node[])(new HashMap.Node[var4]);
        this.table = var14;
        if (var1 != null) {
            for(int var7 = 0; var7 < var2; ++var7) {
                HashMap.Node var8;
                if ((var8 = var1[var7]) != null) {
                    var1[var7] = null;
                    if (var8.next == null) {
                        var14[var8.hash & var4 - 1] = var8;
                    } else if (var8 instanceof HashMap.TreeNode) {
                        ((HashMap.TreeNode)var8).split(this, var14, var7, var2);
                    } else {
                        HashMap.Node var9 = null;
                        HashMap.Node var10 = null;
                        HashMap.Node var11 = null;
                        HashMap.Node var12 = null;

                        HashMap.Node var13;
                        do {
                            var13 = var8.next;
                            if ((var8.hash & var2) == 0) {
                                if (var10 == null) {
                                    var9 = var8;
                                } else {
                                    var10.next = var8;
                                }

                                var10 = var8;
                            } else {
                                if (var12 == null) {
                                    var11 = var8;
                                } else {
                                    var12.next = var8;
                                }
                                var12 = var8;
                            }
                            var8 = var13;
                        } while(var13 != null);

                        if (var10 != null) {
                            var10.next = null;
                            var14[var7] = var9;
                        }
                        if (var12 != null) {
                            var12.next = null;
                            var14[var7 + var2] = var11;
                        }
                    }
                }
            }
        }
        return var14;
    }
```

##### 有参构造

```java
public HashMap(int var1, float var2) {
    if (var1 < 0) {
        throw new IllegalArgumentException("Illegal initial capacity: " + var1);
    } else {
        if (var1 > 1073741824) {
            var1 = 1073741824;
        }

        if (!(var2 <= 0.0F) && !Float.isNaN(var2)) {
            this.loadFactor = var2;
            this.threshold = tableSizeFor(var1);  // 作用是返回大于输入参数且最近的2的整数次幂的数
        } else {
            throw new IllegalArgumentException("Illegal load factor: " + var2);
        }
    }
}
```

##### put方法

```java
1、首先会根据当前key的hashcode，然后hashcode与hashcode的高16位做异或运算（使得hash值更加分散）计算一个hash值
2、然后根据hash值与(n-1)按位相与得到一个桶的索引值i。
3、会把新元素放在哈希桶i的位置，如果i位置已经有元素，需要遍历位置i对应的链表，将新节点与链表节点逐一比较，如果有重复，覆盖原有节点，如果没有重复，将新节点添加到链表末尾。
4、另外在put方法中，如果发现哈希表元素数量超过指定阈值，需要触发扩容机制。如果链表元素数量达到8，需要转换为红黑树。

//put主方法
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
//根据key计算一个哈希值
static final int hash(Object key) {
    if(key==null)
        return 0;
    int h=key.hashCode();
    //h与h右移16位做异或  这样的目的是使hash值尽可能分散
    return h ^ (h >>> 16);
}
//根据key value 和计算出的hash值 执行具体更新方法
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict) {
    Node<K,V>[] tab; 
    Node<K,V> p; 
    int n, i;
    // 如果map还是空的，则先开始初始化resize()，table是map中用于存放索引的表
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    // 这里的下标位置i由(n - 1) & hash计算得到，n为map中数组的长度
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null); // 发现tab[i] 没有值，直接存入即可
    // 如果走到else这一步，说明key索引到的数组位置上已经存在内容，即出现了碰撞
    // 这个时候需要更为复杂处理碰撞的方式来处理，如链表和树
    else {
        // e记录当前节点  k记录key值
        Node<K,V> e; K k;
         // 其中p已经在上面通过计算索引找到了，即发生碰撞那一个节点
         // 比较，如果该节点的hash和当前的hash相等，而且key也相等则说明两个key是 一样的，则将当前节点p用临时节点e保存
        if (p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k))))
            // 将第一个元素赋值给e，用e来记录。直接将插入的新元素覆盖旧元素
            e = p;
        	// 如果当前节点p是（红黑）树类型的节点，则说明碰撞已经开始用树来处理
        	// hash值不相等，即key不相等并且该节点为红黑树结点，将元素插入红黑树
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value); // 放入树中
        else {  // 为链表结点
            // 在链表最末插入结点（尾插法）
            for (int binCount = 0; ; ++binCount) {
                 // 如果当前碰撞到的节点没有后续节点，到达链表的尾部，则直接新建节点并追加
                if ((e = p.next) == null) {
                    // 在尾部插入新结点
                    p.next = newNode(hash, key, value, null);
                     // 结点数量达到阈值(默认为 8 )，执行 treeifyBin 方法
                     // 这个treeifyBin()方法会根据 HashMap 数组情况来决定是否转换为红黑树
                     // 只有当数组长度大于或者等于 64 的情况下，才会执行转换红黑树操作，以减少执行效率。否则，就是只是对数组扩容。
                     // TREEIFY_THRESHOLD = 8
                     // 从0开始的，如果到了7则说明满8了，这个时候就需要转
                     // 重新确定是否是扩容还是转用红黑树了
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    // 跳出循环  此时e=null，表示没有在链表中找到与插入元素key和hash值相同的节点
                    break;
                }
                // 判断链表中结点的key值和Hash值与插入的元素的key值和Hash值是否相等
                // 找到了碰撞节点中，key完全相等的节点，则用新节点替换老节点
                if (e.hash == hash && ((k = e.key) == key || (key != null && key.equals(k))))
                    break; // 若相等，则不用将其插入了，直接跳出循环
                // 用于遍历桶中的链表，与前面的e = p.next组合，可以遍历链表
                p = e;
            }
        }
        // 当e!=null时，表示在数组桶或链表或红黑树中存在key值、hash值与插入元素相等的结点。此时就直接用原有的节点就可以了，不用插入新的元素了。此时e就代表原本就存在于HashMap中的元素
        // 此时的e是保存的被碰撞的那个节点，即老节点
        if (e != null) { // existing mapping for key
            // 记录e的value，也就是旧value值
            V oldValue = e.value;
            // onlyIfAbsent为false或者旧值为null，则需要用新的value值对旧value值进行覆盖
            // onlyIfAbsent是方法的调用参数，表示是否替换已存在的值，
            // 在默认的put方法中这个值是false，所以这里会用新值替换旧值
            if (!onlyIfAbsent || oldValue == null)
                // 用新值替换旧值
                e.value = value;
            // 替换旧值时会调用的方法（默认实现为空）
            afterNodeAccess(e);
            // 返回旧值
            return oldValue;
        }
    }
    // 结构性修改，记录HashMap被修改的次数，主要用于多线程并发时候
    // map变更性操作计数器
    // 比如map结构化的变更像内容增减或者rehash，这将直接导致外部map的并发
    // 迭代引起fail-fast问题，该值就是比较的基础
    ++modCount;
    // 实际大小大于阈值则扩容    ++size只有在插入新元素才会执行，如果发现HashMap中已经存在了相同key和hash的元素，就不会插入新的元素，在上面就已经执行return了，也就不会改变size大小
    // size即map中包括k-v数量的多少
    // 当map中的内容大小已经触及到扩容阈值时，则需要扩容了
    if (++size > threshold)
        resize();
    // 插入成功时会调用的方法（默认实现为空）
    afterNodeInsertion(evict);
    // 没有找到原有相同key和hash的元素，则直接返回Null
    return null;
}

```

![image-20240108113613545](/image-20240108113613545.png)



##### get方法

```java
1、首先根据key，将key的hashcode与hashcode的高16位进行按位与得到一个hash值。
2、然后根据(n - 1) & hash计算一个哈希桶的索引值i，然后哈希桶i处对应的链表，依次比较各个节点的key进行查询，返回对应节点的value值。
3、需要注意链表可能已经树化，如果是红黑树，就要遍历红黑树进行比较。

//get主方法
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}

//根据key计算一个哈希值
static final int hash(Object key) {
    if(key==null)
        return 0;
    int h=key.hashCode();
    //h与h右移16位做异或  这样的目的是使hash值尽可能分散
    return h ^ (h >>> 16);
}

//get方法的主逻辑 传入key和key计算出的hash值进行查询
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; 
    Node<K,V> first, e; 
    int n; K k;
    //如果哈希表为空 哈希表长度为0 (n - 1) & hash处元素为空 则直接返回空
    //否则开始查找逻辑
    if ((tab = table) != null && 
        (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        //如果当前key与i处节点key相等
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        if ((e = first.next) != null) {
            //如果是红黑树开始红黑树的查找逻辑
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
             //如果是链表就依次比较各个节点 比较的时候先比较hashcode在通过equals进行比较。
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

##### 扩容

​	在JDK1.7的时候是先扩容后插入的,不过在扩容前会先判断插入节点是否存在Hash冲突，如果有Hash冲突才会扩容，如果没有Hash冲突，其要插入的数组桶是空的，就会直接插入，不会触发扩容，并不会导致无效扩容的情况

​	在JDK1.8的时候是先插入再扩容的，插入完了之后，再去判断是否达到了扩容阈值，来决定是否扩容。是统一将旧数组中的所有节点转移到新数组的对应位置中，每个节点在新数组中的位置是统一计算的。

```java
1、当hashmap的元素数量超过数组大小乘上加载因子时，就会进行数组扩容，加载因子的默认值为0.75
首先会判断当前map的元素数量是否大于最大限制的容量，如果大于则不能进行扩容了，设置临界值为int的最大值。
2、否则可以进行扩容，以2倍的方式进行扩容，先创建一个2倍大小的新数组，然后将旧数组的所有元素依次拷贝到新数组，并重新计算每个元素在新数组的索引位置，最后修改临界值。

// map的扩容方法
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table; //将原索引表记录为oldTab
    int oldCap = (oldTab == null) ? 0 : oldTab.length; //记录原索引表的长度
    int oldThr = threshold; //记录原map的临界容量
    int newCap, newThr = 0; //新表的索引表长度和新表的临界容量
    // 以前的容量大于0
    if (oldCap > 0) {
         //如果以前的容量大于限制的最大容量2^30,则设置临界值为int的最大值2^31-1这样以后就不会扩容了
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }

         //如果以前容量的2倍小于限制的最大容量，同时大于或等于默认的容量16，
         //设置临界值为以前临界值的2倍
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; 
    }
     // 此处相当于将上一次的初始容量赋值给了新的容量。什么情况下会执行到这句？当调用 	 
     // HashMap(int initialCapacity)构造器，还没有添加元素时
    else if (oldThr > 0) 
        newCap = oldThr;
     // 调用了默认构造器，初始容量没有设置，因此使用默认容量DEFAULT_INITIAL_CAPACITY（16），
     // 临界值就是16*0.75
    else {              
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    //对临界值做判断，确保其不为0，因为在上面第二种情况(oldThr > 0)，并没有计算newThr
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    
    threshold = newThr;
    //构造新表，初始化表中数据
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        //遍历将原来table中的数据放到扩容后的新表中来
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                 //没有链表Node节点，直接放到新的table中下标为【e.hash & (newCap - 1)】位置即可
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                //如果是treeNode节点，则树上的节点放到newTab中
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                //如果e后面还有链表节点，则遍历e所在的链表 
                else { // preserve order
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                         /**
                         * newTab的容量是以前旧表容量的两倍,因为数组table下标并不是根据循环逐步递增
                         * 的，而是通过（table.length-1）& hash计算得到，因此扩容后，存放的位置就
                         * 可能发生变化，那么到底发生怎样的变化呢，就是由下面的算法得到.
                         *
                         * 通过e.hash & oldCap来判断节点位置通过再次hash算法后，是否会发生改变，如
                         * 果为0表示不会发生改变，如果为1表示会发生改变。到底怎么理解呢，举个例子：
                         * e.hash = 13 二进制：0000 1101
                         * oldCap = 32 二进制：0001 0000
                         *  &运算：  0  二进制：0000 0000
                         * 结论：元素位置在扩容后不会发生改变
                         */
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        /**
                         * e.hash = 18 二进制：0001 0010
                         * oldCap = 32 二进制：0001 0000
                         * &运算：  32 二进制：0001 0000
                         * 结论：元素位置在扩容后会发生改变，那么如何改变呢？
                         * newCap = 64 二进制：0010 0000
                         * 通过(newCap-1)&hash
                         * 即0001 1111 & 0001 0010 得0001 0010，32+2 = 34
                         */
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        /**
                         * 若(e.hash & oldCap) == 0，下标不变，将原表某个下标的元素放到扩容表同样
                         * 下标的位置上
                         */
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        /**
                         * 若(e.hash & oldCap) != 0，将原表某个下标的元素放到扩容表中
                         * [下标+增加的扩容量]的位置上
                         */
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

##### 扩容时机

```tex
其中DEFAULT_INITIAL_CAPACITY是初始容量：默认值为16

在计算存入结点下标时，会利用 key 的 hsah 值进行取余操作，而计算机计算时，并没有取余等运算，会将取余转化为其他运算；当HashMap中的元素越来越多的时候，碰撞的几率也就越来越高，所以为了提高查询的效率，就要对HashMap的数组进行扩容；

那么HashMap什么时候进行扩容呢？
	当hashmap中的元素个数超过数组大小*loadFactor时，就会进行数组扩容，loadFactor的默认值为0.75，也就是说，默认情况下，数组大小为16，那么当hashmap中元素个数超过16*0.75=12的时候，就把数组的大小扩展为2*16=32
	
1、当put时发现table未初始化时，进行初始化扩容
2、当put加入节点后，发现size（键值对数量）> threshold时，进行扩容 
3、而在之后的每次扩容中，容量和阈值都变为原来的两倍，即两者仍然保持着0.75的比例
```

##### 为什么HashMap的长度必须是2的n次幂

```java
1、为了避免哈希碰撞
	可以看到HashMap在扩容时选择了位运算，向集合中添加元素时，会获取key的hash值, 使用(n - 1) & hash的计算方法来得出该元素在集合中的位置；
	这样与添加元素的hash值进行位运算时，能够充分的散列，使得添加的元素均匀分布在HashMap的每个位置上，减少hash碰撞；
2、源码解析如下：当HashMap的容量是16时，它的二进制是10000，(n-1)的二进制是01111；
	不同的hash值，和(n-1)去进行&运算，得到的结果是不一样的，使得添加的元素能够均匀分布在集合中不同的位置上，避免hash碰撞；
	如果不是2的n次幂容量，添加元素会导致生成一样的哈希值，严重hash碰撞，导致某一个链表的长度特别长，影响查询的效率；

// 有参构造, 给定容量
public HashMap(int var1, float var2) {
    if (var1 < 0) {
        throw new IllegalArgumentException("Illegal initial capacity: " + var1);
    } else {
        if (var1 > 1073741824) {
            var1 = 1073741824;
        }

        if (!(var2 <= 0.0F) && !Float.isNaN(var2)) {
            this.loadFactor = var2;
            this.threshold = tableSizeFor(var1); // 计算阈值
        } else {
            throw new IllegalArgumentException("Illegal load factor: " + var2);
        }
    }
}

static final int tableSizeFor(int var0) {
    int var1 = var0 - 1;
    var1 |= var1 >>> 1;
    var1 |= var1 >>> 2;
    var1 |= var1 >>> 4;
    var1 |= var1 >>> 8;
    var1 |= var1 >>> 16;
    return var1 < 0 ? 1 : (var1 >= 1073741824 ? 1073741824 : var1 + 1);
}
举例：
1.当传入的cap值为35时；
2.首先执行第一步 cap-1，得到n的值为34；
3.执行第二部：n或（n右移一位），34 二进制为：100010，n右移一位变成：010001，或操作结果为：110011；
4.n右移两位变成：001100，再与110011进行或操作，变成111111，此时n为111111；
5.n右移四位变成：000000，再与111111进行或操作，结果n仍为：111111；
6.n右移16为，再与111111进行或操作，n仍为111111；
7.n+1变成1000000，变成64；

```

##### 为什么HashMap的键值可以设为null？

```java
因为hashMap在对键值为空的时候做了处理：当key为空时，哈希值时会直接被赋值为0;

static final int hash(Object var0) {
	int var1;
	return var0 == null ? 0 : (var1 = var0.hashCode()) ^ var1 >>> 16;
}
```

##### 为什么不直接取余，而要>>>16？

```
	对于一个长度为length的数组，假设其为2的n次幂，length-1的结果就是n个1，其与任何数（如上图中的3、15）相与的结果都是保留其低位，间接性的实现了取模的效果。但是我们为什么不直接取模呢，这是因为&运算直接使用二进制位计算，能更快的达到效果。由于数组长度恒为2的n次幂，所以该结论必然存在。这也是我们每次扩容都是2倍的原因之一
	
	如果使用直接使用hashCode对数组大小取余，那么相当于参与运算的只有hashCode的低位，高位是没有起到任何作用的
	(h >>> 16)是无符号右移16位的运算，左边补0，得到 hashCode 的高16位，可以使得到的 hash 值更加散列，尽可能减少哈希冲突，提升性能;
	而这么来看 hashCode 被散列 (异或) 的是低16位，而 HashMap 数组长度一般不会超过2的16次幂，那么高16位在大多数情况是用不到的，所以只需要拿 key 的 HashCode 和它的低16位做异或即可利用高位的hash值，降低哈希碰撞概率也使数据分布更加均匀;
```

##### hash值计算

​	hash的计算，首先通过key获取到hashCode值，再对hashCode值右移16位，最后再与原hashCode值做异或运算。

​	在Java中，八种基本数据类型位数由小到大排序为：byte(1字节8位) < short(2字节16位) < char(2字节16位) < int(4字节32位) = float(4字节32位) < double(8字节64位) = long(8字节64位)、boolean

```java
其中整型int占用4个字节，二进制32位，举个简单的例子说明

Integer key = 1;
System.out.println(key.hashCode());

HashMap<Integer, Object> map = new HashMap<>();
map.put(key,"Justin");

int hash = 1 ^ (1 >>> 16);
System.out.println(hash);
示例中: 当key为1时，其hashCode值为1，源码(h = key.hashCode()) ^ (h >>> 16) 对应实现为 1 ^ ( 1 >>> 16) 

1  			0000 0000 0000 0000 0000 0000 0000 0001
1>>>16		0000 0000 0000 0000 0000 0000 0000 0000
    
			0000 0000 0000 0000 0000 0000 0000 0001
			0000 0000 0000 0000 0000 0000 0000 0000
1^(1>>>16)  0000 0000 0000 0000 0000 0000 0000 0001
    
  			0000 0000 0000 0000 0000 0000 0000 1111
    		0000 0000 0000 0000 0000 0000 0000 0001
(n-1)&hash  0000 0000 0000 0000 0000 0000 0000 0001
说明：异或位运算(不同则为1,相同则为0)
```

##### jdk1.8后对HashMap的改进

```
1 jdk1.7使用数组加链表的结构进行存储，在jdk1.8中如果链表长度大于8就会改用红黑树进行存储，加快查询速率。
2 以前hashmap在扩容时，复制链表时采用头插法，在多线程环境下如果两个线程同时用头插法操作链表就会出现环形链表的情况，后续get元素就会出现死循环，而1.8以后改用尾插法的方式复制链表，避免了环形链表出现，但是hashmap依然是线程不安全的。
3 jdk1.7扩容时要将旧表的所有数据重新进行hash计算，而1.8中扩容时只需将hashCode和老数组长度做与运算判断是0还是1，是0的话索引不变，是1的话索引变为老索引位置+老数组长度，不需要重新计算hash索引。
```

##### 为什么Hashmap中hash算法要将hashcode与hashcode高16位异或

​	通过hash算法将hashcode 与 hashcode的低16位做异或运算，混合了高位和低位得出的最终hash值，可以减小发生冲突的概率使得hash值分布更加随机。
​	因为在大多数情况下我们只使用到了低16位的值，高16位都是0，而新计算的hash值混合了高位和低位的信息，掺杂的元素多了，那么最终hash值的随机性越大。

##### 为什么hashmap中get和put方法的平均时间复杂度为1

​	get和put操作最浪费时间的地方在于对链表的遍历。而我们以前学过遍历链表的时间复杂度是n。
​	但是当map中元素数量为n，而hash函数保证了冲突的发生是小概率事件，所以链表的长度与n相比可以理解为常数级别。所以可以理解为链表的遍历的时间复杂度是常数级别。
​	况且链表还可以转化为红黑树进一步降低了时间复杂度。

##### 为什么Hashmap中求hash值的索引下标要使用hash & (length-1)

​	按理来说要让hash值在数组中的下标尽可能分散，可以将hash对数组的长度length取余。
​	但是取余运算的效率比较低，所以使用位运算hash & (length-1)来代替，它们的效果是一样的。

##### Hashmap的大小为什么是2的幂次

​	这个与数组索引i的计算方式有关，我们知道 i = hash%(length-1)
​	选择length为2的幂次是为了让数据分布更加均匀，减少碰撞的几率。
​	假如数组长度为15，那么所有hash值都要与14即1110相与，最终的结果最后一位一直为0即最终计算的下标都是偶数，这样奇数的下标就没有利用到造成浪费。

##### hashmap的初始容量为什么设置为16

​	首先解释为什么是2的幂次
​	初始容量设置越大，发生扩容可能性越小，但是内存占用越多，初始容量设置越小，发生扩容可能性越大，但是内存占用越少。所以选择16而不是8或者32是在性能和空间开销上的最佳平衡。

##### hashmap树化阈值多少，为什么？

​	在hashmap中当链表节点数量大于8会转化为红黑树，当红黑树节点数量小于6会退化为链表。我们知道链表的查找时间复杂度为n，红黑树查找的时间复杂度为log n，所以当n很小时链表查询就很快，而当n达到一定阈值就会转化为红黑树提高查询性能。但是为什么阈值是8呢。
​	这应该是红黑树和链表在性能上权衡的结果，当元素数量比较多时，红黑树的性能优于链表，但是当元素数量比较少只有几个的时候，红黑树的整体性能可能不如链表，因为红黑树还要涉及到平衡性调整等问题，在节点数量少的情况下这些开销是不值当的。
​	为什么退化阈值是6而不是8，主要是为了避免hashmap内部在链表和红黑树之间频繁切换。如果退化阈值是8，当链表个数在8左右徘徊，就会频繁的发生树转链表、链表转树，效率会很低。

##### ConcurrentHashMap与Hashtable的区别

​	Hashtable的任何操作都会把整个表锁住，是阻塞的。好处是总能获取最实时的更新，比如说线程A调用putAll写入大量数据，期间线程B调用get，线程B就会被阻塞，直到线程A完成putAll，因此线程B肯定能获取到线程A写入的完整数据。坏处是所有调用都要排队，效率较低。
​	ConcurrentHashMap采用的是分段锁机制，在jdk1.8中会给索引数组的每个索引都加一把锁，在更新时会锁住当前索引的数据而不会影响其他索引数据的更新。同步读取操作则是完全非阻塞的。好处是在保证合理的同步前提下，效率很高。坏处是严格来说读取操作不能保证反映最近的更新。例如线程A调用putAll写入大量数据，期间线程B调用get，则只能get到目前为止已经顺利插入的部分数据。

##### 多线程下 put 会导致元素丢失

​	多线程同时执行 put 操作时，如果计算出来的索引位置是相同的，那会造成前一个 key 被后一个 key 覆盖，从而导致元素的丢失。

##### put 和 get 并发时会导致 get 到 null

​	线程 A 执行put时，因为元素个数超出阈值而出现扩容，线程B 此时执行get。当线程 A 执行完 table = newTab 之后，线程 B 中的 table 此时也发生了变化，此时去 get 的时候就会 get 到 null 了，因为元素还没有迁移完成。

##### 红黑树与链表的互相转化

​	1、由链表转为红黑树

​		链表长度大于8，且数组长度大于等于64时会转换为红黑树

​	2、由于红黑树转为链表

​	（1）删除节点时触发

​       删除节点时，判断为红黑树，则会判断根节点、左右孩子以及左孙子节点是否为空，满足其一则认为红黑树中节点较少，已没有维持红黑树的必要，转换为链表。

​	（2）扩容时触发

​	第一步：遍历当前下标中所有节点，通过(hash & bit)得出的高位为0还是1得到2条链表。

​	第二步：
​		1、低位链表不为null，且长度小于等于6时，低位链表开始去树化操作。
​		2、低位链长度大于6，且高位链表不为空，低位链表进行树化
​		3、高位链表不为null，且长度小于等于6时，高位链表开始去树化操作。
​		4、高位链长度大于6，且低位链表不为空，高位链进行树化

```java

final void split(HashMap<K,V> map, Node<K,V>[] tab, int index, int bit) {
            TreeNode<K,V> b = this;
            // Relink into lo and hi lists, preserving order
            TreeNode<K,V> loHead = null, loTail = null;
            TreeNode<K,V> hiHead = null, hiTail = null;
            int lc = 0, hc = 0;
            for (TreeNode<K,V> e = b, next; e != null; e = next) {
                next = (TreeNode<K,V>)e.next;
                e.next = null;
                if ((e.hash & bit) == 0) {//区分树链表的高低位
                    if ((e.prev = loTail) == null)//低位尾部标记为null,表示还未开始处理，此时e是第一个要处理的低位树链表
                    // 节点，故e.prev等于loTail都等于null
                        loHead = e;//低位树链表的第一个树链表节点
                    else
                        loTail.next = e;
                    loTail = e;
                    ++lc;//低位树链表元素个数计数
                }
                else {
                    if ((e.prev = hiTail) == null)
                        hiHead = e;//高位树链表的第一个树链表节点
                    else
                        hiTail.next = e;
                    hiTail = e;
                    ++hc;//高位树链表元素个数计数
                }
            }
 
            if (loHead != null) {//低位树链表不为null
                if (lc <= UNTREEIFY_THRESHOLD)//低位树链表元素个数若小于等于6
                    tab[index] = loHead.untreeify(map);//开始去树化操作(就是将元素TreeNode节点都转换成Node节点)
                else {
                    tab[index] = loHead;
                    if (hiHead != null) // (else is already treeified) //若高位数链表头节点为空，说明还没有处理完高位
                    //，还不能进行树化操作
                        loHead.treeify(tab);//低位树链表元素个数若大于6且高位树链表头节点不等于null，开始将低位树链表真
                        //正树化成红黑树(前面都只是挂着TreeNode的名号，但实际只是链表结构，还没包含红黑树的特性，
                        //在这里才赋予了它红黑树的特性)
                }
            }
            if (hiHead != null) {//高位树链表不为null
                if (hc <= UNTREEIFY_THRESHOLD)//高位树链表元素个数若小于等于6
                    tab[index + bit] = hiHead.untreeify(map);//开始去树化操作(就是将元素TreeNode节点都转换成Node节点)
                else {
                    tab[index + bit] = hiHead;
                    if (loHead != null) //若低位数链表头节点为空，说明还没有处理完低位，还不能进行树化操作
                        hiHead.treeify(tab);//高位树链表元素个数若大于6且低位树链表头节点不等于null，
                        //开始将高位树链表真正树化成红黑树
                }
            }
```

#### ② LinkedHashMap

```java
LinkedHashMap() 										使用默认初始容量16和默认加载因子0.75创建一个LinkedHashMap
LinkedHashMap(int initialCapacity)						使用自定义初始容量和默认加载因子0.75创建一个LinkedHashMap
LinkedHashMap(int initialCapacity, float loadFactor)	使用自定义初始容量和自动以加载因子创建一个LinkedHashMap
LinkedHashMap(int initialCapacity, float loadFactor, boolean accessOrder)
    使用自定义初始容量和自动以加载因子创建一个LinkedHashMap,并且可以设置accessOrder,accessOrder为true，调用get方法会改变内部结构，这一点后面会细讲
LinkedHashMap(Map<? extends K,? extends V> m)			使用Map子类实例创建一个LinkedHashMap
```

##### 无参构造

```java
public LinkedHashMap() {
   //调用父类HashMap构造方法
　　super();
　　//accessOrder：是否开启访问顺序,当设置为true的时候，会将访问到的元素放到链表的最后
　　accessOrder = false;
}
```

##### 有参构造

```java
public LinkedHashMap(int initialCapacity, float loadFactor) { // 自定义初始容量和加载因子
   //调用父类HashMap构造方法
　　super(initialCapacity, loadFactor);
   //accessOrder：是否开启访问顺序,当设置为true的时候，会将访问到的元素放到链表的最后
　　accessOrder = false;
}
```

##### get方法

​	点开accessOrder的调用处，发现除了构造方法外，只有3处使用了accessOrder字段,分别是get()方法,getOrDefault()方法和afterNodeAccess（）方法。

```Java
public V get(Object key) {
　　Node<K,V> e;
　　if ((e = getNode(hash(key), key)) == null)
　　　　return null;
   //如果accessOrder为true，调用afterNodeAccess()方法,传入get()方法取出的节点e
　　if (accessOrder)
　　　　afterNodeAccess(e);
　　return e.value;
}
// 将访问的元素放到链表的末尾
void afterNodeAccess(Node<K,V> e) { // move node to last
   //声明最后一个节点last
　　LinkedHashMap.Entry<K,V> last;
   //如果是访问序，且当前节点并不是尾节点, 将该节点置为双向链表的尾部
   // 如果accessOrder为true并且最后一个节点tail不等于get()方法取出的节点e
　　if (accessOrder && (last = tail) != e) {
       // 将e复制给p,取出p的前节点和后节点分别复制给b和a
　　　　LinkedHashMap.Entry<K,V> p = (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
       // 将p的后节点置为null
　　　　p.after = null;
　　　　if (b == null)
　　　　　　head = a;
　　　　else
　　　　　　b.after = a;
　　　　if (a != null)
　　　　　　a.before = b;
　　　　else
　　　　　　last = b;
　　　　if (last == null)
　　　　　　head = p;
　　　　else {
　　　　　　p.before = last;
　　　　　　last.after = p;
　　　　}
       // tail是LinkedHashMap的成员变量,保存的是一份最新的节点数据。
       // transient LinkedHashMap.Entry<K,V> tail;
       // 有最新的便有最老的，LinkedHashMap中使用head变量保存。
       // transient LinkedHashMap.Entry<K,V> head;
　　　　tail = p; 
　　　　++modCount;
　　}
}

// 最终的效果是将get()方法取出的节点，放到了链表的最后，使之成为尾部节点，从而改变了数据在LinkedHashMap中的存储顺序，它的迭代顺序就是最后访问其条目的顺序。这种特性很适合构建LRU Cache
```

##### put()方法

​	LinkedHashMap的put()方法继承至HashMap,没有进行重写。此处不再赘述HashMap的put()方法。LinkedHashMap对创建节点的方法进行了重写

##### newNode()重写

```Java
Node<K,V> newNode(int hash, K key, V value, Node<K,V> e) {
　　//创建一个LinkedHashMap.Entry对象
　　LinkedHashMap.Entry<K,V> p =
　　　　new LinkedHashMap.Entry<K,V>(hash, key, value, e);
   //将p放到链表的最后
　　linkNodeLast(p);
　　return p;
}
// LinkedHashMap.Entry是LinkedHashMap的静态内部类，它就是所谓的双向链表,继承至HashMap.Node类，并在此基础上扩展了两个属性:前节点和后节点
static class Entry<K,V> extends HashMap.Node<K,V> {
　　//定义了两个变量，分别是当前节点的前一个节点和后一个节点
　　Entry<K,V> before, after;
　　Entry(int hash, K key, V value, Node<K,V> next) {
　　　　super(hash, key, value, next);
　　}
}

// linkNodeLast(),它的作用是将新节点放到链表的最后
private void linkNodeLast(LinkedHashMap.Entry<K, V> var1) {
    //将tail保存一份
    LinkedHashMap.Entry<K,V> last = tail;
    //将最新的节点p赋值给tail
    tail = p;
    if (last == null)
      //如果链表是空的，将p放在链表头部
      head = p;
    else {
      //如果链表不为空，将p放到链表的尾部
      p.before = last;
      last.after = p;
    }
}

```

##### remove()方法

​	对于remove方法，在LinkedHashMap中也没有重写，它调用的还是父类的HashMap的remove()方法，在LinkedHashMap中重写的是：afterNodeRemoval(Node e)这个方法，在Hash表的元素被删除之后，删除双向链表的元素。

```Java
void afterNodeRemoval(Node<K,V> e) { // unlink
　　LinkedHashMap.Entry<K,V> p =
　　　　(LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
　　p.before = p.after = null;
　　if (b == null)
　　　　head = a;
　　else
　　　　b.after = a;
　　if (a == null)
　　　　tail = b;
　　else
　　　　a.before = b;
}
```

##### Iterator遍历

​	LinkedHashMap重写了entrySet()方法

```Java
public Set<Map.Entry<K,V>> entrySet() {
　　Set<Map.Entry<K,V>> es;
　　return (es = entrySet) == null ? (entrySet = new LinkedEntrySet()) : es;
}

// LinkedHashMap实际上是对双向链表LinkedHashMap.Entry遍历。

abstract class LinkedHashIterator {
　　LinkedHashMap.Entry<K,V> next;
　　LinkedHashMap.Entry<K,V> current;
　　int expectedModCount;

　　LinkedHashIterator() {
　　　　next = head;
　　　　expectedModCount = modCount;
　　　　current = null;
　　}

　　public final boolean hasNext() {
　　　　return next != null;
　　}

　　final LinkedHashMap.Entry<K,V> nextNode() {
       // 遍历双向链表
　　　　LinkedHashMap.Entry<K,V> e = next;
　　　　if (modCount != expectedModCount)
　　　　　　throw new ConcurrentModificationException();
　　　　if (e == null)
　　　　　　throw new NoSuchElementException();
　　　　current = e;
　　　　next = e.after;
　　　　return e;
　　}

　　public final void remove() {
　　　　Node<K,V> p = current;
　　　　if (p == null)
　　　　　　throw new IllegalStateException();
　　　　if (modCount != expectedModCount)
　　　　　　throw new ConcurrentModificationException();
　　　　current = null;
　　　　K key = p.key;
　　　　removeNode(hash(key), key, null, false, false);
　　　　expectedModCount = modCount;
　　}
}
```

1. LinkedHashMap通过在HashMap的基础上增加一条双向链表，实现了插入顺序和访问顺序一致。通过对HashMap一些方法的覆盖,例如newNode, replacementNode, replacementTreeNode, newTreeNode，**让所有对底层HashMap数据结构修改的同时该链表进行修改,遍历的时候便是遍历这一条有序的链表**。需要注意的是get()方法在accessOrder为true的时候也会对底层结构进行修改。
2. 基于get()在accessOrder为true时，会将访问到的元素放到链表的最后的特性，我们可以使用LinkedHashMap实现LRU缓存。
3. LinkedHashMap同HashMap一样，线程不安全。

​	在LinkedHashMap重写的方法中，我们可以看出默认情况下（即accessOrder为false时）双向链表的顺序（即head至tail的顺序）即为节点的插入顺序，每次插入的节点都会按照尾插法加入到链表的尾部，因此双向链表的迭代顺序为插入顺序。

​	当我们将accessOrder修改为true时，每次访问的节点会被调整至链表尾部，这样，经过一段时间后链表头留下的就是最近最久未被访问的节点，链表迭代下来的顺序即为读取的顺序。



##### LinkedHashMap重写的方法

​	1、afterNodeAccess
​        在上文中，get方法在结束前，会根据accessOrder 是否为treu判断是否要调整双链表，如果accessOrder在初始化时被设置为了true，afterNodeAccess方法会在get方法被执行后，将被调访问的该节点调整至双链表的最后。

​	2、afterNodeInsertion
​        该方法在插入操作后，会根据removeEldestEntry判断是否删除双链表表头元素。在LinkedHashMap中，由于removeEldestEntry的返回值固定为false,因此这个方法并不会被启动，但是我们可以在子类中重写该方法。

```Java
// 插入新节点才会触发该方法，因为只有插入新节点才需要内存
// 根据 HashMap 的 putVal 方法, evict 一直是 true
// removeEldestEntry 方法表示移除规则, 在 LinkedHashMap 里一直返回 false
// 所以在 LinkedHashMap 里这个方法相当于什么都不做
void afterNodeInsertion(boolean evict) { // possibly remove eldest
    LinkedHashMap.Entry<K,V> first;
    // 根据条件判断是否移除最近最少被访问的节点
    if (evict && (first = head) != null && removeEldestEntry(first)) {
        K key = first.key;
        removeNode(hash(key), key, null, false, true);
    }
}
 
// 移除最近最少被访问条件之一，通过覆盖此方法可实现不同策略的缓存
// LinkedHashMap是默认返回false的，我们可以继承LinkedHashMap然后复写该方法即可
protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
    return false;
}


// 例如LRU算法中，我们规定链表只能存储固定数量的节点，因此在新增后我们需要删除最长时间没被访问过的节点（结合前文中的afterNodeAccess方法，每次访问该元素都会被调整至尾部），则需要重写removeEldestEntry方法，当节点超过限制时删除表头。

// LeetCode146. LRU 缓存
class LRUCache extends LinkedHashMap<Integer, Integer>{
    private int capacity;
    
    public LRUCache(int capacity) {
        super(capacity, 0.75F, true);
        this.capacity = capacity;
    }
 
    public int get(int key) {
        return super.getOrDefault(key, -1);
    }
 
    @Override
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity; 
    }
}
```

​	3、afterNodeRemoval

​    在删除元素之后，将元素从双向链表中删除。

```java
//在节点删除后，维护链表，传入删除的节点
void afterNodeRemoval(Node<K,V> e) { // unlink
    //p指向待删除元素，b执行前驱，a执行后驱
    LinkedHashMap.Entry<K,V> p =
        (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
    //这里执行双向链表删除p节点操作，很简单。
    p.before = p.after = null;
    if (b == null)
        head = a;
    else
        b.after = a;
    if (a == null)
        tail = b;
    else
        a.before = b;
}
```

#### ③ TreeMap

使用场景：需要按照key进行排序

特点：

​	1、是NavigableMap的直接实现类

​	2、key不允许为null, 会抛出空指针异常

​	3、TreeMap的底层数据结构为红黑树(先天支持排序)，主要是根据key进行排序，支持自定义实现的比较器或者key实现 `Comparable` 接口

​	4、存储空间少，`Treemap` 红黑树中一个节点对应一个kv对，没有冗余无效的Node节点

优点：

​	1、底层为红黑树,先天支持排序

​	2、存储空间少

缺点：

​	1、查询的时间复杂度，TreeMap 的查询时间复杂度为 `O(logN)`, 而HashMap为 `O(1)`，所以Map的元素越多，TreeMap根据key查询的效率会更低；

​	2、修改导致重排，`TreeMap` 每次新增or删除一个kv对，都可能导致红黑树的重排，`HashMap` 当新增一个kv对，使得Map中的个数大于阀值时，需要对数组进行重新扩容

场景：

​	对Map中的kv对有排序需求时，选择 `TreeMap`, 这种场景下，尽量保证以下几点

- Map 元素基本保持不变（即很少添加和删除）
- 主要用于遍历迭代获取数据
- 不存在碰撞的情况（即两个不同的key，根据比较器获取值不能为0）

```Java
public class TreeMap<K, V> extends AbstractMap<K, V> implements NavigableMap<K, V>, Cloneable, Serializable {
```

**SortedMap接口**

```Java
public interface SortedMap<K, V> extends Map<K, V> {
    Comparator<? super K> comparator(); // 返回用于排序此映射中的键的比较器，如果此映射使用其键的自然排序，则返回null。
    SortedMap<K, V> subMap(K var1, K var2);
    SortedMap<K, V> headMap(K var1);
    SortedMap<K, V> tailMap(K var1);
    K firstKey(); 	// 返回当前映射中的第一个(最低)键。
    K lastKey(); 	// 返回当前映射中的最后(最高)键。
    Set<K> keySet(); 
    Collection<V> values();
    Set<Entry<K, V>> entrySet(); // 返回此映射中包含的映射的Set视图。
}
```

**NavigableMap接口**

```java
public interface NavigableMap<K, V> extends SortedMap<K, V> {
    // 返回与严格小于给定键的最大键关联的键值映射，如果没有这样的键，则返回null。
    Entry<K, V> lowerEntry(K var1);
    K lowerKey(K var1);
    // 返回与小于或等于给定键的最大键相关联的键值映射，如果没有这样的键则返回null。
    Entry<K, V> floorEntry(K var1);
    K floorKey(K var1);
    
    // 返回与大于或等于给定键的最小键相关联的键值映射，如果没有这样的键则返回null。
    Entry<K, V> ceilingEntry(K var1);
    // 返回大于或等于给定键的最小键，如果没有这样的键，则返回null。
    K ceilingKey(K var1);
    
    // 返回与严格大于给定键的最小键关联的键值映射，如果没有这样的键，则返回null。
    Entry<K, V> higherEntry(K var1);
    K higherKey(K var1);
    Entry<K, V> firstEntry(); // 返回与该映射中最小的键关联的键值映射，如果映射为空，则返回null。
    Entry<K, V> lastEntry(); // 返回与此映射中最大键关联的键值映射，如果映射为空，则返回null。
    
    // 删除并返回与该映射中最小的键关联的键值映射，如果映射为空，则返回null。
    Entry<K, V> pollFirstEntry();
    // 删除并返回与此映射中最大键关联的键值映射，如果映射为空，则返回null。
    Entry<K, V> pollLastEntry();
    NavigableMap<K, V> descendingMap(); // 返回此映射中包含的映射的倒序视图。
    NavigableSet<K> navigableKeySet();
    NavigableSet<K> descendingKeySet();
    NavigableMap<K, V> subMap(K var1, boolean var2, K var3, boolean var4);
    
    // 返回该映射中键严格小于toKey的部分的视图。
    NavigableMap<K, V> headMap(K var1, boolean var2);
    NavigableMap<K, V> tailMap(K var1, boolean var2);
    
    // 返回该映射中键范围从fromKey(包含)到toKey(独占)的部分的视图。
    SortedMap<K, V> subMap(K var1, K var2);
    SortedMap<K, V> headMap(K var1);
    SortedMap<K, V> tailMap(K var1); // 返回该映射中键大于或等于fromKey的部分的视图。
}
```





```Java
// 这是一个比较器，方便插入查找元素等操作
private final Comparator<? super K> comparator; // 作为key的排序规则
// 红黑树的根节点：每个节点是一个Entry
private transient Entry<K,V> root; // root是红黑树的根节点，说明的确底层用的红黑树作为数据结构。
// 集合元素数量
private transient int size = 0;
// 集合修改的记录
private transient int modCount = 0;
```

Entry

```Java

static final class Entry<K,V> implements Map.Entry<K,V> {
    K key;
    V value;
    //左子树
    Entry<K,V> left;
    //右子树
    Entry<K,V> right;
    //父节点
    Entry<K,V> parent;
    //每个节点的颜色：红黑树属性。
    boolean color = BLACK;
    Entry(K key, V value, Entry<K,V> parent) {
        this.key = key;
        this.value = value;
        this.parent = parent;
    }
    public K getKey() {
        return key;
    }
    public V getValue() {
        return value;
    }
    public V setValue(V value) {
        V oldValue = this.value;
        this.value = value;
        return oldValue;
    }

    public boolean equals(Object o) {
        if (!(o instanceof Map.Entry))
            return false;
        Map.Entry<?,?> e = (Map.Entry<?,?>)o;

        return valEquals(key,e.getKey()) && valEquals(value,e.getValue());
    }

    public int hashCode() {
        int keyHash = (key==null ? 0 : key.hashCode());
        int valueHash = (value==null ? 0 : value.hashCode());
        return keyHash ^ valueHash;
    }

    public String toString() {
        return key + "=" + value;
    }
}
```

##### get方法

​	TreeMap基于红黑树实现，而红黑树是一种自平衡二叉查找树，所以 TreeMap 的查找操作流程和二叉查找树一致。

​	二叉树的查找流程是这样的，先将目标值和根节点的值进行比较，如果目标值小于根节点的值，则再和根节点的左孩子进行比较。如果目标值大于根节点的值，则继续和根节点的右孩子比较。

​	在查找过程中，如果目标值和二叉树中的某个节点值相等，则返回 true，否则返回 false。TreeMap 查找和此类似，只不过在 TreeMap 中，节点（Entry）存储的是键值对<k,v>。

​	在查找过程中，比较的是键的大小，返回的是值，如果没找到，则返回null。TreeMap 中的查找方法是get。

```Java
public V get(Object key) {
        //调用 getEntry方法查找
        Entry<K,V> p = getEntry(key);
        return (p==null ? null : p. value);
}
 
final Entry<K,V> getEntry(Object key) {
    / 如果比较器为空，只是用key作为比较器查询
    if (comparator != null) 
        return getEntryUsingComparator(key);
    if (key == null)
        throw new NullPointerException();
    Comparable<? super K> k = (Comparable<? super K>) key;
    // 取得root节点
    Entry<K,V> p = root;
   //核心来了：从root节点开始查找，根据比较器判断是在左子树还是右子树
    while (p != null) {
        int cmp = k.compareTo(p.key );
        if (cmp < 0)
            p = p. left;
        else if (cmp > 0)
            p = p. right;
        else
           return p;
    }
}
```

##### put方法

```Java
public V put(K key, V value) {
    Entry<K,V> t = root;
    // 1.如果根节点为 null，将新节点设为根节点
    if (t == null) {
        compare(key, key); // type (and possibly null) check

        root = new Entry<>(key, value, null);
        size = 1;
        modCount++;
        return null;
    }
    //如果root不为null，说明已存在元素 
    int cmp;
    Entry<K,V> parent;
    // split comparator and comparable paths
    Comparator<? super K> cpr = comparator;
    //如果比较器不为null 则使用比较器
    if (cpr != null) {
        //找到元素的插入位置
        do {
            parent = t;
            cmp = cpr.compare(key, t.key);
            //当前key小于节点key 向左子树查找
            if (cmp < 0)
                t = t.left;
            //当前key大于节点key 向右子树查找
            else if (cmp > 0)
                t = t.right;
            else
                //相等的情况下 直接更新节点值
                return t.setValue(value);
        } while (t != null);
    }
    //如果比较器为null 则使用默认比较器
    else {
        //如果key为null  则抛出异常
        if (key == null)
            throw new NullPointerException();
        @SuppressWarnings("unchecked")
        Comparable<? super K> k = (Comparable<? super K>) key;
        //找到元素的插入位置
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
    Entry<K,V> e = new Entry<>(key, value, parent);
    //根据比较结果决定插入到左子树还是右子树
    if (cmp < 0)
        parent.left = e;
    else
        parent.right = e;
    //保持红黑树性质，进行红黑树的旋转等操作
    fixAfterInsertion(e);
    size++;
    modCount++;
    return null;
}

// 比较关键的就是fixAfterInsertion方法， 看懂这个方法需要你对红黑树的机制比较了解。
private void fixAfterInsertion(Entry<K,V> x) {
    // 将新插入节点的颜色设置为红色
    x. color = RED;
    // while循环，保证新插入节点x不是根节点或者新插入节点x的父节点不是红色（这两种情况不需要调整）
    while (x != null && x != root && x. parent.color == RED) {
        // 如果新插入节点x的父节点是祖父节点的左孩子
        if (parentOf(x) == leftOf(parentOf (parentOf(x)))) {
            // 取得新插入节点x的叔叔节点
            Entry<K,V> y = rightOf(parentOf (parentOf(x)));
            // 如果新插入x的父节点是红色
            if (colorOf(y) == RED) {
                // 将x的父节点设置为黑色
                setColor(parentOf (x), BLACK);
                // 将x的叔叔节点设置为黑色
                setColor(y, BLACK);
                // 将x的祖父节点设置为红色
                setColor(parentOf (parentOf(x)), RED);
                // 将x指向祖父节点，如果x的祖父节点的父节点是红色，按照上面的步奏继续循环
                x = parentOf(parentOf (x));
            } else {
                // 如果新插入x的叔叔节点是黑色或缺少，且x的父节点是祖父节点的右孩子
                if (x == rightOf( parentOf(x))) {
                    // 左旋父节点
                    x = parentOf(x);
                    rotateLeft(x);
                }
                // 如果新插入x的叔叔节点是黑色或缺少，且x的父节点是祖父节点的左孩子
                // 将x的父节点设置为黑色
                setColor(parentOf (x), BLACK);
                // 将x的祖父节点设置为红色
                setColor(parentOf (parentOf(x)), RED);
                // 右旋x的祖父节点
                rotateRight( parentOf(parentOf (x)));
            }
        } else { // 如果新插入节点x的父节点是祖父节点的右孩子和上面的相似
            Entry<K,V> y = leftOf(parentOf (parentOf(x)));
            if (colorOf(y) == RED) {
                setColor(parentOf (x), BLACK);
                setColor(y, BLACK);
                setColor(parentOf (parentOf(x)), RED);
                x = parentOf(parentOf (x));
            } else {
                if (x == leftOf( parentOf(x))) {
                    x = parentOf(x);
                    rotateRight(x);
                }
                setColor(parentOf (x), BLACK);
                setColor(parentOf (parentOf(x)), RED);
                rotateLeft( parentOf(parentOf (x)));
            }
        }
    }
    // 最后将根节点设置为黑色
    root.color = BLACK;
}
```

##### remove方法

​	最终还是落到了对红黑树节点的删除上，需要维持红黑树的特性

```Java
public V remove(Object key) {
    // 根据key查找到对应的节点对象
    Entry<K,V> p = getEntry(key);
    if (p == null)
        return null;

    // 记录key对应的value，供返回使用
    V oldValue = p. value;
    // 删除节点
    deleteEntry(p);
    return oldValue;
}

private void deleteEntry(Entry<K,V> p) {
    modCount++;
    //元素个数减一
    size--;
    // 如果被删除的节点p的左孩子和右孩子都不为空，则查找其替代节
    if (p.left != null && p. right != null) {
        // 查找p的替代节点
        Entry<K,V> s = successor (p);
        p. key = s.key ;
        p. value = s.value ;
        p = s;
    }
    Entry<K,V> replacement = (p. left != null ? p.left : p. right);
    if (replacement != null) { 
        // 将p的父节点拷贝给替代节点
        replacement. parent = p.parent ;
        // 如果替代节点p的父节点为空，也就是p为跟节点，则将replacement设置为根节点
        if (p.parent == null)
            root = replacement;
        // 如果替代节点p是其父节点的左孩子，则将replacement设置为其父节点的左孩子
        else if (p == p.parent. left)
            p. parent.left   = replacement;
        // 如果替代节点p是其父节点的左孩子，则将replacement设置为其父节点的右孩子
        else
            p. parent.right = replacement;
        // 将替代节点p的left、right、parent的指针都指向空
        p. left = p.right = p.parent = null;
        // 如果替代节点p的颜色是黑色，则需要调整红黑树以保持其平衡
        if (p.color == BLACK)
            fixAfterDeletion(replacement);
    } else if (p.parent == null) { // return if we are the only node.
        // 如果要替代节点p没有父节点，代表p为根节点，直接删除即可
        root = null;
    } else {
        // 如果p的颜色是黑色，则调整红黑树
        if (p.color == BLACK)
            fixAfterDeletion(p);
        // 下面删除替代节点p
        if (p.parent != null) {
            // 解除p的父节点对p的引用
            if (p == p.parent .left)
                p. parent.left = null;
            else if (p == p.parent. right)
                p. parent.right = null;
            // 解除p对p父节点的引用
            p. parent = null;
        }
    }
}
```

#### ⑤ EnumMap

​	底层使用数组存储数据，根据枚举key的ordinal序号，查询和存储到数组中，并且初始化map时会根据枚举中定义的枚举常量个数初始化数组的大小

​	使用场景：key为枚举类型


```java
// 1、创建 EnumMap
// 创建一个空的 EnumMap，并指定枚举类型
EnumMap<Weekday, Integer> map1 = new EnumMap<>(Weekday.class);

// 2、添加和更新键值对
// 添加一个键值对到 EnumMap
map1.put(Weekday.MONDAY, 10);
map1.put(Weekday.TUESDAY, 2);
map1.put(Weekday.WEDNESDAY, 3);
// 更新一个键对应的值
map1.put(Weekday.MONDAY, 1);

// 3、获取和删除键值对
// 获取指定键对应的值
Integer value1 = map1.get(Weekday.MONDAY);
// 删除指定键对应的键值对
map1.remove(Weekday.MONDAY);

// 4、检查键是否存在
// 检查 EnumMap 是否包含指定键
boolean containsKey = map1.containsKey(Weekday.MONDAY);

// 5、大小和清空操作
// 获取 EnumMap 中键值对的数量
int size = map1.size();
// 清空 EnumMap 中的所有键值对
// map1.clear();

// 6、遍历键值对
// 使用迭代器遍历 EnumMap 的键值对
Iterator<Map.Entry<Weekday, Integer>> iterator = map1.entrySet().iterator();
while (iterator.hasNext()) {
Map.Entry<Weekday, Integer> entry = iterator.next();
Weekday key = entry.getKey();
Integer value = entry.getValue();
System.out.println("key="+key+",value="+value);
}
// 使用增强的 for 循环遍历 EnumMap 的键值对
for (Map.Entry<Weekday, Integer> entry : map1.entrySet()) {
Weekday key = entry.getKey();
Integer value = entry.getValue();
System.out.println("key="+key+",value="+value);
}
```

#### ⑥ IdentityHashMap

​	1、使用对象的内存地址作为键（key）进行存储和查找

​	2、`IdentityHashMap` 不能保证映射顺序

```java
TestInfo testInfo1 = new TestInfo(1, "测试信息1");
TestInfo testInfo2 = new TestInfo(2, "测试信息2");
TestInfo testInfo3 = new TestInfo(3, "测试信息3");
TestInfo testInfo4 = new TestInfo(4, "测试信息4");
TestInfo testInfo5 = new TestInfo(5, "测试信息5");

//创建一个 IdentityHashMap
IdentityHashMap map = new IdentityHashMap ();
//添加元素
map.put(testInfo1.getId(), testInfo1);
map.put(testInfo2.getId(), testInfo2);
map.put(testInfo3.getId(), testInfo3);
map.put(testInfo4, testInfo4);
map.put(testInfo5, testInfo5);

map.put(testInfo5.getId(), testInfo5);

//删除元素
map.remove(testInfo3.getId());


//根据Key循环查询
Set keys = map.keySet();
for (Object obj : keys) {
    TestInfo testInfo = null;
    if(obj instanceof Integer){
        int id = (int)obj;
        testInfo = (TestInfo)map.get(id);
    }
    else{
        testInfo = (TestInfo)obj;
    }
    System.out.println("根据Key 循环输出元素 "+testInfo.getName());
}

//根据entry循环查询
Set entry = map.entrySet();
for (Object obj : entry) {
    Map.Entry me = (Map.Entry)obj;
    Object key = me.getKey();
    TestInfo testInfo = (TestInfo)me.getValue();
    System.out.println("根据Entry 循环输出元素 "+testInfo.getName());
}
```

#### ⑦ConcurrentHashMap

​	jdk 1.7采用分段锁技术，整个Hash表被分成多个段，每个段中会对应一个Segment段锁，段与段之间可以并发访问，但是多线程想要操作同一个段是需要获取锁的。所有的 put、get和remove 等方法都是根据键的hash值对应到相应的段中，然后尝试获取锁进行访问。

![image-20240109170232106](/image-20240109170232106.png)

​	jdk 1.8取消了基于Segment的分段锁思想，改用`CAS + synchronized`控制并发操作，在某些方面提升了性能。并且追随1.8版本的HashMap底层实现，使用**数组+链表+红黑树**进行数据存储。

![image-20240109170301071](/image-20240109170301071.png)

ConcurrentHashMap添加元素的整体流程如下：
（1）桶数组如果还没初始化，就先初始化
（2）根据元素的key计算hash值，然后用hash值去定义桶位
（3）桶位如果为空，就直接插入元素
（4）桶位的元素如果被迁移了，说明当前桶处于扩容过程，当前线程应该要先帮助完成扩容
（5）桶位有元素，且没有被迁移，就锁住这个桶位
（6）如果桶位元素是链表节点，就遍历链表添加元素，更新或新增元素
（7）如果桶位元素是红黑树节点，就往红黑树添加元素，更新或新增元素
（8）如果元素存在，返回旧值
（9）如果元素不存在，桶的元素个数+1，,然后判断添加元素后的桶是否要扩容
添加元素使用了自旋锁 + CAS + synchronized来保证线程安全的



##### 成员变量

```Java
private static final int MAXIMUM_CAPACITY = 1 << 30;	// 桶数组的最大容量
private static final int DEFAULT_CAPACITY = 16;			// 桶数组的默认容量
static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;// 最大数组大小 不是2的n次方
private static final int DEFAULT_CONCURRENCY_LEVEL = 16;// 默认并发级别JDK1.7用的
// 默认加载因子，不能修改 和HashMap不一样 HashMap的可以通过构造方法修改
private static final float LOAD_FACTOR = 0.75f;
static final int TREEIFY_THRESHOLD = 8 		// 链表树化的阈值 大于等于这个值就树化
static final int UNTREEIFY_THRESHOLD = 6 	// 树退化成链表的阈值 小于等于这个值就退化成链表

// 链表树化桶数组的阈值 桶数组的大小要大于等于这个值链表才允许树化成红黑树 否则就直接扩容 
static final int MIN_TREEIFY_CAPACITY = 64
private static final int MIN_TRANSFER_STRIDE = 16 // 迁移数据时一个线程所能处理的最多桶位 
private static int RESIZE_STAMP_BITS = 16	// 用来计算扩容标识戳 每次扩容的标识戳都不一样

// 最多能有多少个线程参与并发扩容
// 1左移16位后-1 相当于2的16次方-1 = 65535 最多能有65535个线程参与并发扩容
private static final int MAX_RESIZERS = (1 << (32 - RESIZE_STAMP_BITS)) - 1

//用来算扩容标识戳的 sizeCtl高16表示扩容标识戳 所以要右移16位来判断当前线程是否是当前参与扩容的线程
private static final int RESIZE_STAMP_SHIFT = 32 - RESIZE_STAMP_BITS

//节点hash值的特殊含义
static final int MOVED  = -1; 				//表示当前节点已经被迁移了 即处于扩容状态
static final int TREEBIN   = -2 			//表示当前节点是红黑树节点
static final int RESERVED  = -3 			//表示翻转节点
static final int HASH_BITS = 0x7fffffff 	//作用是把hash值变为正数

static final int NCPU = Runtime.getRuntime().availableProcessors();// cpu数
```

##### 关键字段

```Java
transient volatile Node<K,V>[] table//存储元素的桶数组
//新表的引用  扩容时，旧表的元素会迁移到这个新表，扩容完成之后会被置空
private transient volatile Node<K,V>[] nextTable
//LongAdder的baseCount 没有发生线程竞争时，增量都存储在这里
 private transient volatile long baseCount;

//用来表示table的状态，可正可负
//负数时有两种情况：1、-1时表示table正在进行初始化，其他线程要自旋等待进行初始化的线程
//               2、-(1 + nThread),当前有n个线程进行并发扩容
//= 0时，用默认容量16初始化table
//> 0 时：如果table还没初始化，就表示始化大小 如果table已经初始化，就表示下次扩容的阈值
private transient volatile int sizeCtl
// 这是一个非常重要的属性，无论是初始化哈希表，还是扩容 rehash 的过程，都是需要依赖这个关键属性的
// 该属性有以下几种取值：
// 		sizeCtl=0，代表数组未被初始化，默认初始容量为16；
// 		sizeCtl>0，如果数组未初始化，那么记录数组的初始容量。如果数组已初始化，那么其记录数组的扩容阈值（数组的初始容量 * 0.75）；
//		sizeCtl=-1，表示是数组正在解析初始化；
// 		sizeCtl<0 && sizeCtl !=-1，表述数组正在扩容，-(1+n)表示此事有n个线程正在共同完成数组的扩容操作。


//表示扩容时，已经处理到哪个桶位了 
//例如桶位大小为32，transferIndex的初始化时是桶位大小即32
//第一个线程被分配到处理第17个到第32个桶位，然后transferIndex变为16
//第二个线程根据transferIndex被分配到处理第1个到16个桶位
private transient volatile int transferIndex;

//0表示LongAdder对象处于无锁 1表示加锁
private transient volatile int cellsBusy

//LongAdder的cell数组 baseCount发生竞争后会创建这个数组，然后线程通过计算的hash值去取对应的cell，然后把增量加到这个cell里
//桶数组的元素个数是baseCount的数加上每个cell的数
private transient volatile CounterCell[] counterCells
```

##### 重要的内部类

①单链表节点

```Java
static class Node<K,V> implements Entry<K,V> {
    final int hash;
    final K key;
    volatile V val;
    volatile Node<K,V> next; //下一个节点
}
```

② 红黑树代理节点

```Java
static final class TreeBin<K,V> extends Node<K,V> {
    TreeNode<K,V> root; //根节点
    volatile TreeNode<K,V> first; //链表头结点
    volatile Thread waiter; // 等待者线程（当前lockState是读锁状态）
    volatile int lockState; //锁的状态 写锁是独占状态 写的时候其他线程不能读或写 读锁是共享状态 其他线程可以读不能写 等待者状态 写线程要等其他线程读完才能写 把lockState最后两位设为2
    // values for lockState
    static final int WRITER = 1; // set while holding write lock
    static final int WAITER = 2; // set when waiting for write lock
    static final int READER = 4;
} 
```

③ 红黑树节点

```Java
static final class TreeNode<K,V> extends Node<K,V> {
    TreeNode<K,V> parent;  // red-black tree links
    TreeNode<K,V> left;
    TreeNode<K,V> right;
    TreeNode<K,V> prev;    // needed to unlink next upon deletion
    boolean red;
}
```

④ FWD节点

​	该节点的作用是标识当前节点已经被迁移了，并表明当前table处于扩容状态

```Java
static final class ForwardingNode<K,V> extends Node<K,V> {
    final Node<K,V>[] nextTable;
    ForwardingNode(Node<K,V>[] tab) {
        super(MOVED, null, null, null);
        this.nextTable = tab;
    }
}
```

##### 偏移地址

```Java
private static final sun.misc.Unsafe U; //根据偏移地址进行操作的类
private static final long SIZECTL;  //sizeCtl在ConcurrentHashMap内存的偏移地址
private static final long TRANSFERINDEX; //transferIndex在ConcurrentHashMap内存的偏移地址
private static final long BASECOUNT;  //baseCount在ConcurrentHashMap内存的偏移地址
private static final long CELLSBUSY;  //cellsBusy在ConcurrentHashMap内存的偏移地址
private static final long CELLVALUE;  //cellValue在ConcurrentHashMap内存的偏移地址
private static final long ABASE;    //数组第一个元素的地址
private static final int ASHIFT;   //偏移量 ABASE + ASHIFT能定义到具体的位置

//这段代码是用来给上面的偏移地址赋值的
static {
    try {
        U = sun.misc.Unsafe.getUnsafe();
        Class<?> k = ConcurrentHashMap.class;
        SIZECTL = U.objectFieldOffset
            (k.getDeclaredField("sizeCtl"));
        TRANSFERINDEX = U.objectFieldOffset
            (k.getDeclaredField("transferIndex"));
        BASECOUNT = U.objectFieldOffset
            (k.getDeclaredField("baseCount"));
        CELLSBUSY = U.objectFieldOffset
            (k.getDeclaredField("cellsBusy"));
        Class<?> ck = CounterCell.class;
        CELLVALUE = U.objectFieldOffset
            (ck.getDeclaredField("value"));
        Class<?> ak = Node[].class;
        ABASE = U.arrayBaseOffset(ak);// 拿到数组第一个元素的偏移地址
        int scale = U.arrayIndexScale(ak);// 表示数组中每一个单元所占用的空间大小，即scale表示Node[]数组中每一个单元所占用的空间
        if ((scale & (scale - 1)) != 0)//scale要是2的n次方 不是2的2次方就不合法
            throw new Error("data type scale not a power of two");
        //numberOfLeadingZeros(scale)统计scale从高位有多少个连续的0
        //例如numberOfLeadingZeros（4）= 29   ASHIFT = 31 - 29 = 2 
        //ASHIFT的作用是用来算某个下标元素的偏移地址 例如下标为5的元素偏移地址为 ABASE + (5 << ASHIFT) = ABASE + (5 << 2) = ABASE + (5 * scale) = ABASE + (5 * 4) =   
        ASHIFT = 31 - Integer.numberOfLeadingZeros(scale);//获取偏移地址
    } catch (Exception e) {
        throw new Error(e);
    }
}
```

##### 几个关键的通用方法和构造方法

```Java
//根据table和下标i的偏移地址得到下标i的桶位元素
static final <K,V> Node<K,V> tabAt(Node<K,V>[] tab, int i) {
    return (Node<K,V>)U.getObjectVolatile(tab, ((long)i << ASHIFT) + ABASE);// ((long)i << ASHIFT) + ABASE能得到当前Node[] 数组下标为i的节点对象的偏移地址
}

//通过CAS把table下标i的桶位元素的值更新成v 更新成功返回true 更新失败返回false c是期望值 v是更新后的值
static final <K,V> boolean casTabAt(Node<K,V>[] tab, int i, Node<K,V> c, Node<K,V> v) {
    return U.compareAndSwapObject(tab, ((long)i << ASHIFT) + ABASE, c, v);
}

//把table下标i的桶位元素的值更新成v 没有用到cas
static final <K,V> void setTabAt(Node<K,V>[] tab, int i, Node<K,V> v) {
    U.putObjectVolatile(tab, ((long)i << ASHIFT) + ABASE, v);
}   

static final int spread(int h) {
    //hashCode和hashCode的高16位进行异或运算使得hashCode变得更为分散
    return (h ^ (h >>> 16)) & HASH_BITS; //hashmap的hash方法加上与HASH_BITS进行与运算使得hash值永远是正数
}

//算出大于c的最小2次方 例如c是29 就会算出32
private static final int tableSizeFor(int c) {
    int n = c - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}    

//算出扩容标识戳 线程参与并发扩容时通过sizeCtl算出的扩容标识戳等于这个才能参与并发扩容
//例如n是16，表示table从16扩容到32
static final int resizeStamp(int n) {
    // 27 | （1 << 15） = 27 | 32768 = 0000 0000 0001 1011 | 1000 0000 0000 0000 = 1000 0000 0001 1011
    // 线程通过sizeCtl算出的扩容标识戳要等于1000 0000 0001 1011才能参与到16扩容到32的这个过程
    return Integer.numberOfLeadingZeros(n) | (1 << (RESIZE_STAMP_BITS - 1));
}
```

##### 构造函数

```Java
// 无参构造
public ConcurrentHashMap() {}
// 指定初始容量
public ConcurrentHashMap(int initialCapacity) {
    this(initialCapacity, 0.75F, 1);
}
// 指定初始容量和加载因子
public ConcurrentHashMap(int initialCapacity, float loadFactor) {
    this(initialCapacity, loadFactor, 1);
}
// 指定初始容量和加载因子以及并发线程数
public ConcurrentHashMap(int initialCapacity, float loadFactor, int concurrencyLevel) {
    if (loadFactor > 0.0F && initialCapacity >= 0 && concurrencyLevel > 0) {
        // 当指定的初始化容量initialCapacity小于并发级别concurrencyLevel时
        if (initialCapacity < concurrencyLevel) { 
            // 初始化容量值设置为并发级别的值
            initialCapacity = concurrencyLevel; 
        }
        long size = (long)(1.0D + (double)((float)((long)initialCapacity) / loadFactor));
        int cap = size >= 1073741824L ? 1073741824 : tableSizeFor((int)size);
        this.sizeCtl = cap; //sizeCtl > 0 table还没初始化，sizeCtl表示初始容量 
    } else {
        throw new IllegalArgumentException();
    }
}
```

##### 结构初始化

```Java
private final Node<K,V>[] initTable() {
    Node<K,V>[] tab; int sc;
    while ((tab = table) == null || tab.length == 0) {
        if ((sc = sizeCtl) < 0) // 此时正在扩容, 或者初始化, 需要让出线程
            Thread.yield();
        else if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
    		// 使用unsafe原子操作，假如等于sc，则将sizeCtl设置为-1，表示正在初始化
   			// compareAndSwapInt可以避免多个线程同时对表格初始化，如果有其他线程
    		// 已经重置为-1，那么当前的线程就不需要对表格进行初始化操作。
            try {
                // 二次确认当前还没被初始化
                if ((tab = table) == null || tab.length == 0) {
                    int n = (sc > 0) ? sc : DEFAULT_CAPACITY;
                    @SuppressWarnings("unchecked")
                    Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
                    table = tab = nt;
                    // 全局变量LOAD_FACTOR = 0.75f, 表示负载阈值
                    // 计算负载阈值sc = n * 0.75, 采用以下移位操作更快
                    sc = n - (n >>> 2); 
                }
            } finally {
                // 初始化之后, 更新为负载阈值, 超过会重新扩容
                sizeCtl = sc;
            }
            break;
        }
    }
    return tab;
}
```

##### 数据插入-put

```Java
public V put(K key, V value) {
	return this.putVal(key, value, false);
}

/**
 * 数据插入
 * @param key 键
 * @param value 值
 * @param onlyIfAbsent 只有缺失时才插入，否则强制更新
 */
final V putVal(K key, V value, boolean onlyIfAbsent) {
   // 不允许空值
   if (key == null || value == null) 
   		throw new NullPointerException();
   // 计算哈希值
   int hash = spread(key.hashCode());
   int binCount = 0;
   for (Node<K,V>[] tab = table;;) {
       Node<K,V> f; int n, i, fh;
       // 如果没有初始化,或者数组长度为零
       if (tab == null || (n = tab.length) == 0)
           tab = initTable();
       // 判断索引位置是否有存在元素
       else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
 			// tabAt原子操作f = tab[i], 找到表格中对应的桶, i为hash投影的索引
           if (casTabAt(tab, i, null, new Node<K,V>(hash, key, value, null)))
           		// casTabAt原子操作，主要是tab[i]= new Node(...)，如果失败则表示
           		// 其他线程已经设置，就for中可以直接获取
               break;
       }
       // 如果桶结点为ForwardingNode,则协助扩容
       else if ((fh = f.hash) == MOVED)
           // 协助扩容
           tab = helpTransfer(tab, f);
       else {
           V oldVal = null;
           // 普通的节点, 将头锁住方便更新
           synchronized (f) {
           	    // 再次检测下，如果不等说明被其他线程更新，等待一次重新操作
               if (tabAt(tab, i) == f) { 
                   if (fh >= 0) {
                   	   // 如果是正常节点, 直接往后查询
                   	   // binCount表示当前桶中节点的数量
                       binCount = 1;
                       for (Node<K,V> e = f;; ++binCount) {
                           K ek;
                           if (e.hash == hash &&
                               ((ek = e.key) == key ||
                                (ek != null && key.equals(ek)))) {
                               oldVal = e.val;
                               // 如果已经找到, 则根据onlyIfAbsent是否更新
                               if (!onlyIfAbsent)
                                   e.val = value;
                               break;
                           }
                           Node<K,V> pred = e;
                           // 假如没找到则追加到链尾
                           if ((e = e.next) == null) {
                               pred.next = new Node<K,V>(hash, key,
                                                         value, null);
                               break;
                           }
                       }
                   }
                   // 当前的桶是一个红黑二叉树的头结点
                   else if (f instanceof TreeBin) {
                       Node<K,V> p;
                       binCount = 2; //  binCount设置为2, 防止转化成二叉树
                       if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                      value)) != null) {
                           oldVal = p.val;
                           if (!onlyIfAbsent)
                               p.val = value;
                       }
                   }
               }
           }
           if (binCount != 0) {
               // 假如当前桶的节点数量大于8个，那就该桶的数据转成红黑二叉树形式
               // 注意：treeifyBin会检查元素个数是否大于MIN_TREEIFY_CAPACITY=64
               if (binCount >= TREEIFY_THRESHOLD)
                   treeifyBin(tab, i);
               if (oldVal != null)
                   return oldVal;
               break;
           }
       }
   }
   // 元素个数增加1, binCount表示当前插入数据所在桶元素序号（1，2...）, 根据条件决定是否扩容
   addCount(1L, binCount);
   return null;
}

```

##### 树节点更新

`	putTreeVal`表示查找二叉树上对应的节点，如果没找到则自动生成一个节点

```Java
final TreeNode<K,V> putTreeVal(int h, K k, V v) {
  Class<?> kc = null;
  boolean searched = false;
  for (TreeNode<K,V> p = root;;) {
      int dir, ph; K pk;
      if (p == null) {
          // 当前桶为空，则直接创建一个新的节点
          first = root = new TreeNode<K,V>(h, k, v, null, null);
          break;
      }
      else if ((ph = p.hash) > h)
          // hash值大于当前key的，在左子树
          dir = -1;
      else if (ph < h)
          // hash值小于当前key的，在右子树
          dir = 1;
      else if ((pk = p.key) == k || (pk != null && k.equals(pk)))
          // 等于，则比较key是否相等
          return p;
      else if ((kc == null &&
                (kc = comparableClassFor(k)) == null) ||
               (dir = compareComparables(kc, k, pk)) == 0) {
        // 如果hash值相等，那么就比较下是否是String或者实现了Comparable接口
        // 如果等出现二次碰撞，则左右子树都搜索
          if (!searched) {
              TreeNode<K,V> q, ch;
              searched = true;
              // 找到对应的节点
              if (((ch = p.left) != null &&
                   (q = ch.findTreeNode(h, k, kc)) != null) ||
                  ((ch = p.right) != null &&
                   (q = ch.findTreeNode(h, k, kc)) != null))
                  // 找到直接返回即可
                  return q;
          }
          // 假如没有找到，则根据类名、System.identityHashCode比较来确定到底放在左右哪边
          dir = tieBreakOrder(k, pk);
      }

      TreeNode<K,V> xp = p;
      // 根据比较的大小dir来确定插入左子树还是右子树
      if ((p = (dir <= 0) ? p.left : p.right) == null) {
          TreeNode<K,V> x, f = first;
          first = x = new TreeNode<K,V>(h, k, v, f, xp);
          if (f != null)
              f.prev = x;
          if (dir <= 0) // <=0 左子树
              xp.left = x;
          else // >0 右子树
              xp.right = x;
          if (!xp.red)
              x.red = true;
          else {
              // 加锁，调整平衡
              lockRoot();
              try {
                  root = balanceInsertion(root, x);
              } finally {
                  unlockRoot();
              }
          }
          break;
      }
  }
  assert checkInvariants(root);
  return null;
}

// 多次冲突碰撞之后，就用此来决定谁大谁小，以区分在左还是在右子树上
static int tieBreakOrder(Object a, Object b) {
  int d;
  if (a == null || b == null ||
      (d = a.getClass().getName().
       compareTo(b.getClass().getName())) == 0)
      d = (System.identityHashCode(a) <= System.identityHashCode(b) ?
           -1 : 1);
  return d;
}
```

##### 更新节点数

```Java
private final void addCount(long x, int check) {
    CounterCell[] as; long b, s;
    // counterCells和baseCount用于存储结点数
    // 这里可以看到首先对baseCount进行更新,其次是counterCells
    if ((as = counterCells) != null ||
        !U.compareAndSwapLong(this, BASECOUNT, b = baseCount, s = b + x)) {
        CounterCell a; long v; int m;
        boolean uncontended = true;
        // ThreadLocalRandom.getProbe()值对线程是固定的
        // 分析可得：第一次对baseCount添加失败, 则这里会对counterCells进行更新
        if (as == null || (m = as.length - 1) < 0 ||
            (a = as[ThreadLocalRandom.getProbe() & m]) == null ||
            !(uncontended =
              U.compareAndSwapLong(a, CELLVALUE, v = a.value, v + x))) {
            // 多线程修改baseCount时, 
            // 竞争失败的线程会执行fullAddCount(x, uncontended), 把x的值插入到counterCell类中
            fullAddCount(x, uncontended);
            return;
        }
        if (check <= 1)
            return;
        // 计算节点数
        s = sumCount();
    }
    if (check >= 0) {
        Node<K,V>[] tab, nt; int n, sc;
        // s为更新之后的结点数, 判断是否大于sizeCtl阈值
        // 并且, table不为空
        // 以及, 桶长度小于最大容许范围
        while (s >= (long)(sc = sizeCtl) && (tab = table) != null &&
               (n = tab.length) < MAXIMUM_CAPACITY) {
            int rs = resizeStamp(n);
            // 从上面可得sc = sizeCtl
            // 根据sizeCtl值知道, sizeCtl<0表示正在扩容
            if (sc < 0) {
            	// 判断扩容是否完成
                if ((sc >>> RESIZE_STAMP_SHIFT) != rs || sc == rs + 1 ||
                    sc == rs + MAX_RESIZERS || (nt = nextTable) == null ||
                    transferIndex <= 0)
                    break;
                // 更新sizeCtl值, 协助扩容
                if (U.compareAndSwapInt(this, SIZECTL, sc, sc + 1))
                    transfer(tab, nt);
            }
            // 开始扩容
            // 可以结合transfer、resizeStamp、helpTransfer查看扩容对sizeCtl的操作
            else if (U.compareAndSwapInt(this, SIZECTL, sc,
                                         (rs << RESIZE_STAMP_SHIFT) + 2))
          		// 将sizeCtrl设置负数, 然后开始转移
                transfer(tab, null);
            s = sumCount();
        }
    }
}
```

##### 修改计数

​	多线程修改baseCount时，竞争失败的线程会执行`fullAddCount(x, uncontended)`，把x的值插入到counterCell类中

```Java
private final void fullAddCount(long x, boolean wasUncontended) {
    int h;
    //获取当前线程的probe的值, 如果值为0, 则初始化当前线程的probe的值, probe就是随机数
    if ((h = ThreadLocalRandom.getProbe()) == 0) {
        ThreadLocalRandom.localInit();      // force initialization
        h = ThreadLocalRandom.getProbe();
        // 由于重新生成了probe，未冲突标志位设置为true
        wasUncontended = true;
    }
    boolean collide = false;                // True if last slot nonempty

    //自旋
    for (;;) {
        CounterCell[] as; CounterCell a; int n; long v;
        //说明counterCells已经被初始化过了, 我们先跳过这个代码, 先看初始化部分
        if ((as = counterCells) != null && (n = as.length) > 0) {
            // 通过该值与当前线程probe求与，获得cells的下标元素，和hash 表获取索引是一样的
            if ((a = as[(n - 1) & h]) == null) {
                //cellsBusy=0, 表示counterCells不在初始化或者扩容状态下
                if (cellsBusy == 0) {            // Try to attach new Cell
                    //构造一个CounterCell的值，传入元素个数
                    CounterCell r = new CounterCell(x); // Optimistic create
                    //通过cas设置cellsBusy标识，防止其他线程来对counterCells并发处理
                    if (cellsBusy == 0 &&
                        U.compareAndSwapInt(this, CELLSBUSY, 0, 1)) {
                        boolean created = false;
                        try {               // Recheck under lock
                            CounterCell[] rs; int m, j;
                            //将初始化的r对象的元素个数放在对应下标的位置
                            if ((rs = counterCells) != null &&
                                (m = rs.length) > 0 &&
                                rs[j = (m - 1) & h] == null) {
                                rs[j] = r;
                                created = true;
                            }
                        } finally {
                            //恢复标志位
                            cellsBusy = 0;
                        }
                        if (created)
                            break;
                        //说明指定cells下标位置的数据不为空，则进行下一次循环
                        continue;           // Slot is now non-empty
                    }
                }
                collide = false;
            }
            //说明在addCount方法中cas失败了，并且获取probe的值不为空
            else if (!wasUncontended)       // CAS already known to fail
                //设置为未冲突标识，进入下一次自旋
                wasUncontended = true;      // Continue after rehash
            //由于指定下标位置的cell值不为空，则直接通过cas进行原子累加，如果成功，则直接退出
            else if (U.compareAndSwapLong(a, CELLVALUE, v = a.value, v + x))
                break;
            // 如果已经有其他线程建立了新的counterCells,
            // 或者CounterCells大于CPU核心数（很巧妙，线程的并发数不会超过cpu核心数）
            else if (counterCells != as || n >= NCPU)
                //设置当前线程的循环失败不进行扩容
                collide = false;            // At max size or stale
            //恢复collide状态，标识下次循环会进行扩容
            else if (!collide)
                collide = true;
            //进入这个步骤，说明CounterCell数组容量不够，线程竞争较大，所以先设置一个标识表示为正在扩容
            else if (cellsBusy == 0 &&
                     U.compareAndSwapInt(this, CELLSBUSY, 0, 1)) {
                try {
                    if (counterCells == as) {// Expand table unless stale
                        //扩容一倍, 这个扩容比较简单
                        CounterCell[] rs = new CounterCell[n << 1];
                        for (int i = 0; i < n; ++i)
                            rs[i] = as[i];
                        counterCells = rs;
                    }
                } finally {
                    //恢复标识
                    cellsBusy = 0;
                }
                collide = false;
                //继续下一次自旋
                continue;                   // Retry with expanded table
            }
            //继续下一次自旋
            h = ThreadLocalRandom.advanceProbe(h);
        }
        //cellsBusy=0表示没有在做初始化, 
        // 通过cas更新cellsbusy的值标注当前线程正在做初始化操作
        else if (cellsBusy == 0 && counterCells == as &&
                 U.compareAndSwapInt(this, CELLSBUSY, 0, 1)) {
            boolean init = false;
            try {  // Initialize table
                if (counterCells == as) {
                    //初始化容量为2
                    CounterCell[] rs = new CounterCell[2];
                    //将x也就是元素的个数 放在指定的数组下标位置
                    rs[h & 1] = new CounterCell(x);
                    //赋值给counterCells
                    counterCells = rs;
                    //设置初始化完成标识
                    init = true;
                }
            } finally {
                //恢复标识
                cellsBusy = 0;
            }
            if (init)
                break;
        }
        else if (U.compareAndSwapLong(this, BASECOUNT, v = baseCount, v + x))
            //竞争激烈，其它线程占据cell 数组，直接累加在base变量中
            break;                          // Fall back on using base
    }
}
```

##### 桶扩容

```Java
/**
 * 桶扩容
 * @param tab 原桶
 * @param nextTab 协助扩容桶
 */
private final void transfer(Node<K,V>[] tab, Node<K,V>[] nextTab) {
        int n = tab.length, stride;
        // 将length/8, 然后除以CPU核数。如果得到的结果小于16, 那么就使用16。
        // 目的：让每个CPU处理的桶一样多, 避免出现转移任务不均匀的现象。
        // 如果桶较少的话, 默认一个 CPU（一个线程）处理 16 个桶
        if ((stride = (NCPU > 1) ? (n >>> 3) / NCPU : n) < MIN_TRANSFER_STRIDE)
            stride = MIN_TRANSFER_STRIDE; // subdivide range
        if (nextTab == null) {            // initiating
            try {
                // 扩容2倍
                @SuppressWarnings("unchecked")
                Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n << 1];
                nextTab = nt;
            } catch (Throwable ex) {      // try to cope with OOME
                sizeCtl = Integer.MAX_VALUE;
                return;
            }
            // 保存新表在成员变量中
            nextTable = nextTab;
            // 保存下一张表大小, 方便转移
            transferIndex = n;
        }
        int nextn = nextTab.length;
        // 创建一个fwd节点, 用于占位。
        // 当其他线程发现这个槽位中是fwd类型的节点, 则跳过这个节点。
        ForwardingNode<K,V> fwd = new ForwardingNode<K,V>(nextTab);
        boolean advance = true;
        // 用来标记是否完成，方便提交更改
        boolean finishing = false; // to ensure sweep before committing nextTab
        for (int i = 0, bound = 0;;) {
            Node<K,V> f; int fh;
            // 先获取锁定一下转移数据表的索引范围值bound ~ i
            while (advance) {
                int nextIndex, nextBound;
                if (--i >= bound || finishing) // 不停往前移动表索引
                    advance = false;
                else if ((nextIndex = transferIndex) <= 0) {
                    // 如果是<= 0，表明转移全部完成
                    i = -1;
                    advance = false;
                }
                else if (U.compareAndSwapInt
                         (this, TRANSFERINDEX, nextIndex,
                          nextBound = (nextIndex > stride ?
                                       nextIndex - stride : 0))) {
                	// transferIndex减去步伐stride，下个线程就从新的索引处nextBound转移数据
                    bound = nextBound;
                    i = nextIndex - 1;
                    advance = false;
                }
            }
            if (i < 0 || i >= n || i + n >= nextn) {
                int sc;
                if (finishing) {
                    // 将新表替换旧表
                    nextTable = null;
                    table = nextTab;
                    // 负载阈值：2 * 0.75 * n 
                    sizeCtl = (n << 1) - (n >>> 1);
                    return;
                }
                if (U.compareAndSwapInt(this, SIZECTL, sc = sizeCtl, sc - 1)) {
                    // 当前的线程转移完成
                    if ((sc - 2) != resizeStamp(n) << RESIZE_STAMP_SHIFT) 
                    // 判断是否还有其他的线程在转移
                        return;
                    // 转移完成
                    finishing = advance = true;
                    i = n; // recheck before commit
                }
            }
            else if ((f = tabAt(tab, i)) == null)
                // 如原来的表没有数据，则直接设置一个转移节点到旧表中
                advance = casTabAt(tab, i, null, fwd);
            else if ((fh = f.hash) == MOVED)
                // 有可能其他的线程已经处理过
                advance = true; // already processed
            else {
                // 锁定表中对应桶，然后进行数据转移，元素要不在在原来i对应的低位桶，要不就在高位(i+n)对应桶
                synchronized (f) {
                    if (tabAt(tab, i) == f) { // 二次确认，防止别其他线程抢
                        Node<K,V> ln, hn;
                        if (fh >= 0) { // hash值大于0，普通的链表
                            int runBit = fh & n;
                            Node<K,V> lastRun = f;
                            // 找到最后一个转以后不在当前索引对应桶中，而是在新扩对应(i+n)索引对应桶中
                            // 因为元素散列算法就是 (hash^(hash>>>16)&0x7FFFFFFF) & (2^x -1)，扩容一次
                            // x的值就增加1，表大小容量是2倍增(n=2^x)，所以当hash&n=hash&2^x的值不同时，
                            // 则说明该元素在新的表中会散列到高位索引(i+n)中
                            for (Node<K,V> p = f.next; p != null; p = p.next) {
                                int b = p.hash & n;
                                if (b != runBit) {
                                    runBit = b;
                                    lastRun = p;
                                }
                            }
                            if (runBit == 0) {
                           // 如果没有找到，则表明该桶中的所有元素在新表对应的位置不变
                                ln = lastRun;
                                hn = null;
                            }
                            else {
                            // 如果找到了，那么说明该桶中的元素需要重新散列
                                hn = lastRun;
                                ln = null;
                            }
                            // 重新建立新链表
                            for (Node<K,V> p = f; p != lastRun; p = p.next) {
                                int ph = p.hash; K pk = p.key; V pv = p.val;
                                if ((ph & n) == 0)
                                    ln = new Node<K,V>(ph, pk, pv, ln);
                                else
                                    hn = new Node<K,V>(ph, pk, pv, hn);
                            }
                            // 设置新表低位、高位的头，并将原来的节点设置为转移节点
                            setTabAt(nextTab, i, ln);
                            setTabAt(nextTab, i + n, hn);
                            setTabAt(tab, i, fwd);
                            advance = true;
                        }
                        else if (f instanceof TreeBin) {
                           // 二叉树
                            TreeBin<K,V> t = (TreeBin<K,V>)f;
                            TreeNode<K,V> lo = null, loTail = null;
                            TreeNode<K,V> hi = null, hiTail = null;
                            int lc = 0, hc = 0;
                            // 将二叉树的节点分成低位元素和高位元素链表
                            for (Node<K,V> e = t.first; e != null; e = e.next) {
                                int h = e.hash;
                                TreeNode<K,V> p = new TreeNode<K,V>
                                    (h, e.key, e.val, null, null);
                                if ((h & n) == 0) { // 低位元素
                                    if ((p.prev = loTail) == null)
                                        lo = p;
                                    else
                                        loTail.next = p;
                                    loTail = p;
                                    ++lc;
                                }
                                else { // 高位元素
                                    if ((p.prev = hiTail) == null)
                                        hi = p;
                                    else
                                        hiTail.next = p;
                                    hiTail = p;
                                    ++hc;
                                }
                            }
                            // 如果元素链表数量小于6，则退化成链表，否则就生产二叉树
                            ln = (lc <= UNTREEIFY_THRESHOLD) ? untreeify(lo) :
                                (hc != 0) ? new TreeBin<K,V>(lo) : t;
                            hn = (hc <= UNTREEIFY_THRESHOLD) ? untreeify(hi) :
                                (lc != 0) ? new TreeBin<K,V>(hi) : t;
                            // 设置新表的头，并且将旧表设置为转移节点
                            setTabAt(nextTab, i, ln);
                            setTabAt(nextTab, i + n, hn);
                            setTabAt(tab, i, fwd);
                            advance = true;
                        }
                    }
                }
            }
        }
    }
}
```

##### 协助扩容

​	假如对于MOVE节点(forwarding nodes)，也是说当前有其他的线程正在扩容，整体尚未完成，但是当前的节点已经完成了转移，那此时就加入到协助扩容环节

```Java
final Node<K,V>[] helpTransfer(Node<K,V>[] tab, Node<K,V> f) {
  Node<K,V>[] nextTab; int sc;
  if (tab != null && (f instanceof ForwardingNode) &&
      (nextTab = ((ForwardingNode<K,V>)f).nextTable) != null) {
      int rs = resizeStamp(tab.length);
      while (nextTab == nextTable && table == tab &&
             (sc = sizeCtl) < 0) {
          if ((sc >>> RESIZE_STAMP_SHIFT) != rs || sc == rs + 1 ||
              sc == rs + MAX_RESIZERS || transferIndex <= 0)
              break;
          // 多个线程同时进入, sizeCtrl + 1
          if (U.compareAndSwapInt(this, SIZECTL, sc, sc + 1)) {
              transfer(tab, nextTab);
              break;
          }
      }
      return nextTab;
  }
  return table;
}
```

##### 数据获取-get

​	查找元素更为简单，找到列表中对应的桶，根据不同桶的类型，调用的不同的方法找对应元素。

```Java
public V get(Object key) {
    Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
    // 计算对应的hash值
    int h = spread(key.hashCode());
    // 找到对应的数组对应的桶, 数组n的必须2的次方(n = 2^i, 其中i<=30)
    // (n - 1) & h) 表示将hash值投影在0 ~ n-1之间的索引值
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (e = tabAt(tab, (n - 1) & h)) != null) {
        if ((eh = e.hash) == h) {
            // 假如hash值相等, key也相等, 那么表示找到
            if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                return e.val;
        }
        else if (eh < 0)
            // 如果hash值小于0, 说明是特殊节点ForwardingNode, ReservationNode
            return (p = e.find(h, key)) != null ? p.val : null;
        while ((e = e.next) != null) {
            // eh>=0，为普通节点, 继续搜索链表后面的节点列表
            if (e.hash == h &&
                ((ek = e.key) == key || (ek != null && key.equals(ek))))
                return e.val;
        }
    }
    return null;
}
```

##### 大小获取

`	size` 方法的作用是为返回哈希表中实际存在的键值对的总数

```Java
public int size() {
    // 获取元素数量
    long n = sumCount();
    return ((n < 0L) ? 0 :(n > (long)Integer.MAX_VALUE) ? Integer.MAX_VALUE :(int)n);
}

final long sumCount() {
    // 统计counterCells和baseCount的数据和
    CounterCell[] as = counterCells; CounterCell a;
    long sum = baseCount;
    if (as != null) {
        for (int i = 0; i < as.length; ++i) {
            if ((a = as[i]) != null)
                sum += a.value;
        }
    }
    return sum;
}
```



#### ⑧ Properties

Properties特点：

（1）Properties是Hashtable<Object,Object>的子类；

（2）Properties类表示了一个可持久的属性集；

（3）Properties可以保存在流中或从流中加载；

（4）Properties中每个键和对应的值都是一个字符串（String类型）；

（5）Properties有一个特殊的作用：专门用来加载xxx.properties配置文件。

​	继承Hashtable，大部分方法都加了synchronized

​	底层使用private transient volatile ConcurrentHashMap<Object, Object> map;来存储数据

#### ⑨ HashTable



#### HashMap与TreeMap比较

##### HashMap的实现

​	HashMap的内部实现是由数组和链表（或红黑树）组成的。数组的每个元素都是一个链表（或红黑树），链表（或红黑树）中存储的是键值对。当发生哈希冲突时，新的键值对会被添加到链表（或红黑树）的尾部。当链表（或红黑树）中元素的个数超过了一个阈值时，链表（或红黑树）就会被转换成红黑树。这样可以保证HashMap的性能在最坏情况下也能达到O(log n)。

##### HashMap的优点

1. 查找、插入、删除的时间复杂度为O(1)；
2. 可以存储null值和null键；
3. 内存占用比较小；
4. 适合于快速查找、插入、删除元素的场景。

##### HashMap的缺点

1. 迭代HashMap的顺序是不确定的；
2. 当哈希冲突比较严重时，性能会下降；
3. 不支持按照键值对的键或值进行排序。

##### TreeMap的实现

TreeMap的内部实现是由红黑树组成的。红黑树是一种自平衡的二叉搜索树，可以保证插入、删除、查找操作的时间复杂度都是O(log n)。在插入键值对时，TreeMap会按照键进行排序，这样可以保证遍历TreeMap时的顺序是按照键的顺序输出的。

##### TreeMap的优点

1. 可以按照键值对的键进行排序；
2. 内部有序，可以保证元素的顺序；
3. 性能比HashMap更稳定，不会因为哈希冲突而导致性能下降。

##### TreeMap的缺点

1. 查找、插入、删除的时间复杂度为O(log n)，相比于HashMap稍微慢一些；
2. 不能存储null键；
3. 内存占用比较大；
4. 不支持按照键值对的值进行排序。



### 集合和数组工具类

1、Collections 工具类

```
1）sort：对列表进行排序。
（2）binarySearch：在已排序的列表中使用二分查找来查找指定的元素。
（3）reverse：反转列表中元素的顺序。
（4）shuffle：随机重排列表中的元素。
（5）min：返回集合中的最小元素。
（6）max：返回集合中的最大元素。
（7）unmodifiableList：返回一个不可修改的列表视图。
（8）synchronizedMap：返回一个线程安全的映射。
（9）ingleton：返回一个包含单个指定对象的不可修改的集合。
（10）emptyList：返回一个空的不可修改的列表。
（11）emptySet：返回一个空的不可修改的集合。
```

2、Arrays工具类

```
（1）sort：对数组进行排序。
（2）binarySearch：在已排序的数组中使用二分查找来查找指定的元素。
（3）fill：将数组中的所有元素填充为指定的值。
（4）copyOfRange：复制原始数组的指定范围部分，生成一个新的数组。
（5）equals：判断两个数组是否相等。
（6）asList：将指定的元素转换为固定大小的列表。
```



### 迭代器遍历集合

Iterator 提供了一种安全、抽象化的遍历方式，适用于多种集合实现，同时支持元素的移除操作，使代码更加健壮和清晰。主要优势如下：

（1）安全遍历集合： Iterator 提供了一种安全的方式来遍历集合，尤其在多线程环境中，可以避免遍历过程中的并发修改问题。遍历过程中如果尝试修改集合，会抛出 ConcurrentModificationException 异常，从而提醒开发者注意并发问题。

（2）支持移除元素： Iterator 提供了 remove() 方法，允许你在遍历过程中删除集合中的元素。这是使用增强 for 循环无法实现的功能，可以避免在遍历过程中删除元素时引发的问题。

（3）抽象化遍历方式： 使用 Iterator 可以将遍历逻辑与具体的集合实现分离，使你能够以一种统一的方式遍历不同类型的集合，无论是数组、列表、集或映射。

（4）避免索引操作： 使用 Iterator 可以避免使用索引进行遍历，从而使代码更加清晰，不会因索引操作而引入错误。

（5）遍历途中插入元素： 使用 Iterator 遍历集合时，可以在遍历过程中插入新元素，而不会影响当前遍历的内容。这是在使用增强 for 循环时无法做到的。



