# Java基础12  
<hr>     

## 集合框架工具类Collections   

* 如果为此类的方法所提供的 collection 或类对象为 null，则这些方法都将抛出 NullPointerException。
* public static <T extends Comparable<? super T>> void sort(List<T> list) 根据元素的自然顺序 对指定列表按升序进行排序。列表中的所有元素都必须实现 Comparable 接口。此外，列表中的所有元素都必须是可相互比较的（也就是说，对于列表中的任何 e1 和 e2 元素，e1.compareTo(e2) 不得抛出 ClassCastException）。
  
![](https://i.imgur.com/ddyMbXe.jpg)  

*    

![](https://i.imgur.com/2vf2uvj.jpg)
![](https://i.imgur.com/Y76Jh8y.jpg)  
   
* Collections.max()  
![](https://i.imgur.com/9F9EqY0.jpg)
![](https://i.imgur.com/yW8RXnF.png)  
  
* binarySearch  
	* public static <T> int binarySearch(List<? extends Comparable<? super T>> list,T key) 
	* 使用二分搜索法搜索指定列表，以获得指定对象。在进行此调用之前，必须根据列表元素的自然顺序对列表进行升序排序（通过 sort(List) 方法）。如果没有对列表进行排序，则结果是不确定的。如果列表包含多个等于指定对象的元素，则无法保证找到的是哪一个。   
![](https://i.imgur.com/W2Z2siC.jpg)  
如果搜索键包含在列表中，则返回搜索键的索引；否则返回 (-(插入点) - 1)。    
  

* fill 使用指定元素替换指定列表中的所有元素。 
	* public static <T> void fill(List<? super T> list,T obj)   
* replaceAll
	* 使用另一个值替换列表中出现的所有某一指定值。更确切地讲，使用 newVal 替换 list 中满足 (oldVal==null ? e==null : oldVal.equals(e)) 的每个 e 元素。
	* public static <T> boolean replaceAll(List<T> list,T oldVal,T newVal) 

* reverse 反转指定列表中元素的顺序。
	* public static void reverse(List<?> list)

* reverseOrder 
	* 返回一个比较器，它强行逆转实现了 Comparable 接口的对象 collection 的自然顺序。（自然顺序是通过对象自身的 compareTo 方法强行排序的。）此方法允许使用单个语句，以逆自然顺序对实现了 Comparable 接口的对象 collection（或数组）进行排序（或维护）   

![](https://i.imgur.com/ihPzfeH.jpg)   
  
* 强行逆转  

![](https://i.imgur.com/9U0RWsD.jpg)  
![](https://i.imgur.com/PjQjzGK.jpg)  
![](https://i.imgur.com/uZOD5WY.png)
  
* swap 在指定列表的指定位置处交换元素。
	* public static void swap(List<?> list,int i,int j)  
  
* shuffle 使用默认随机源对指定列表进行置换。所有置换发生的可能性都是大致相等的。
	* public static void shuffle(List<?> list)