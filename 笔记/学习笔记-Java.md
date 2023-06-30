---
title: "学习笔记Java"
date: 2020-10-12T14:07:39+08:00
draft: false
tags: ["Java"]
categories: ["学习笔记"]
---

## Java集合框架

### List接口

#### 简介

List集合代表一个有序、可重复集合，集合中每个元素都有其对应的顺序索引。List集合默认按照元素的添加顺序设置元素的索引，可以通过索引（类似数组的下标）来访问指定位置的集合元素。

实现List接口的集合主要有：ArrayList、LinkedList、Vector、Stack。

- List:元素是有序的(怎么存的就怎么取出来，顺序不会乱)，元素可以重复（角标1上有个3，角标2上也可以有个3）因为该集合体系有索引
    - ArrayList：底层的数据结构使用的是数组结构（数组长度是可变的百分之五十延长）（特点是查询很快，但增删较慢）线程不同步
    - LinkedList：底层的数据结构是链表结构（特点是查询较慢，增删较快） 线程不同步
    - Vector：底层是数组数据结构 线程同步（数组长度是可变的百分之百延长）（无论查询还是增删都很慢，被ArrayList替代了）

#### List排序通用方法

```java
Collections.sort(list, new Comparator() {
    public int compare(Student s1, Student s2) {  //先按成绩 降序 排序，如果成绩一样的话按id 升序 排序
 		if(s1.getScore()>s2.getScore()){	//greater
			return -1;
		}else if(s1.getScore()==s2.getScore()){	//equals
			if(s1.getId()>s2.getId()){
				return 1;
			}else if(s1.getId()==s2.getId()){
				return 0;
			}else{
				return -1;
			}
		}else{	//less
			return 1;
		}　
    }
});
```

#### List通用方法

```java
List list = new ArrayList();
// 向列表的尾部追加指定的元素
list.add("lwc");
// 在列表的指定位置插入指定元素
list.add(1, "nxj");
// 追加指定 collection 中的所有元素到此列表的结尾
list.addAll(new ArrayList());
// 从列表中移除所有元素
list.clear();
// 如果列表包含指定的元素,则返回true
list.contains("nxj");
// 如果列表包含指定 collection 的所有元素,则返回 true
list.containsAll(new ArrayList());
// 比较指定的对象与列表是否相等
list.equals(new ArrayList());
// 返回列表中指定位置的元素
list.get(0);
// 返回列表的哈希码值
list.hashCode();
// 返回列表中首次出现指定元素的索引,如果列表不包含此元素,则返回 -1
list.indexOf("lwc");
// 返回列表中最后出现指定元素的索引,如果列表不包含此元素,则返回 -1
list.lastIndexOf("lwc");
// 如果列表不包含元素,则返回 true
list.isEmpty();
// 移除列表中指定位置的元素
list.remove(0);
// 移除列表中出现的首个指定元素
list.remove("lwc");
// 从列表中移除指定 collection 中包含的所有元素
list.removeAll(new ArrayList());
// 用指定元素替换列表中指定位置的元素
list.set(0, "lp");
// 返回列表中的元素数
list.size();
// 返回列表中指定的fromIndex(包括)和toIndex(不包括)之间的部分视图
list.subList(1, 2);
// 返回以正确顺序包含列表中的所有元素的数组
list.toArray();
// 返回以正确顺序包含列表中所有元素的数组
list.toArray(new String[] { "a", "b" });
```

#### List通用遍历

```java
// 迭代方式
// 第一种for-size循环
for (int i = 0; i < sites.size(); i++) {
    System.out.println(sites.get(i));
}
// 第二种for-each循环
for (String i : sites) {
    System.out.println(i);
}
// 第三种Iterator循环
for(Iterator<String> it = sites.iterator(); it.hasNext();){
    String value=it.next();
}
```



#### ArrayList类

ArrayList是一个动态数组，也是我们最常用的集合，是List类的典型实现。它允许任何符合规则的元素插入甚至包括null。每一个ArrayList都有一个初始容量（10），该容量代表了数组的大小。随着容器中的元素不断增加，容器的大小也会随着增加。在每次向容器中增加元素的同时都会进行容量检查，当快溢出时，就会进行扩容操作。所以如果我们明确所插入元素的多少，最好指定一个初始容量值，避免过多的进行扩容操作而浪费时间、效率。

**示例：**

