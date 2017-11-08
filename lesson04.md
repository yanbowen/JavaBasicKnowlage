# Java基础04  
<hr>     

##Object   
* equals 比较地址  

##toString   
* getClass().getName() + '@' + Integer.toHexString(hashCode())

##内部类  
* 内部类访问规则
	* 内部类可以直接访问外部类成员，包括私有
	* 外部类访问内部类，必须建立内部类对象
	* 内部类可以被private修饰
	* 当内部类被static修饰后，只能直接访问外部类中的static成员。出现访问局限  
* 在外部其他类中，如何访问static内部类的非静态成员呢？
	* new Outer.Inner().function();
* 在外部其他类中，如何访问static内部类的静态成员呢？
	* Outer.Inner.function();

	`Outer.Inner in = new Outer().new Inner();
	`  
  
* 当内部类中定义了静态成员，该内部类必须是static的
* 当外部类中的静态方法访问内部类时，内部类也必须是静态的  



* 当描述事物时，事物的内部还有事物，该事物用内部类来描述，因为内部事务在使用外部事务的内容  

		class Body
		{
			private class XinZang
			{
				
			}	
			public void show()
			{
				new XinZang().XXX;
			}
		}  

*   
* 内部类定义在局部时  
	* 局部内部类不能用static修饰
	* 从内部类中访问局部变量，需要将局部变量定义为final类型
	* 内部类定义在局部时，不可以被成员修饰符修饰
	* 可以直接访问外部类中的成员，因为还持有外部类中的引用，但是不可以访问它所在的局部中的变量，只能访问被final修饰的局部变量

	
* 定义匿名内部类的前提：
	* 内部类必须是继承一个类或者实现接口

			new AbsDemo()
			{
				void show()
				{
					System.out.println("sssss");
				}	
			}.show();   

 			

