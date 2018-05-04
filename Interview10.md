# 面试题目整理 10
<hr>     
  
## Android实战：清理内存
  
### 查看内存总量 
   
	// import android.annotation.TargetApi;
	// import android.app.ActivityManager;
    	@TargetApi(Build.VERSION_CODES.JELLY_BEAN)
    	private String getCurrentMeminfo() {
    	    StringBuffer sb = new StringBuffer();
    	    ActivityManager.MemoryInfo mi = new ActivityManager.MemoryInfo();
    	    ActivityManager activityManager = (ActivityManager) getSystemService(Context.ACTIVITY_SERVICE);
    	    activityManager.getMemoryInfo(mi);
    	    sb.append("剩余内存："+(mi.availMem/1024/1024)+"MB\n");//返回的是字节 B
    	    if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN) {
    	        sb.append("总内存： " + (mi.totalMem/1024/1024) + "MB\n");
    	    }
    	    sb.append("内存是否过低：" + mi.lowMemory);
    	    return sb.toString();
    	}  
   
在SDK API 15以及之前并不存在ActivityManager.MemoryInfo并没有totalMem属性，而创建项目时候选择的minimum sdk的API为15：
   
获取内存使用情况的另外一个方法是解析文件/proc/meminfo，总内存、剩余内存等都可以得到，而且该方法没有版本限制。
   
    
### 清理内存
首先要在AndroidManifest.xml中添加权限：   

	<uses-permission android:name="android.permission.KILL_BACKGROUND_PROCESSES" />    
    
内存清理代码如下    
   
	// import android.app.ActivityManager;
    	/**
    	 * 清理内存
    	 */
    	private void clearMem() {
    	    ActivityManager activityManger = (ActivityManager) this.getSystemService(ACTIVITY_SERVICE);
    	    List<ActivityManager.RunningAppProcessInfo> appList = activityManger.getRunningAppProcesses();
    	    if (appList != null) {
    	        for (int i = 0; i < appList.size(); i++) {
    	            ActivityManager.RunningAppProcessInfo appInfo = appList.get(i);
    	            String[] pkgList = appInfo.pkgList;
    	            if (appInfo.importance > ActivityManager.RunningAppProcessInfo.IMPORTANCE_VISIBLE) {
    	                for (int j = 0; j < pkgList.length; j++) {
    	                    activityManger.killBackgroundProcesses(pkgList[j]);
    	                }
    	            }
    	        }
    	    }
    	}

ActivityManager.RunningAppProcessInfo.IMPORTANCE_VISIBLE这个级别是可以修改的，值不同，内存清理效果不同    
    
### 改进   
在MainActivity.java中引入代码：   
   
	@Override
    protected void onRestoreInstanceState(Bundle savedInstanceState) {
        super.onRestoreInstanceState(savedInstanceState);
        String infoTvContent = savedInstanceState.getString("infotv");
        infoTv.setText(infoTvContent);
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        outState.putString("infotv", infoTv.getText().toString());
    }

这样在旋转屏幕后重建activity后可以恢复infoTv之前的内容。     
    
## volatile关键字   
   
Java 语言中的 volatile 变量可以被看作是一种 “程度较轻的 synchronized”；与 synchronized 块相比，volatile 变量所需的编码较少，并且运行时开销也较少，但是它所能实现的功能也仅是 synchronized 的一部分。

我们知道，在Java中设置变量值的操作，除了long和double类型的变量外都是原子操作，也就是说，对于变量值的简单读写操作没有必要进行同步。

这在JVM 1.2之前，Java的内存模型实现总是从主存读取变量，是不需要进行特别的注意的。而随着JVM的成熟和优化，现在在多线程环境下volatile关键字的使用变得非常重要。

在当前的Java内存模型下，线程可以把变量保存在本地内存（比如机器的寄存器）中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成数据的不一致。    
    
要解决这个问题，只需要像在本程序中的这样，把该变量声明为volatile（不稳定的）即可，这就指示JVM，这个变量是不稳定的，每次使用它都到主存中进行读取。一般说来，多任务环境下各任务间共享的标志都应该加volatile修饰。

Volatile修饰的成员变量在每次被线程访问时，都强迫从共享内存中重读该成员变量的值。而且，当成员变量发生变化时，强迫线程将变化值回写到共享内存。这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。

Java语言规范中指出：为了获得最佳速度，允许线程保存共享成员变量的私有拷贝，而且只当线程进入或者离开同步代码块时才与共享成员变量的原始值对比。

这样当多个线程同时与某个对象交互时，就必须要注意到要让线程及时的得到共享成员变量的变化。

而volatile关键字就是提示VM：对于这个成员变量不能保存它的私有拷贝，而应直接与共享成员变量交互。

使用建议：在两个或者更多的线程访问的成员变量上使用volatile。当要访问的变量已在synchronized代码块中，或者为常量时，不必使用。

由于使用屏蔽掉了VM中必要的代码优化，所以在效率上比较低，因此一定在必要时才使用此关键字。    
       
## Android OkHttp      
  
okhttp是一个第三方类库，用于android中请求网络。

这是一个开源项目,是安卓端最火热的轻量级框架,由移动支付Square公司贡献(该公司还贡献了Picasso和LeakCanary) 。用于替代HttpUrlConnection和Apache HttpClient(android API23 里已移除HttpClient)。   
   
Okhttp集成有多种，一种就是直接下载它的jar包但是并不推荐，一种是Maven方式去构建，这种在java程序里用的会比较多    
   
	<dependency>
		<groupId>com.squareup.okhttp3</groupId>
		<artifactId>okhttp</artifactId>
		<version>3.6.0</version>
	</dependency>  
   
在android程序里，我们一般用Gradle，把这句话加入build.gradle。   
   
compile 'com.squareup.okhttp3:okhttp:3.6.0'    
    