```java
import java.util.ArrayList;

public class Test {
    public static void main(String[] args) {
        ArrayList<String> sites = new ArrayList<String>();   // 创建ArrayList
        sites.add("Google");  // 添加元素
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        sites.set(2, "Wiki");  // 修改元素
        sites.remove(3);  // 删除元素，下标为3的元素，就是第四个元素
        System.out.println(sites.get(1));  // 访问元素
        System.out.println(sites.size());  // 获取大小

        // 将 lambda 表达式传递给 forEach
        numbers.forEach((e) -> {
            e = e * 10;
            System.out.print(e + " ");
        });
    }
}
```

#### LinkedList类

链表（Linked list）是一种常见的基础数据结构，是一种线性表，但是并不会按线性的顺序存储数据，而是在每一个节点里存到下一个节点的地址。链表可分为单向链表和双向链表。Java LinkedList（链表） 类似于 ArrayList，是一种常用的数据容器。与 ArrayList 相比，LinkedList 的增加和删除的操作效率更高，而查找和修改的操作效率较低。

**以下情况使用 ArrayList :**

- 频繁访问列表中的某一个元素。
- 只需要在列表末尾进行添加和删除元素操作。

**以下情况使用 LinkedList :**

- 你需要通过循环迭代来访问列表中的某些元素。
- 需要频繁的在列表开头、中间、末尾等位置进行添加和删除元素操作。

LinkedList 继承了 AbstractSequentialList 类。

LinkedList 实现了 Queue 接口，可作为队列使用。

LinkedList 实现了 List 接口，可进行列表的相关操作。

LinkedList 实现了 Deque 接口，可作为队列使用。

LinkedList 实现了 Cloneable 接口，可实现克隆。

LinkedList 实现了 java.io.Serializable 接口，即可支持序列化，能通过序列化去传输。

**示例：**

```java
import java.util.LinkedList;

public class Test {
    public static void main(String[] args) {
        LinkedList<String> sites = new LinkedList<String>();   //创建LinkedList
        sites.add("Google");   //添加元素
        sites.add("Runoob");
        sites.add("Taobao");
        sites.addFirst("Wiki");  //头部添加元素
        sites.addLast("Weibo");  //尾部添加元素
        sites.removeFirst();  //移除头部元素
        sites.removeLast();  //移除尾部元素
        System.out.println(sites.getFirst());  //获取头部元素
        System.out.println(sites.getLast());  //获取尾部元素
    }
}
```

### Set接口

#### 简介

Set体系集合可以知道某物是否已近存在于集合中,不会存储重复的元素。加入Set的每个元素必须是唯一的，否则，Set是不会把它加进去的。要想加进Set，Object必须定义equals()，这样才能标明对象的唯一性。Set的接口和Collection的一摸一样。Set的接口不保证它会用哪种顺序来存储元素。

- Set
  - HashSet : 为快速查找设计的Set。存入HashSet的对象必须定义hashCode()。
  - TreeSet : 保存次序的Set, 底层为树结构。使用它可以从Set中提取有序的序列。
  - LinkedHashSet : 具有HashSet的查询速度，且内部使用链表维护元素的顺序(插入的次序)。于是在使用迭代器遍历Set时，结果会按元素插入的次序显示。

#### Set排序

```java
//把HashSet保存在ArrayList里，再用Collections.sort()方法比较
final HashSet<Integer> va = new HashSet<Integer>();
va.add(2007111315);
va.add(2007111314);
va.add(2007111318);
va.add(2007111313);
final List<Integer> list = new ArrayList<Integer>();
for(final Integer value : va){
    list.add(value);
}
Collections.sort(list);
System.out.println(list);

//把这个HashSet做为构造参数放到TreeSet中就可以排序了
final TreeSet ts = new TreeSet(va);
ts.comparator();
System.out.println(ts);
```

#### Set遍历

```java
//Iterator迭代遍历
Set<String> set = new HashSet<String>();
Iterator<String> it = set.iterator();
while (it.hasNext()) {
	String str = it.next();
	System.out.println(str);
}

//for-each循环遍历
for (String str : set) {
	System.out.println(str);
}
```

#### Set转List

```java
//通过ArrayList进行转换
List<String> list1 = new ArrayList<String>(set);

//List实现类进行转换
List<String> list2 = new ArrayList<String> ();
list2.addAll(set);
```



#### HashSet类

HashSet使用的是相当复杂的方式来存储元素的，使用HashSet能够最快的获取集合中的元素，效率非常高（以空间换时间）。它会根据hashcode和equals来判断是否是同一个对象，如果hashcode一样，并且equals返回true，则是同一个对象，不能重复存放。

