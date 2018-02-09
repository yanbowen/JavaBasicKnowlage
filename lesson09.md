# Java基础09  
<hr>     

## 集合类   
* 为了方便存储对象就出现了集合

![](https://i.imgur.com/53M6HYy.jpg) 
  
* List ： 元素有序，元素可以重复。因为该集合体系有索引。
	* E set(int index,E element)
		* 用指定元素替换列表中指定位置的元素（可选操作）
	* void add(int index,E element)
		* 在列表的指定位置插入指定元素（可选操作）。将当前处于该位置的元素（如果有的话）和所有后续元素向右移动（在其索引中加 1）。 
	* E remove(int index)
		* 移除列表中指定位置的元素（可选操作）。将所有的后续元素向左移动（将其索引减 1）。返回从列表中移除的元素。 
	* E get(int index)
		* 返回列表中指定位置的元素

* ArrayList ： 注意，此实现不是同步的；查询快，增删慢
	* al1.retainAll(al2) //取交集
	* Iterator // 获取迭代器
		* Iterator it = al1.iterator();  
		
				while(it.hasNext())
				{
					sout(it.next());
				}  
  

* LinkedList : 底层使用的链表数据结构；增删速度快，查询慢   

和ArrayList一样，LinkedList也是是未同步的，多线程并发读写时需要外部同步  
和ArrayList一样，LinkedList也对存储的元素无限制，允许null元素。  

	* public void addFirst(E e) 将指定元素插入此列表的开头。
	* public void addLast(E e) 将指定元素添加到此列表的结尾。
	* public E getFirst() 返回此列表的第一个元素。
	* public E getLast() 返回此列表的最后一个元素。
	* public E removeFirst() 移除并返回此列表的第一个元素。
	* public E removeLast() 移除并返回此列表的最后一个元素。

	* JDK1.6版本后才出现
	* public boolean offerFirst(E e) 在此列表的开头插入指定的元素。
	* public boolean offerLast(E e) 在此列表末尾插入指定的元素。
	* public E peekFirst() 获取但不移除此列表的第一个元素；如果此列表为空，则返回 null。
	* public E peekLast() 获取但不移除此列表的最后一个元素；如果此列表为空，则返回 null。 
	* public E pollFirst() 获取并移除此列表的第一个元素；如果此列表为空，则返回 null。 
	* public E pollLast() 获取并移除此列表的最后一个元素；如果此列表为空，则返回 null。   


* Vector : 底层是数组数据结构；线程同步
  
* set ： 元素无序，元素不可重复,此实现不是同步的,允许null元素  
	* HashSet ： 此类实现 Set 接口，由哈希表（实际上是一个 HashMap 实例）支持。它不保证 set 的迭代顺序；特别是它不保证该顺序恒久不变。此类允许使用 null 元素。
		* HashSet通过元素的两个方法，hashCode和equals来完成，若HashCode值相同，才会判断equals是否为true
		* 对于判断元素是否存在，以及删除等操作，依赖的方法是元素的hashCode和equals方法   
		
		![](https://i.imgur.com/fwkLBOy.jpg)  

	* TreeSet ： 可以对Set集合中的元素进行排序，排序时，当主要条件相同时，一定要判断下次要条件。底层数据结构是二叉树,TreeSet是SortedSet接口的唯一实现类，TreeSet可以确保集合元素处于排序状态。TreeSet支持两种排序方式，自然排序 和定制排序，其中自然排序为默认的排序方式。向TreeSet中加入的应该是同一个类的对象。
		* 保证元素唯一的依据是：compareTo方法返回return 0
		* 排序第一种方式：让元素自身具备比较性，需要Comparable接口，覆盖compareTo方法
		
		![](https://i.imgur.com/ClRJaSk.jpg)  

		* TreeSet集合第   
		* 二种排序方式 ： 当元素自身不具备比较性时，这时需要让容器自身具备比较性，定义比较器，将比较器对象作为参数传递给TreeSet集合的构造器  
		* 定义一个类，实现Comparator接口，覆盖compare方法。
		![](https://i.imgur.com/k3KFXaM.jpg)  
		![](https://i.imgur.com/f7HTIvm.jpg)  
  
* LinkedHashSet集合同样是根据元素的hashCode值来决定元素的存储位置，但是它同时使用链表维护元素的次序。这样使得元素看起 来像是以插入顺序保存的，也就是说，当遍历该集合时候，LinkedHashSet将会以元素的添加顺序访问集合的元素。
