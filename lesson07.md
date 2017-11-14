# Java基础07  
<hr>     

## 多线程通信   
* 等待唤醒机制：使用在同步中对持有监视器（锁）的线程操作
	* 锁.wait();
	* 锁.notify();
	* notifyAll();
* 停止线程
	* interrupt ：中断线程；如果线程在调用 Object 类的 wait()、wait(long) 或 wait(long, int) 方法，或者该类的 join()、join(long)、join(long, int)、sleep(long) 或 sleep(long, int) 方法过程中受阻，则其中断状态将被清除，它还将收到一个 InterruptedException。 
	* interrupted ：测试当前线程是否已经中断。线程的中断状态 由该方法清除。换句话说，如果连续两次调用该方法，则第二次调用将返回 false（在第一次调用已清除了其中断状态之后，且第二次调用检验完中断状态前，当前线程再次中断的情况除外）。 如果当前线程已经中断，则返回 true；否则返回 false。
* setPriority(int new Priprity) 更改线程优先级  
![](https://i.imgur.com/0ykmMPN.jpg)  
  
## String
* 字符串是常量；它们的值在创建之后不能更改。字符串缓冲区支持可变的字符串。因为 String 对象是不可变的，所以可以共享。  

		String s1 = "abc";
		String s2 = new String("abc");
		String s3 = "abc";
		//以上两句话的区别
		//s1在内存中有一个对象
		//s2在内存中有两个对象
		//s1==s3 -> true  

![](https://i.imgur.com/5YoKQmE.jpg)  

![](https://i.imgur.com/XtkznmF.jpg)  
  
* 判断内容是否相同，重写了Object类中的equals方法  
 
		boolean equals(str);

* 判断内容是否相同，忽略大小写  
 
		boolean equalsIgnoreCase(str);
  
### 转换
* 将字符数组转换成字符串
	* 构造函数 String(char[])
	* String(char[] value, int offset, int count) 
		* 分配一个新的 String，它包含取自字符数组参数一个子数组的字符。offset 参数是子数组第一个字符的索引，count 参数指定子数组的长度。该子数组的内容已被复制；后续对字符数组的修改不会影响新创建的字符串。
	* 静态方法 
		* copyValueOf(char[] data)
		* copyValueOf(char[] data, int offset, int count) 
		* String.valueOf(3); // 3+""
	* public char[] toCharArray() 将此字符串转换为一个新的字符数组。

### 替换
* replace(char oldChar, char newChar) 返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有 oldChar 得到的。
* replace(CharSequence target, CharSequence replacement) 使用指定的字面值替换序列替换此字符串所有匹配字面值目标序列的子字符串。

### 切割
* public String[] split(String regex) 该方法的作用就像是使用给定的表达式和限制参数 0 来调用两参数 split 方法。因此，所得数组中不包括结尾空字符串。
	* 例如，字符串 "boo:and:foo" 使用这些表达式可生成以下结果：   
	
			Regex        结果  
			:       { "boo", "and", "foo" }   
			o       { "b", "", ":and:f" }   

* public String substring(int beginIndex) 返回一个新的字符串，它是此字符串的一个子字符串。该子字符串从指定索引处的字符开始，直到此字符串末尾。  

		"unhappy".substring(2) returns "happy"
 		"Harbison".substring(3) returns "bison"
 		"emptiness".substring(9) returns "" (an empty string)

* public String substring(int beginIndex,int endIndex)  //包含头，不包含尾
	* 返回一个新字符串，它是此字符串的一个子字符串。该子字符串从指定的 beginIndex 处开始，直到索引 endIndex - 1 处的字符。因此，该子字符串的长度为 endIndex-beginIndex。
		*  "hamburger".substring(4, 8) returns "urge"
		* "smiles".substring(1, 5) returns "mile"

*  toLowerCase
	*  public String toLowerCase() 
		*  使用默认语言环境的规则将此 String 中的所有字符都转换为小写
	*  public String toUpperCase(Locale locale) 
		*  使用给定 Locale 的规则将此 String 中的所有字符都转换为大写。 
		
* public String trim()
	* 返回字符串的副本，忽略前导空白和尾部空白。

* public int compareTo(String anotherString) 
	* 按字典顺序比较两个字符串。该比较基于字符串中各个字符的 Unicode 值。按字典顺序将此 String 对象表示的字符序列与参数字符串所表示的字符序列进行比较。如果按字典顺序此 String 对象位于参数字符串之前，则比较结果为一个负整数。如果按字典顺序此 String 对象位于参数字符串之后，则比较结果为一个正整数。如果这两个字符串相等，则结果为 0；compareTo 只在方法 equals(Object) 返回 true 时才返回 0。 