|--HashSet 底层是由HashMap实现的，通过对象的hashCode方法与equals方法来保证插入元素的唯一性，无序(存储顺序和取出顺序不一致)，。

​	|--**LinkedHashSet** 底层数据结构由哈希表和链表组成。哈希表保证元素的唯一性，链表保证元素有序。(存储和取出是一致)

#### LinkedHashSet类

LinkedHashSet是HashSet的一个子类，具有HashSet的特性，也是根据元素的hashCode值来决定元素的存储位置。但它使用链表维护元素的次序，元素的顺序与添加顺序一致。由于LinkedHashSet需要维护元素的插入顺序，因此性能略低于HashSet，但在迭代访问Set里的全部元素时由很好的性能。

(1)HashSet是Set接口的实现。HashSet按Hash算法来存储集合中的元素，具有很好的存取和查找性能。
(2)HashSet不是同步的，多个线程访问是需要通过代码保证同步
(3)HashSet集合元素值可以使null。
(4)HashSet不能保证元素的排列顺序，顺序可能与添加顺序不同，顺序也有可能发生变化。
(5)当向HashSet集合中存入一个元素时，HashSet会调用该对象的hashCode()方法来得到该对象的hashCode值，然后根据该HashCode值决定该对象在HashSet中的存储位置。如果有两个元素通过equals()方法比较返回true，但它们的hashCode()方法返回值不相等，HashSet将会把它们存储在不同的位置，依然可以添加成功。即，HashSet集合判断两个元素相等的标准是两个对象通过equals()方法比较相等，并且两个对象的hashCode()方法返回值也相等。

#### TreeSet类

TreeSet时SortedSet接口的实现类，TreeSet可以保证元素处于排序状态，它采用红黑树的数据结构来存储集合元素。TreeSet支持两种排序方法：自然排序和定制排序，默认采用自然排序。

**自然排序**

　　TreeSet会调用集合元素的compareTo(Object obj)方法来比较元素的大小关系，然后将元素按照升序排列，这就是自然排序。如果试图将一个对象添加到TreeSet集合中，则该对象必须实现Comparable接口，否则会抛出异常。当一个对象调用方法与另一个对象比较时，例如obj1.compareTo(obj2)，如果该方法返回0，则两个对象相等；如果返回一个正数，则obj1大于obj2；如果返回一个负数，则obj1小于obj2。

　　Java常用类中已经实现了Comparable接口的类有以下几个：

　　♦ BigDecimal、BigDecimal以及所有数值型对应的包装类：按照它们对应的数值大小进行比较。

　　♦ Charchter：按照字符的unicode值进行比较。

　　♦ Boolean：true对应的包装类实例大于false对应的包装类实例。

　　♦ String：按照字符串中的字符的unicode值进行比较。

　　♦ Date、Time：后面的时间、日期比前面的时间、日期大。

　　对于TreeSet集合而言，它判断两个对象是否相等的标准是：两个对象通过compareTo(Object obj)方法比较是否返回0，如果返回0则相等。

**定制排序**

　　想要实现定制排序，需要在创建TreeSet集合对象时，提供一个Comparator对象与该TreeSet集合关联，由Comparator对象负责集合元素的排序逻辑。

　　综上：自然排序实现的是Comparable接口，定制排序实现的是Comparator接口。



### Map接口

#### 简介

Map 提供了一个更通用的元素存储方法。Map 集合类用于存储元素对（称作“键”和“值”），其中每个键映射到一个值。从概念上而言，您可以将 List 看作是具有数值键的 Map。

- Map 是映射接口，Map中存储的内容是键值对(key-value)。
  - TreeMap 继承于AbstractMap，且实现了NavigableMap接口；因此，TreeMap中的内容是“有序的键值对”
  - HashMap 继承于AbstractMap，但没实现NavigableMap接口；因此，HashMap的内容是“键值对，但不保证次序”
  - Hashtable 虽然不是继承于AbstractMap，但它继承于Dictionary(Dictionary也是键值对的接口)，而且也实现Map接口；因此，Hashtable的内容也是“键值对，也不保证次序”。但和HashMap相比，Hashtable是线程安全的，而且它支持通过Enumeration去遍历。

#### Map通用方法

