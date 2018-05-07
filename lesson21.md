# Java基础21  
<hr>     
  
## 队列（Queue）  
   
	public class Main {
    	public static void main(String[] args) {
    	    //add()和remove()方法在失败的时候会抛出异常(不推荐)
    	    Queue<String> queue = new LinkedList<String>();
    	    //添加元素
    	    queue.offer("a");
    	    queue.offer("b");
    	    queue.offer("c");
    	    queue.offer("d");
    	    queue.offer("e");
    	    for(String q : queue){
    	        System.out.println(q);
    	    }
    	    System.out.println("===");
    	    System.out.println("poll="+queue.poll()); //返回第一个元素，并在队列中删除
    	    for(String q : queue){
    	        System.out.println(q);
    	    }
    	    System.out.println("===");
    	    System.out.println("element="+queue.element()); //返回第一个元素 
    	    for(String q : queue){
    	        System.out.println(q);
    	    }
    	    System.out.println("===");
    	    System.out.println("peek="+queue.peek()); //返回第一个元素 
    	    for(String q : queue){
    	        System.out.println(q);
        	}
    	}
	}   
  
* add		：增加一个元索					如果队列已满，则抛出一个IIIegaISlabEepeplian异常
* remove	：移除并返回队列头部的元素		如果队列为空，则抛出一个NoSuchElementException异常
* element	：返回队列头部的元素				如果队列为空，则抛出一个NoSuchElementException异常
* offer		：添加一个元素并返回true			如果队列已满，则返回false
* poll		：移除并返问队列头部的元素		如果队列为空，则返回null
* peek		：返回队列头部的元素				如果队列为空，则返回null
* put		：添加一个元素					如果队列满，则阻塞
* take		：移除并返回队列头部的元素		如果队列为空，则阻塞  
  
## 链表（LinkedList）   
  
	public class Main {
    	public static void main(String[] args) {
    	    LinkedList<String> lList = new LinkedList<String>();
    	    lList.add("1");
    	    lList.add("2");
        	lList.add("3");
    	    lList.add("4");
    	    lList.add("5");
    	    System.out.println("链表的第一个元素是：" + lList.getFirst());
    	    System.out.println("链表的最后一个元素是：" + lList.getLast());

        	System.out.println(lList);
        	lList.addFirst("0");
        	System.out.println(lList);
        	lList.addLast("6");
        	System.out.println(lList);
    	}
	}    
  
  
以上代码运行输出结果为：

	链表的第一个元素是：1
	链表的最后一个元素是：5
	1, 2, 3, 4, 5
	0, 1, 2, 3, 4, 5
	0, 1, 2, 3, 4, 5, 6   
   
## Stack类为线程安全类   
  
	public class Test {  
    	public static void main(String[] args) {  
        	Stack<String> s = new Stack<String>();  
        	System.out.println("------isEmpty");  
        	System.out.println(s.isEmpty());  
        	System.out.println("------push");  
        	s.push("1");  
        	s.push("2");  
        	s.push("3");  
        	Test.it(s);  
        	System.out.println("------pop");  
        	String str = s.pop();  
        	System.out.println(str);  
        	Test.it(s);  
        	System.out.println("------peek");  
        	str = s.peek();  
        	System.out.println(str);  
        	Test.it(s);  
        	System.out.println("------search");  
        	int i = s.search("2");  
        	System.out.println(i);  
        	i = s.search("1");  
        	System.out.println(i);  
        	i = s.search("none");  
        	System.out.println(i);  
    	}  
      
    	public static void it(Stack<String> s){  
        	System.out.print("iterator:");  
        	Iterator<String> it = s.iterator();  
        	while(it.hasNext()){  
        	    System.out.print(it.next()+";");  
        	}  
        	System.out.print("\n");  
    	}  
	}      
  
------isEmpty  
true            
------push  
iterator:1;2;3;    
------pop  
3       --栈顶是数组最后一个  
iterator:1;2;  
------peek  
2       --pop取后删掉，peek只取不删  
iterator:1;2;  
------search      
1       --以1为基数，即栈顶为1  
2       --和栈顶见的距离为2-1=1  
-1      --不存在于栈中     
   
