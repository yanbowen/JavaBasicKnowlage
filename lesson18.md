# Java基础18  
<hr>     
  
## 类加载器
  
### 类的加载
当程序要使用某个类时，如果该类还未被加载到内存中，则系统会通过加载，连接，初始化三步来实现对这个类进行初始化。  

* 加载 
	* 就是指将class文件读入内存，并为之创建一个Class对象。
	* 任何类被使用时系统都会建立一个Class对象。
* 连接
	* 验证 是否有正确的内部结构，并和其他类协调一致
	* 准备 负责为类的静态成员分配内存，并设置默认初始化值
	* 解析 将类的二进制数据中的符号引用替换为直接引用

### 类初始化时机 

* 创建类的实例
* 访问类的静态变量，或者为静态变量赋值
* 调用类的静态方法
* 使用反射方式来强制创建某个类或接口对应的java.lang.Class对象
* 初始化某个类的子类
* 直接使用java.exe命令来运行某个主类

### 类加载器  
  
* 负责将.class文件加载到内存在中，并为之生成对应的Class对象。

* 类加载器的组成
	* Bootstrap ClassLoader 根类加载器
		* 也被称为引导类加载器，负责Java核心类的加载
			* 比如System,String等。在JDK中JRE的lib目录下rt.jar文件中    
  
	* Extension ClassLoader 扩展类加载器  
		* 负责JRE的扩展目录中jar包的加载。
			* 在JDK中JRE的lib目录下ext目录

	* Sysetm ClassLoader 系统类加载器
		* 负责在JVM启动时加载来自java命令的class文件，以及classpath环境变量所指定的jar包和类路径


## 反射  
* JAVA反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。
* 要想解剖一个类,必须先要获取到该类的字节码文件对象。而解剖使用的就是Class类中的方法.所以先要获取到每一个字节码文件对应的Class类型的对象.
  
		获取class文件对象的方式：
		A： Object类的getClass()方法
		B： 数据类型的静态属性class
		C： Class类中的静态方法

		一般情况下：开发使用第三种方式
	
		//方式1
		Person p = new Person();
		Class c = p.getClass();
		
		Person p2 = new Person();
		Class c2 = p2.getClass();

		System.out.println(p == p2); // false
		System.out.println(c == c2); // true

		//方式2
		Class c3 = Person.class;
		//int.class;
		//String.class;
		System.out.println(c == c3); // true
		
		//方式3
		Class c4 = Class.forName("cn.it.Person"); //写全路径 //外面写 不会报错
		System.out.println(c == c4); // true 
		

---
	  
### 通过反射获取构造方法并使用  
  
	//获取字节码文件对象
	Class c = Class.forName("cn.yanbowen.person.Person");
	//获取构造方法
	//返回一个包含某些 Constructor 对象的数组，这些对象反映此 Class 对象所表示的类的所有公共构造方法。
	Constructor[] cons = c.getConstructors();
	
	for(Constructor con : cons){
		System.out.println(con);
	}
	
	//返回 Constructor 对象的一个数组，这些对象反映此 Class 对象表示的类声明的所有构造方法。
	Constructor[] cons = c.getDeclaredConstructors();

	//获取单个构造方法
	//返回一个 Constructor 对象，它反映此 Class 对象所表示的类的指定公共构造方法。
	//public Constructor<T> getConstructor(Class<?>... parameterTypes)  //注意传参的类型
	
	Constructor con = c.getConstructor(); //通过反射得到构造函数对象
	//使用此 Constructor 对象表示的构造方法来创建该构造方法的声明类的新实例，并用指定的初始化参数初始化该实例。
	Object obj = con.newInstance();
	
	//Person p = (Person)obj;
	//p.show();


---  
### 通过反射获取该构造方法并使用

	/*
	 * Person p = new Person("韦小宝",27,"北京");
	 * System.out.println(p);
	 */
		
	//获取字节码文件对象
	Class c = Class.forName("cn.yanbowen.person.Person");

	//获取带参构造器方法对象
	Constructor con = c.getConstructor(String.class, int.class, String.class);
	
	//通过带参构造方法对象创建对象
	Object obj = con.newInstance("韦小宝",33,"北京");

	System.out.println(obj);  
  
---  
### 通过反射获取私有构造方法  


	/*
	 * private Person(String name){}
	 * Person p = new Person("韦小宝");
	 * System.out.println(p);
	 */

	// 获取字节码文件对象
	Class c = Class.forName("cn.yanbowen.person.Person");
		
	//获取私有构造器方法对象
	Constructor con = c.getDeclaredConstructor(String.class); 
		
	//将此对象的 accessible 标志设置为指示的布尔值。值为 true 则指示反射的对象在使用时应该取消 Java 语言访问检查。
	con.setAccessible(true); 
		
	//通过带参构造方法对象创建对象
	Object obj = con.newInstance("韦小宝");
		
	System.out.println(obj);   
  
---  
  
### 通过反射获取成员变量并使用  
  
	/*
	 * Person p = new Person();
	 * p.address = "北京";
	 * System.out.println(p);
	 */


	// 获取字节码文件对象
	Class c = Class.forName("cn.yanbowen.person.Person");
  
	// 通过无参构造方法创建对象
	Constructor con = c.getConstructor();
	Object obj = con.newInstance();

	// 获取单个的成员变量
	Field addressField = c.getField("address");

	//将指定对象变量上此Field 对象表示的字段设置为指定的新值
	//给obj对象的addressField字段设置为北京
	addressField.set(obj, "北京");
		
	Field nameField = c.getDeclaredField("name");
	nameField.setAccessible(true);
	nameField.set(obj, "令狐冲");
	System.out.println(obj);


### 通过反射获取成员变量并使用   
  
	/*
	 * Person p = new Person();
	 * p.show();
	 */


	// 获取字节码文件对象
	Class c = Class.forName("cn.yanbowen.person.Person");
 
	Constructor con = c.getConstructor();
	Object obj = con.newInstance();
		
	//第一个参数表示的方法名，第二个参数表示的是方法的参数的class类型
	Method m1 = c.getMethod("show");
	//第一个参数表示对象是谁，第二参数表示调用该方法的实际参数
	m1.invoke(obj);  

### 反射获取带参带返回值成员方法并使用
  
	// 获取字节码文件对象
	Class c = Class.forName("cn.yanbowen.person.Person");
 
	Constructor con = c.getConstructor();
	Object obj = con.newInstance();
		
	//第一个参数表示的方法名，第二个参数表示的是方法的参数的class类型
	Method m2 = c.getMethod("method", String.class);
	
	m2.invoke(obj,"Hello");  
  
	Method m3 = c.getMethod("getString", String.class, int.class);
	
	Object objString = m3.invoke(obj,"Hello", 100);
	System.out.println(objString);