```java
abstract void                 clear()
abstract boolean              containsKey(Object key)
abstract boolean              containsValue(Object value)
abstract Set<Entry<K, V>>     entrySet()
abstract boolean              equals(Object object)
abstract V                    get(Object key)
abstract int                  hashCode()
abstract boolean              isEmpty()
abstract Set<K>               keySet()
abstract V                    put(K key, V value)
abstract void                 putAll(Map<? extends K, ? extends V> map)
abstract V                    remove(Object key)
abstract int                  size()
abstract Collection<V>        values()
```

1. Map提供接口分别用于返回 键集、值集或键-值映射关系集。entrySet()用于返回键-值集的Set集合;keySet()用于返回键集的Set集合;values()用户返回值集的Collection集合,因为Map中不能包含重复的键；每个键最多只能映射到一个值。所以，键-值集、键集都是Set，值集时Collection。
2. Map提供了“键-值对”、“根据键获取值”、“删除键”、“获取容量大小”等方法。

#### Map遍历

```java
//用for循环遍历
for(Map.Entry<String, String> entry:map.entrySet()){
	System.out.println(entry.getKey()+"--->"+entry.getValue());
}

//用Iterator迭代遍历
Set set = map.entrySet();
Iterator i = set.iterator();
while(i.hasNext()){
    Map.Entry<String, String> entry1=(Map.Entry<String, String>)i.next();
    System.out.println(entry1.getKey()+"=="+entry1.getValue());
}

//用keySet()迭代遍历
Iterator it=map.keySet().iterator();
while(it.hasNext()){
    String key;
    String value;
    key=it.next().toString();
    value=map.get(key);
    System.out.println(key+"--"+value);
}
```

#### HashMap类

HashMap是我们使用非常多的Collection，它是基于哈希表的 Map 接口的实现，以key-value的形式存在。
HashMap实现提供所有可选的映射操作，并允许使用 null 值和 null 键。（除了不同步和允许使用 null 之外，HashMap 类与 Hashtable 大致相同。）此类不保证映射的顺序，特别是它不保证该顺序恒久不变。
HashMap不是线程安全的，如果想要线程安全的HashMap，可以通过Collections类的静态方法synchronizedMap获得线程安全的HashMap,例如：Map map = Collections.synchronizedMap(new HashMap());

1. 归纳起来简单地说，HashMap 在底层将 key-value 当成一个整体进行处理，这个整体就是一个 Entry 对象。HashMap 底层采用一个 Entry[] 数组来保存所有的 key-value 对，当需要存储一个 Entry 对象时，会根据 Hash 算法来决定其存储位置；当需要取出一个 Entry 时，也会根据 Hash 算法找到其存储位置，直接取出该 Entry。由此可见：HashMap 之所以能快速存、取它所包含的 Entry，完全类似于现实生活中母亲从小教我们的：不同的东西要放在不同的位置，需要时才能快速找到它。
2. 当创建 HashMap 时，有一个默认的负载因子（load factor），其默认值为 0.75，这是时间和空间成本上一种折衷：增大负载因子可以减少 Hash 表（就是那个 Entry 数组）所占用的内存空间，但会增加查询数据的时间开销，而查询是最频繁的的操作（HashMap 的 get() 与 put() 方法都要用到查询）；减小负载因子会提高数据查询的性能，但会增加 Hash 表所占用的内存空间。

#### LinkedHashMap实现类

LinkedHashMap使用双向链表来维护key-value对的次序（其实只需要考虑key的次序即可），该链表负责维护Map的迭代顺序，与插入顺序一致，因此性能比HashMap低，但在迭代访问Map里的全部元素时有较好的性能。

#### HashMap按照key进行排序

HashMap本身就是升序排列,如果要获取集合数据，见如下代码：

```java
Map<String, Integer> map = new HashMap<String, Integer>();
map.put("d", 3);
map.put("c", 1);
Set keySet = map.keySet();
Collections.sort(keySet);
for(Iterator ite = keySet.iterator(); ite.hasNext();) {
	String temp = ite.next();
  	System.out.println("key-value: "+temp+","+map.getValue(temp);
}
```

但是如果想要通过key进行降序排列，则需要重写sort方法，见如下代码：

```java
Collections.sort(keySet, new Comparator() {
	public int compare(Object o1, Object o2) {  //如果map里面是其他类型直接更改sort里面的比较方法。
        if (Integer.parseInt(o1.toString()) > Integer.parseInt(o2.toString()) return 1;
        if (Integer.parseInt(o1.toString()) == Integer.parseInt(o2.toString()) return 0;
        else return - 1;
    }
});
```

#### HashMap按照value值排序的方法

HashMap的value值没有排序功能，若要进行较轻松的排序，可改写Comparator接口方法compare进行排序，代码如下：

