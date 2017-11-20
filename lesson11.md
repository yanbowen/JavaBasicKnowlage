# Java基础11  
<hr>     

## 集合Map概述   
* 将键映射到值的对象。一个映射不能包含重复的键；每个键最多只能映射到一个值。  
	* V get(Object key) 返回指定键所映射的值；如果此映射不包含该键的映射关系，则返回 null。 
	* V put(K key,V value) 将指定的值与此映射中的指定键关联（可选操作）。如果此映射以前包含一个该键的映射关系，则用指定值替换旧值
	* V remove(Object key) 返回此映射中以前关联该键的值，如果此映射不包含该键的映射关系，则返回 null。
	* boolean containsValue(Object value) 如果此映射将一个或多个键映射到指定值，则返回 true。
	* Set<K> keySet() 返回此映射中包含的键的 Set 视图。
	
			Map<String,String> map = new HashMap<String,String>();
			map.put("02","zhang2");
			map.put("03","zhang3");
			map.put("04","zhang4");
			map.put("05","zhang5");

			Set<String> keySet = map.keySet();
			Iterator<String> it = keySet.iterator();
			while(it.hasNext())
			{
				String key = it.next();
				String value = map.get(key);
			}
  
	* Set<Map.Entry<K,V>> entrySet() 返回此映射中包含的映射关系的 Set 视图。  
	
			//将Map集合中的映射关系取出  
			Set<Map.Entry<String,String>> entrySet = map.entrySet();
			Iterator<Map.Entry<String,String>> it = entrySet.iterator();
			while(it.hasNext())
			{
				Map.Entry<String,String> me = it.next();
				String key = me.getKey();
				String value = me.getValue();
			}
	* Map.Entry 原因在于Entry也是一个接口，它是Map接口中的一个内部接口。（嵌套类）  
	
![](https://i.imgur.com/iDAIAD0.jpg)
				
* Hashtable(JDK1.0)
	* 此类实现一个哈希表，该哈希表将键映射到相应的值。任何非 null 对象都可以用作键或值。
	* 为了成功地在哈希表中存储和获取对象，用作键的对象必须实现 hashCode 方法和 equals 方法。Hashtable 是同步的  
	  

* HashMap(JDK1.2)
	* 基于哈希表的 Map 接口的实现。此实现提供所有可选的映射操作，并允许使用 null 值和 null 键。（除了非同步和允许使用 null 之外，HashMap 类与 Hashtable 大致相同。）此类不保证映射的顺序，特别是它不保证该顺序恒久不变。 


* TreeMap(与Set很像，其实Set底层就是使用了Map)
	* 基于红黑树（Red-Black tree）的 NavigableMap 实现。该映射根据其键的自然顺序进行排序，或者根据创建映射时提供的 Comparator 进行排序，具体取决于使用的构造方法。 

![](https://i.imgur.com/cWDJqVx.jpg)   
![](https://i.imgur.com/2dTKjTc.jpg)  
