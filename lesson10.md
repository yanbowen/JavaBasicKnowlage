
# Java基础10  
<hr>     

## 泛型概述   
* 为JDK1.5版本出现新特性，用于解决安全问题  

		ArrayList<String> al = new ArrayList<String>();  
  
* 将运行时才能发现的问题，转移到编译时期,增加安全；
* 避免強转类型  

![](https://i.imgur.com/b2fIdMC.jpg)  
  
![](https://i.imgur.com/D0nxJus.jpg)   
  
![](https://i.imgur.com/NK3f0Ix.jpg)  
  
* 静态方法不可以访问类上定义的泛型  
  
![](https://i.imgur.com/3jWZ461.jpg)  
  
* 泛型接口  

![](https://i.imgur.com/HSdWFZs.jpg)  
  
![](https://i.imgur.com/DEFy66B.jpg)  
  
![](https://i.imgur.com/K90M2i5.jpg)  
  
* 泛型限定  
	* ? extends E ： 接收E类型或E的子类型
	* super E	 :  接收E类型或者E的父类型  
	
![](https://i.imgur.com/ct1w8Sq.jpg)  