```java
Map<String, Integer> map = new HashMap<String, Integer>();
map.put("d", 2);
map.put("c", 1);
map.put("b", 1);
map.put("a", 3);
List<Map.Entry<String, Integer>> infoIds = new ArrayList<Map.Entry<String, Integer>>(map.entrySet());
//排序前
for (int i = 0; i < infoIds.size(); i++) {
    String id = infoIds.get(i).toString();
    System.out.println(id);
}
//根据value排序
Collections.sort(infoIds, new Comparator<Map.Entry<String, Integer>>() {
    public int compare(Map.Entry<String, Integer> o1, Map.Entry<String, Integer> o2) {
        return (o2.getValue() - o1.getValue());
        //return (o1.getKey()).toString().compareTo(o2.getKey());
    }
});
//排序后
for (int i = 0; i < infoIds.size(); i++) {
    String id = infoIds.get(i).toString();
    System.out.println(id);
}
```

#### TreeMap类

TreeMap继承了NavigableMap，而NavigableMap继承自SortedMap，为SortedMap添加了搜索选项，NavigableMap有几种方法，分别是不同的比较要求：floorKey是小于等于，ceilingKey是大于等于，lowerKey是小于，higherKey是大于。TreeMap的本质是R-B Tree(红黑树)，它包含几个重要的成员变量： root, size, comparator。

1. TreeMap 是一个有序的key-value集合，它是通过红黑树实现的。
2. TreeMap 继承于AbstractMap，所以它是一个Map，即一个key-value集合。
3. TreeMap 实现了NavigableMap接口，意味着它支持一系列的导航方法。比如返回有序的key集合。
4. TreeMap 实现了Cloneable接口，意味着它能被克隆。
5. TreeMap 实现了java.io.Serializable接口，意味着它支持序列化。
6. TreeMap基于红黑树（Red-Black tree）实现。该映射根据其键的自然顺序进行排序，或者根据创建映射时提供的 Comparator 进行排序，具体取决于使用的构造方法。
7. TreeMap的基本操作 containsKey、get、put 和 remove 的时间复杂度是 log(n) 。
8. TreeMap是非同步的。 它的iterator 方法返回的迭代器是fail-fastl的。

#### HashTable类

hashTable将key和value结合起来构成键值对通过put(key,value)方法保存起来，然后通过get(key)方法获取相对应的value值。它是线程安全的哈希映射表，内部采用Entry[]数组，每个Entry均可作为链表的头，用来解决冲突（碰撞）。同时Hashtables提供了一个很有用的方法可以使应用程序的性能达到最佳。

1. 线程安全。

2. Key、Value均不能为null。

3. 包含了一个Entry[]数组，而Entry又是一个链表，用来处理冲突。

4. 每个Key对应了Entry数组中固定的位置（记为index），称为槽位（Slot）。槽位计算公式为： (key.hashCode() & 0x7FFFFFFF) % Entry[].length() 。

5. 当Entry[]的实际元素数量（Count）超过了分配容量（Capacity）的75%时，新建一个Entry[]是原先的2倍，并重新Hash（rehash）。

6. rehash的核心思路是，将旧Entry[]数组的元素重新计算槽位，散列到新Entry[]中。

#### HashTable与HashMap的区别

HashTable和HashMap存在很多的相同点，但是他们还是有几个比较重要的不同点。
第一：我们从他们的定义就可以看出他们的不同，HashTable基于Dictionary类，而HashMap是基于AbstractMap。Dictionary是什么？它是任何可将键映射到相应值的类的抽象父类，而AbstractMap是基于Map接口的骨干实现，它以最大限度地减少实现此接口所需的工作。
第二：HashMap可以允许存在一个为null的key和任意个为null的value，但是HashTable中的key和value都不允许为null。当HashMap遇到为null的key时，它会调用putForNullKey方法来进行处理。对于value没有进行任何处理，只要是对象都可以。
而当HashTable遇到null时，他会直接抛出NullPointerException异常信息。
第三：Hashtable的方法是同步的，而HashMap的方法不是。所以有人一般都建议如果是涉及到多线程同步时采用HashTable，没有涉及就采用HashMap，但是在Collections类中存在一个静态方法：synchronizedMap()，该方法创建了一个线程安全的Map对象，并把它作为一个封装的对象来返回，所以通过Collections类的synchronizedMap方法是可以我们你同步访问潜在的HashMap。

### Queue接口

