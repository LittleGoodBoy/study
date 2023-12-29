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
```

















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

​	2、LinkedHashMap 在 HashMap 的基础上，使用链表维护插入顺序或访问顺序，能够保持插入顺序或按照访问顺序遍历键值对。

​	3、TreeMap 基于红黑树实现，存储键值对，并按照键的自然顺序（或者提供的比较器）进行排序。

​	4、WeakHashMap 与 HashMap 类似，但对于没有被其他对象引用的键，不会阻止垃圾回收。

​	5、EnumMap 专门用于存储枚举类型的键的集合，内部基于数组实现，非常高效。

​	6、IdentityHashMap 使用引用相等而不是值相等来比较键，对于对象引用的比较非常严格。

​	7、ConcurrentHashMap线程安全的 HashMap 变体，支持并发访问，分段锁机制提高了并发性能。

​	8、Properties一个特殊的 Map 实现，用于处理属性文件，键和值都必须为字符串类型。

​	9、HashTable 与 HashMap 类似，但是是线程安全的，性能较差。在多线程环境中使用，或者在性能要求不高的情况下使用。


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




