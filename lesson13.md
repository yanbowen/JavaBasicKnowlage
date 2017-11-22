# Java基础13  
<hr>     

## Arrays  

		int[] arr = {2,4,5};
		System.out.println(Arrays.toString(arr))
* 数组转集合 : 可以使用集合的方法操作数组

		List<String> li = Arrays.asList(arr);
	* 将数组变成集合不可以使用集合的增删方法（会发生不支持异常）    
	  
![](https://i.imgur.com/CgGxbDR.jpg)  
    

---
## 集合变数组
* Collection接口中的toArray方法
	* 返回包含此 collection 中所有元素的数组。
	* 假定 x 是只包含字符串的一个已知 collection。以下代码用来将 collection 转储到一个新分配的 String 数组： 

			ArrayList<String> x = new ArrayList<String>();
			x.add("avc1");
			x.add("avc2");
			x.add("avc3");
     		String[] y = x.toArray(new String[x.size()]);
			System.out.println(Arrays.toString(y));
  
* 增强for  

		for(String s : x)
		{
			System.out.println("s" + s);
		}  
  
* 对集合进行遍历
	* 只能获取元素。但是不能对集合进行操作
	* 迭代器除了遍历，还可以进行remove集合中元素的动作
	* 如果是ListIterator 还有在遍历过程中进行增删改查的操作
	* 接口 ListIterator<E> 系列表迭代器，允许程序员按任一方向遍历列表、迭代期间修改列表，并获得迭代器在列表中的当前位置。 
	 
			HashMap<Integer,String> hm = new HashMap<Integer,String>();
			hm.put(1,"a");
			hm.put(2,"b");
			hm.put(3,"c");

			Set<Integer> keySet = hm.keySet();
			for(Integer i : keySet)
			{
				System.out.println("i" + hm.get(i));
			}

			//Set<Map.Entry<Integer,String>> entrySet = hm.entrySet();
			//for(Map.Entry<Integer,String> me : entrySet)

			for(Map.Entry<Integer,String> me : hm.entrySet())
			{
				System.out.println(me.getKey() + " -- " + me.getValue);
			}  
  
* 静态导入  
  
![](https://i.imgur.com/9qGhcjN.jpg)  
   
* 类 System 
	* System 类包含一些有用的类字段和方法。它不能被实例化。   
![](https://i.imgur.com/YLlpteY.png)  
  
![](https://i.imgur.com/y3tzOxI.jpg)  
  
* 类 Runtime  
	* 每个 Java 应用程序都有一个 Runtime 类实例，使应用程序能够与其运行的环境相连接。可以通过 getRuntime 方法获取当前运行时。   
  
	* public static Runtime getRuntime() 返回与当前 Java 应用程序相关的运行时对象。Runtime 类的大多数方法是实例方法，并且必须根据当前的运行时对象对其进行调用。 
  
   
			Runtime r = Runtime.getRuntime();
			Process p = r.exec("C:\\winmine.exe");  
			p.destroy();  
  
* 类 Date   ： 表示特定的瞬间，精确到毫秒。
  
		Date d = new Date();
		//封装到SimpleDateFormat
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 E hh:mm:ss");
		String time = sdf.format(d);
  		System.out.println("time = " + time);
* 类 Calendar 
	* Calendar 类是一个抽象类，它为特定瞬间与一组诸如 YEAR、MONTH、DAY_OF_MONTH、HOUR 等 日历字段之间的转换提供了一些方法，并为操作日历字段（例如获得下星期的日期）提供了一些方法
	
			Calendar c = Calendar.getInstance();
			System.out.println(c.get(Calendar.YEAR));  
  
* 类 Math
	* Math 类包含用于执行基本数学运算的方法，如初等指数、对数、平方根和三角函数  

![](https://i.imgur.com/CrdEP4q.png)  
  
* public static double ceil(double a) 
	* 返回最小的（最接近负无穷大）double 值，该值大于等于参数，并等于某个整数。