Queue，java中模拟队列的一种数据结构，先进先出（FIFO）,不支持随机访问数据，通过offer（）方法增加数据到队列尾部，poll（）获取队列头部元素，可以将Queue看成一个通道，最先走进的通道的也是最先走出通道的，最后走进去的，在通道里面呆的时间最久。

常用方法：
**add** 增加一个元索如果队列已满，则抛出一个IIIegaISlabEepeplian异常
**remove** 移除并返回队列头部的元素，如果队列为空，则抛出一个NoSuchElementException异常
**element** 返回队列头部的元素，如果队列为空，则抛出一个NoSuchElementException异常
**offer** 添加一个元素并返回true，如果队列已满，则返回false
**poll** 移除并返问队列头部的元素，如果队列为空，则返回null
**peek** 返回队列头部的元素，如果队列为空，则返回null
**put** 添加一个元素，如果队列满，则阻塞
**take** 移除并返回队列头部的元素，如果队列为空，则阻塞
**drainTo(list)** 一次性取出队列所有元素

#### PriorityQueue类

优先队列PriorityQueue是Queue接口的实现，可以对其中元素进行排序，可以放基本数据类型的包装类（如：Integer，Long等）或自定义的类对于基本数据类型的包装器类，优先队列中元素默认排列顺序是升序排列，但对于自己定义的类来说，需要自己定义比较器。

**常用方法：**

peek()//返回队首元素
poll()//返回队首元素，队首元素出队列
add()//添加元素
size()//返回队列元素个数
isEmpty()//判断队列是否为空，为空返回true,不空返回false

**示例：**

```java
public static void main(String[] args) {
    //不用比较器，默认升序排列
    Queue<Integer> q = new PriorityQueue<>();
    q.add(3);
    q.add(2);
    q.add(4);
    while(!q.isEmpty())
    {
        System.out.print(q.poll()+" "); //2 3 4
    }
    //使用自定义比较器，降序排列
    Queue<Integer> qq = new PriorityQueue<>(new Comparator<Integer>() {
        public int compare(Integer e1, Integer e2) {
            return e2 - e1;  //升序:e1-e2,降序:e2-e1
        }
    });
    qq.add(3);
    qq.add(2);
    qq.add(4);
    while(!qq.isEmpty())
    {
        System.out.print(qq.poll()+" "); //4 3 2
    }
}
```

## Java多线程

Java 给多线程编程提供了内置的支持。 一条线程指的是进程中一个单一顺序的控制流，一个进程中可以并发多个线程，每条线程并行执行不同的任务。

多线程是多任务的一种特别的形式，但多线程使用了更小的资源开销。

这里定义和线程相关的另一个术语 - 进程：一个进程包括由操作系统分配的内存空间，包含一个或多个线程。一个线程不能独立的存在，它必须是进程的一部分。一个进程一直运行，直到所有的非守护线程都结束运行后才能结束。

多线程能满足程序员编写高效率的程序来达到充分利用 CPU 的目的。

### 一个线程的生命周期

线程是一个动态执行的过程，它也有一个从产生到死亡的过程。

下图显示了一个线程完整的生命周期。

