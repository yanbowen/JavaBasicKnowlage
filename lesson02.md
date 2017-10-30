# Java基础02  
<hr>  

## 选择排序  

	public static viod selectSort(int[] arr){
	
		for(int m=0; m<arr.length-1; m++){
			for(int n=m+1; n<arr.length;n++){
				if(arr[m]>arr[n]){
					int temp = arr[m];
					arr[m] = arr[n];
					arr[n] = temp;
				}
			}
		}
	}   
  
## 冒泡排序  

	public static viod bubbleSort(int[] arr){
	
		for(int m=0; m<arr.length-1; m++){
			for(int n=0; n<arr.length-m-1;n++){
				if(arr[n]>arr[n+1]){
					int temp = arr[n];
					arr[n] = arr[n+1];
					arr[n+1 ] = temp;
				}
			}
		}
	}   
  
排序函数
   
	Arrays.sort(arr);//从小到大排序

最快的排序方式：希尔排序  
  
## 数组查找  
折半查找：前提是该数组为有序数组  
 
	package althorgrim;  
	/** 
	 * 1、必须采用顺序存储结果 
	 * 2、关键字必须有序 
	 */  
	public class BinarySearch {  
      
    public static int binarySearch(int a[],int goal){  
        int high=a.length-1;  
        int low=0;  
        while (low<=high) {  
            int middle=(low+high)>>2;  
            if (a[middle]==goal) {  
                return middle;  
            }  
            else if (a[middle]>goal) {  
                high=middle-1;  
            }  
            else {  
                low=middle+1;  
            }  
        }  
        return -1;  
    }  
  
## 面向过程  
  
* 面向对象是相对于面向过程而言  
  
<hr>  
  
## 面向对象  
  
* 类 ：对现实生活中事物的描述。  
* 对象 ：这类事物，实实在在存在的个体。  (在堆内存中，new产生的实体)
* 成员变量 ： 存在于堆内存中  
* 局部变量 ： 存在于栈内存中  

<hr> 
  
## 匿名对象  

* 匿名对象是对象的简化形式；
* 使用情况
	* 当对象方法仅进行一次调用时；
	* 匿名对象可以作为实际参数进行传递；  

<hr>   
  
## 封装(Encapsulation)  
* 封装是指隐藏对象的属性和实现细节，仅对外提供公共访问方式
* 好处： 
	* 将变化隔离
	* 便于使用
	* 提高重用性
	* 安全性
* 封装原则 ：
	* 将不需要对外提供的内容都隐藏起来
	* 把属性都隐藏，提供公共方法对其访问  
  
私有仅仅是封装的一种表现形式。  
<hr>   
  
## 构造函数  
* 函数名与类名相同  
* 不用定义返回值类型  
* 不写return语句  
* 当类中自定义构造函数后，系统默认定义的构造函数就没有了  
* 构造函数在对象一建立就运行
  
## 构造代码块  
* 作用： 给对象进行初始化
* 对象一建立就运行，且优先于构造函数执行
* 构造代码块是给所有对象进行统一初始化
* 构造函数是定义不同初始化对象时使用

this使用：本类内部用到本类对象时使用  
构造函数间调用只能使用this，且只能定义在构造函数的第一行，因为初始化需要先执行  

<hr>   
  
## static  
* 用于修饰成员（成员变量和成员函数）
* 被修饰后的成员具备以下特点
	* 随着类的加载而加载
	* 优先于对象存在
	* 被所有对象共享
	* 可以直接被类名调用
* 注意：
	* 静态方法只能访问静态成员（方法和变量）
	* 静态方法中不可以写this，super关键字
	* 主函数是静态的   

实例变量和类变量的区别：  

* 存放位置：
	* 类变量随着类的加载而存在于方法区中
	* 实例变量随着对象的建立而存在于堆内存中
* 生命周期：
	* 类变量生命周期最长，随着类的消失而消失
	* 实例变量生命周期随着对象的消失而消失  
   
弊端： 
	* 生命过长
	* 访问出现局限性  
  
方法设置为静态后，可以方便使用，但该类还是可以被其他程序建立对象，为了更加严谨，强制让该类不能建立对象，通过将构造函数私有化完成。  

