# Java基础05  
<hr>     

## 异常   
* 问题划分
	* 对于严重问题：Java通过Error类进行描述---一般不编写针对性的代码进行处理
	* 对于非严重问题：Java通过Exception类进行描述---需要针对处理
	
 
* 多异常处理
	* 声明异常时，建议声明更为具体的异常，这样处理的可以更具体
	* 对方声明几个异常，就对应几个catch块，不要定义多余的catch块
* throw与throws
	* throws后面是异常类，可以加多个，用逗号隔开，函数上（小括号与大括号之间）
	* throw后面是异常对象，函数内
* Exception中的RunTimeException特点：若函数内抛出该异常，函数上不用声明，编译通过，
	* 函数上声明该异常，调用者不用进行处理

  
* 对于异常分为两种
	* 编译时被检测的异常
	* 编译时不被检测的异常（RuntimeException及其子类）（不需要标识） 

*  子类在覆盖父类时，如果父类的方法抛出异常，那么子类的覆盖方法，只能抛出父类的异常或者该异常的子类

<hr>     

## 包（package）  
* 对类文件进行分类管理
* 给类提供多层命名空间
* 写在程序文件的第一行
* 类名的全称是 包名.类名
* 包也是一种封装形式
  
		javac -d . PackageDemo.java //源文件包含包目录
		java pack.PackageDemo 
<hr>     

## jar包  

		C:\myclass>jar -cf haha.jar pack1 pack2  
  
		C:\>dir > c:\2.txt  
![](https://i.imgur.com/i7lHHow.jpg)  
  