![线程的生命周期](https://www.runoob.com/wp-content/uploads/2014/01/java-thread.jpg)

- 新建状态：

  使用 **new** 关键字和 **Thread** 类或其子类建立一个线程对象后，该线程对象就处于新建状态。它保持这个状态直到程序 **start()** 这个线程。

- 就绪状态：

  当线程对象调用了start()方法之后，该线程就进入就绪状态。就绪状态的线程处于就绪队列中，要等待JVM里线程调度器的调度。

- 运行状态：

  如果就绪状态的线程获取 CPU 资源，就可以执行 **run()**，此时线程便处于运行状态。处于运行状态的线程最为复杂，它可以变为阻塞状态、就绪状态和死亡状态。

- 阻塞状态：

  如果一个线程执行了sleep（睡眠）、suspend（挂起）等方法，失去所占用资源之后，该线程就从运行状态进入阻塞状态。在睡眠时间已到或获得设备资源后可以重新进入就绪状态。可以分为三种：

  等待阻塞：运行状态中的线程执行 wait() 方法，使线程进入到等待阻塞状态。

  同步阻塞：线程在获取 synchronized 同步锁失败(因为同步锁被其他线程占用)。

  其他阻塞：通过调用线程的 sleep() 或 join() 发出了 I/O 请求时，线程就会进入到阻塞状态。当sleep() 状态超时，join() 等待线程终止或超时，或者 I/O 处理完毕，线程重新转入就绪状态。

- 死亡状态：

  一个运行状态的线程完成任务或者其他终止条件发生时，该线程就切换到终止状态。

### 线程的优先级

每一个 Java 线程都有一个优先级，这样有助于操作系统确定线程的调度顺序。

Java 线程的优先级是一个整数，其取值范围是 1 （Thread.MIN_PRIORITY ） - 10 （Thread.MAX_PRIORITY ）。

默认情况下，每一个线程都会分配一个优先级 NORM_PRIORITY（5）。

具有较高优先级的线程对程序更重要，并且应该在低优先级的线程之前分配处理器资源。但是，线程优先级不能保证线程执行的顺序，而且非常依赖于平台。

### Thread 方法

| 序号  | 方法描述                                                                                                                                        |
| :---: | :---------------------------------------------------------------------------------------------------------------------------------------------- |
|   1   | **public void start()**<br/>使该线程开始执行；**==Java==** 虚拟机调用该线程的 run 方法。                                                        |
|   2   | **public void run()**<br/>如果该线程是使用独立的 Runnable 运行对象构造的，则调用该 Runnable 对象的 run 方法；否则，该方法不执行任何操作并返回。 |
|   3   | **public final void setName(String name)**<br/>改变线程名称，使之与参数 name 相同。                                                             |
|   4   | **public final void setPriority(int priority)**<br/> 更改线程的优先级。                                                                         |
|   5   | **public final void setDaemon(boolean on)**<br/>将该线程标记为守护线程或用户线程。                                                              |
|   6   | **public final void join(long millisec)**<br/>等待该线程终止的时间最长为 millis 毫秒。                                                          |
|   7   | **public void interrupt()**<br/>中断线程。                                                                                                      |
|   8   | **public final boolean isAlive()**<br/>测试线程是否处于活动状态。                                                                               |

测试线程是否处于活动状态。 上述方法是被Thread对象调用的。下面的方法是Thread类的静态方法。

| 序号  | 方法描述                                                                                                                                                 |
| :---: | :------------------------------------------------------------------------------------------------------------------------------------------------------- |
|   1   | **public static void yield()**<br/>暂停当前正在执行的线程对象，并执行其他线程。                                                                          |
|   2   | **public static void sleep(long millisec)**<br/>在指定的毫秒数内让当前正在执行的线程休眠（暂停执行），此操作受到系统计时器和调度程序精度和准确性的影响。 |
|   3   | **public static boolean holdsLock(Object x)**<br/>当且仅当当前线程在指定的对象上保持监视器锁时，才返回 true。                                            |
|   4   | **public static Thread currentThread()**<br/>返回对当前正在执行的线程对象的引用。                                                                        |
|   5   | **public static void dumpStack()**<br/>将当前线程的堆栈跟踪打印至标准错误流。                                                                            |



### 创建一个线程

Java 提供了三种创建线程的方法：

- 通过实现 Runnable 接口；
- 通过继承 Thread 类本身；
- 通过 Callable 和 Future 创建线程。

#### 通过实现Runnable 接口来创建线程

创建一个线程，最简单的方法是创建一个实现 Runnable 接口的类。

为了实现 Runnable，一个类只需要执行一个方法调用 run()，声明如下：

`public void run()`

你可以重写该方法，重要的是理解的 run() 可以调用其他方法，使用其他类，并声明变量，就像主线程一样。

在创建一个实现 Runnable 接口的类之后，你可以在类中实例化一个线程对象。

Thread 定义了几个构造方法，下面的这个是我们经常使用的：

`Thread(Runnable threadOb,String threadName);`

这里，threadOb 是一个实现 Runnable 接口的类的实例，并且 threadName 指定新线程的名字。

新线程创建之后，你调用它的 start() 方法它才会运行。

`void start();`

下面是一个创建线程并开始让它执行的实例：

```java
class RunnableDemo implements Runnable {
   private Thread t;
   private String threadName;

   RunnableDemo( String name) {
      threadName = name;
      System.out.println("Creating " +  threadName );
   }

   public void run() {
      System.out.println("Running " +  threadName );
      try {
         for(int i = 4; i > 0; i--) {
            System.out.println("Thread: " + threadName + ", " + i);
            // 让线程睡眠一会
            Thread.sleep(50);
         }
      }catch (InterruptedException e) {
         System.out.println("Thread " +  threadName + " interrupted.");
      }
      System.out.println("Thread " +  threadName + " exiting.");
   }

   public void start () {
      System.out.println("Starting " +  threadName );
      if (t == null) {
         t = new Thread (this, threadName);
         t.start ();
      }
   }
}

public class TestThread {

   public static void main(String args[]) {
      RunnableDemo R1 = new RunnableDemo( "Thread-1");
      R1.start();

      RunnableDemo R2 = new RunnableDemo( "Thread-2");
      R2.start();
   }
}
```

#### 通过继承Thread来创建线程

创建一个线程的第二种方法是创建一个新的类，该类继承 Thread 类，然后创建一个该类的实例。

继承类必须重写 run() 方法，该方法是新线程的入口点。它也必须调用 start() 方法才能执行。

该方法尽管被列为一种多线程实现方式，但是本质上也是实现了 Runnable 接口的一个实例。

```java
class ThreadDemo extends Thread {
   private Thread t;
   private String threadName;

   ThreadDemo( String name) {
      threadName = name;
      System.out.println("Creating " +  threadName );
   }

   public void run() {
      System.out.println("Running " +  threadName );
      try {
         for(int i = 4; i > 0; i--) {
            System.out.println("Thread: " + threadName + ", " + i);
            // 让线程睡眠一会
            Thread.sleep(50);
         }
      }catch (InterruptedException e) {
         System.out.println("Thread " +  threadName + " interrupted.");
      }
      System.out.println("Thread " +  threadName + " exiting.");
   }

   public void start () {
      System.out.println("Starting " +  threadName );
      if (t == null) {
         t = new Thread (this, threadName);
         t.start ();
      }
   }
}

public class TestThread {

   public static void main(String args[]) {
      ThreadDemo T1 = new ThreadDemo( "Thread-1");
      T1.start();

      ThreadDemo T2 = new ThreadDemo( "Thread-2");
      T2.start();
   }
}
```

#### 通过Callable和Future创建线程

- 1. 创建 Callable 接口的实现类，并实现 call() 方法，该 call() 方法将作为线程执行体，并且有返回值。
- 2. 创建 Callable 实现类的实例，使用 FutureTask 类来包装 Callable 对象，该 FutureTask 对象封装了该 Callable 对象的 call() 方法的返回值。
- 3. 使用 FutureTask 对象作为 Thread 对象的 target 创建并启动新线程。
- 4. 调用 FutureTask 对象的 get() 方法来获得子线程执行结束后的返回值。

```java
public class CallableThreadTest implements Callable<Integer> {
    public static void main(String[] args)
    {
        CallableThreadTest ctt = new CallableThreadTest();
        FutureTask<Integer> ft = new FutureTask<>(ctt);
        for(int i = 0;i < 100;i++)
        {
            System.out.println(Thread.currentThread().getName()+" 的循环变量i的值"+i);
            if(i==20)
            {
                new Thread(ft,"有返回值的线程").start();
            }
        }
        try
        {
            System.out.println("子线程的返回值："+ft.get());
        } catch (InterruptedException e)
        {
            e.printStackTrace();
        } catch (ExecutionException e)
        {
            e.printStackTrace();
        }

    }
    @Override
    public Integer call() throws Exception
    {
        int i = 0;
        for(;i<100;i++)
        {
            System.out.println(Thread.currentThread().getName()+" "+i);
        }
        return i;
    }
}
```

#### 创建线程的三种方式的对比

1. 采用实现 Runnable、Callable 接口的方式创建多线程时，线程类只是实现了 Runnable 接口或 Callable 接口，还可以继承其他类。
2. 使用继承 Thread 类的方式创建多线程时，编写简单，如果需要访问当前线程，则无需使用 Thread.currentThread() 方法，直接使用 this 即可获得当前线程。

### 线程的几个主要概念

在多线程编程时，你需要了解以下几个概念：

- 线程同步
- 线程间通信
- 线程死锁
- 线程控制：挂起、停止和恢复

### 多线程的使用

有效利用多线程的关键是理解程序是并发执行而不是串行执行的。例如：程序中有两个子系统需要并发执行，这时候就需要利用多线程编程。

通过对多线程的使用，可以编写出非常高效的程序。不过请注意，如果你创建太多的线程，程序执行的效率实际上是降低了，而不是提升了。

请记住，上下文的切换开销也很重要，如果你创建了太多的线程，CPU 花费在上下文的切换的时间将多于执行程序的时间！



> 本文参考至，如需更多详情，请查阅以下网站
> [菜鸟教程](https://www.runoob.com/java/java-collections.html)
> [Java School](http://www.51gjie.com/java/639.html)
