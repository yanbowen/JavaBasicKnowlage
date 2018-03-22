# 面试题目整理 
<hr>   

1. 什么是Activity?  
四大组件之一,一般的,一个用户交互界面对应一个activity, activity 是Context的子类,同时实现了window.callback和keyevent.callback, 可以处理与窗体用户交互的事件. 开发常用的的有FragmentActivitiy，ListActivity  , PreferenceActivity ,TabAcitivty等。如果界面有共同的特点或者功能的时候,还会自己定义一个BaseActivity.   
  
2. 请描述一下Activity 生命周期？  
Activity从创建到销毁有多种状态，从一种状态到另一种状态时会激发相应的回调方法，这些回调方法包括：oncreate ondestroy onstop onstart onresume onpause ，其实这些方法都是两两对应的，onCreate创建与onDestroy销毁；
onStart可见与onStop不可见；onResume可编辑（即焦点）与onPause；这6个方法是相对应的，那么就只剩下一个onRestart方法了，这个方法在什么时候调用呢？  
答案就是：在Activity被onStop后，但是没有被onDestroy，在再次启动此Activity时就调用onRestart（而不再调用onCreate）方法；如果被onDestroy了，则是调用onCreate方法。  
最后讲自己项目中的经验,比如说手机卫士每次进入某个界面的时候都要看到最新的数据,这个刷新列表的操作 就放在onStart()的方法里面.这样保证每次用户看到的数据都是最新的.  
多媒体播放, 播放来电话. onStop() 视频, 视频声音设置为0 , 记录视频播放的位置 mediaplayer.pause();
onStart() 根据保存的状态恢复现场. mediaplayer.start();   
在读文档的时候 还发现 activity还有两个方法 onPostResume() 和 OnPostCreate()这两个生命周期的方法,不过开发的时候没有用到过.  
  
3. 两个Activity之间跳转时必然会执行的是哪几个方法。  
一般情况比如说有两个activity,分别叫A,B ,当在A里面激活B组件的时候, A 会调用 onPause()方法,然后B 调用onCreate() ,onStart(), OnResume() ,这个时候B覆盖了窗体, A会调用onStop()方法.  如果B呢 是个透明的,或者是对话框的样式, 就不会调用onStop()方法  
  
4. 横竖屏切换时候Activity的生命周期。  
这个生命周期跟清单文件里的配置有关系  
1、不设置Activity的android:configChanges时，切屏会重新调用各个生命周期
默认首先销毁当前activity,然后重新加载  
2、设置Activity的android:configChanges="orientation|keyboardHidden|screenSize"时，切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法，游戏开发中, 屏幕的朝向都是写死的.   
  
5. 如何将一个Activity设置成窗口的样式。  
可以自定义一个activity的样式  

		android:theme="@android:style/Theme.Dialog"  
  
6. 你后台的Activity被系统 回收怎么办？如果后台的Activity由于某原因被系统回收可了，如何在被系统回收之前保存当前状态？  
除了在栈顶的activity,其他的activity都有可能在内存不足的时候被系统回收,一个activity越处于栈底,被回收的可能性越大.  

	protected void onSaveInstanceState(Bundle outState) {  
	super.onSaveInstanceState(outState);  
	outState.putLong("id", 1234567890);  
	}  
	public void onCreate(Bundle savedInstanceState) {  
	//判断 savedInstanceState是不是空.  
	//如果不为空就取出来  
	super.onCreate(savedInstanceState);  
	}  
  
7. 如何退出Activity？如何安全退出已调用多个Activity的Application？  
退出activity 直接调用 finish () 方法 .   
用户点击back键 就是退出一个activity   
退出activity 会执行 onDestroy()方法 .  
1、抛异常强制退出：  
该方法通过抛异常，使程序Force Close。   
验证可以，但是，需要解决的问题是，如何使程序结束掉，而不弹出Force Close的窗口。  
	100/0  
	//安全结束进程 	android.os.Process.killProcess(android.os.Process.myPid());  
2、记录打开的Activity：  
每打开一个Activity，就记录下来。在需要退出时，关闭每一个Activity即可。

		List<Activity> lists ; 在application 全集的环境里面 
		lists = new ArrayList<Activity>();
		lists.add(this);

		for(Activity activity: lists)
		{
			activity.finish();
		}
		ondestory
		lists.remove(this);

	3、发送特定广播：  
在需要结束应用时，发送一个特定的广播，每个Activity收到广播后，关闭即可。  
//给某个activity 注册接受接受广播的意图 	
	registerReceiver(receiver, filter)  
//如果过接受到的是 关闭activity的广播  就调用finish()方法 把当前的activity finish()掉 

	4、递归退出  
在打开新的Activity时使用startActivityForResult，然后自己加标志，在onActivityResult中处理，递归关闭。  
上面是网上的一些做法.  
其实 可以通过 intent的flag 来实现.. intent.setFlag(FLAG_ACTIVITY_CLEAR_TOP)激活一个新的activity,然后在新的activity的oncreate方法里面 finish掉.  
  
8. service是否在main thread中执行, service里面是否能执行耗时的操作?  
默认情况,如果没有显示的指定service所运行的进程, Service和activity是运行在当前app所在进程的main thread(UI主线程)里面  
service里面不能执行耗时的操作(网络请求,拷贝数据库,大文件 )  
在子线程中执行 new Thread(){}.start();  
Thread.currentThread().getName();  
特殊情况 ,可以在清单文件配置 service 执行所在的进程 ,让service在另外的进程中执行   
  
  
9. 两个Activity之间怎么传递数据？
从Activity1带参数跳转到Activity2这个经常用到，比较熟悉，但如何从Activity2将数据返回给Activity1呢？这个其实也不麻烦，动手敲一下就知道了。   
Activity1代码：  
    

		public class MainActivity extends ActionBarActivity {   
    		private Button button;  
  
    		@Override  
    		protected void onCreate(Bundle savedInstanceState) {  
        		super.onCreate(savedInstanceState);  
        		setContentView(R.layout.activity_main);  
  
        		button = (Button) findViewById(R.id.main_btn);  
  
        		button.setOnClickListener(new View.OnClickListener() {  
        		    @Override  
        		    public void onClick(View v) {  
        		        Intent intent = new Intent(MainActivity.this,SecondActivity.class);  
        		        intent.putExtra("data","data");  
        		        startActivityForResult(intent, 99);  
        		    }  
        		});  
    		}  
  
    		@Override  
    		protected void onActivityResult(int requestCode, int resultCode, Intent data) {  
        		super.onActivityResult(requestCode, resultCode, data);  
        		if(requestCode == 99){  
        		    if(resultCode == 88){  
        		        Bundle bundle = data.getExtras();  
        		        String back = bundle.getString("back");  
        		        Toast.makeText(MainActivity.this,back,Toast.LENGTH_SHORT).show();  
        		    }  
        		}  
    		}  
		}    
  
在MainActivity中通过intent.putExtra("data","data");将数据传递给第二个Activity,若不需要返回值，直接调用startActivity().  
这次我们是需要返回值，所以调用startActivityForResult(intent, 99);第二个参数为requestcode。在下面的onActivityResult(int requestCode, int resultCode, Intent data) 方法中，首先判断requestcode，再判断resultCode,resultCode是在第二个Activity中，我们调用setResult()方法中的一个参数，两个必须一样。

下面是第二个Activity的代码：  
  
	public class SecondActivity extends Activity {  
  
    	private static final String TAG = "zh";  
    	private Button btn2;  
    	@Override  
    	protected void onCreate(Bundle savedInstanceState) {  
    	    super.onCreate(savedInstanceState);  
    	    setContentView(R.layout.item);  
  	
    	    String str = getIntent().getStringExtra("data");  
    	    Log.i(TAG,str);  
    	    btn2 = (Button) findViewById(R.id.second_btn);  
  	
    	    btn2.setOnClickListener(new View.OnClickListener() {  
    	        @Override  
    	        public void onClick(View v) {  
    	            Intent intent = new Intent();  
    	            intent.putExtra("back","我是返回的数据");  
    	            setResult(88,intent);  
    	            finish();  
    	        }  
    	    });  
  
    	}    
  
当点击第二个Activity上的按钮后，就会跳转到第一个Activity，同时弹出一个吐司。    

  
10.怎么让在启动一个Activity是就启动一个service？   
在activity的onCreate()方法里面 startService();    
  
11.同一个程序，但不同的Activity是否可以放在不同的Task任务栈中？  
Singleinstance  运行在另外的单独的任务栈里面  
比方说在激活一个新的activity时候, 给intent设置flag  
Intent的flag添加FLAG_ACTIVITY_NEW_TASK  
这个被激活的activity就会在新的task栈里面…    
  
	Intent intent = new Intent(A.this,B.class);
	intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
	startActivity(intent);  
  
12.Activity怎么和service绑定，怎么在activity中启动自己对应的service？  
startService() 一旦被创建，调用着无关，没法使用service里面的方法   
bindService () 把service 与调用者绑定，如果调用者被销毁，service会销毁  
bindService() 我们可以使用service 里面的方法  
bindService().  让activity能够访问到 service里面的方法  
构建一个intent对象,  
Intent service = new Intent(this,MyService.class);  
通过bindService的方法去启动一个服务,  
  
   	bindService(intent, new MyConn(), BIND_AUTO_CREATE);  
	ServiceConnection 对象(重写onServiceConnected和OnServiceDisconnected方法) 和BIND_AUTO_CREATE.
	private class myconn implements ServiceConnection

	{

		public void onServiceConnected(ComponentName name, IBinder service) {
			// TODO Auto-generated method stub
			//可以通过IBinder的对象 去使用service里面的方法
		}

		public void onServiceDisconnected(ComponentName name) {
			// TODO Auto-generated method stub
			
		}
		
	}  
  
  
MainActivity.java:  
  
	public class MainActivity extends ActionBarActivity {

    	private String MyTag = "MyTag";
    	private Boolean bindTag = false;//设置一个标记，用于标记绑定与否；
	
    	private ImageView imgObj;
    	private Button btnObj,btn02Obj,btn03Obj;

    	//这两者，用于绑定Service后，接收从Service传回来的对象，并能通过该对象获取需要的值；
    	private MyBinder myBinder;
    	private MyServiceConnection myServiceConnection;

    	@Override
    	protected void onCreate(Bundle savedInstanceState) {
    	    super.onCreate(savedInstanceState);
    	    setContentView(R.layout.activity_main);
    	    myServiceConnection = new MyServiceConnection();
	
    	    imgObj = (ImageView)findViewById(R.id.imgid);
    	    btnObj = (Button)findViewById(R.id.btnid);
    	    btn02Obj = (Button)findViewById(R.id.btn02id);
    	    btn03Obj = (Button)findViewById(R.id.btn03id);

    	    btnObj.setOnClickListener(new View.OnClickListener() {
    	        @Override
    	        public void onClick(View v) {
	
    	            imgObj.setImageResource(myBinder.getRes());
    	        }
    	    });

    	    btn02Obj.setOnClickListener(new View.OnClickListener() {
    	        @Override
    	        public void onClick(View v) {
    	            Intent intent = new Intent(MainActivity.this, MyService.class);
    	            Log.i(MyTag,"intent");
	
    	            //绑定Service；
    	            bindService(intent,myServiceConnection, Service.BIND_AUTO_CREATE);
    	            Log.i(MyTag,"bindService");

    	            //设置标记，true代表绑定；
    	            bindTag = true;
    	        }
    	    });
	
    	    btn03Obj.setOnClickListener(new View.OnClickListener() {
    	        @Override
    	        public void onClick(View v) {
    	            //解绑定；
    	            unbindService(myServiceConnection);
    	            Log.i(MyTag,"unbindService");
	
    	            //设置标记，false代表解绑定；
    	            bindTag = false;
    	        }
    	    });

    	}

    	public class MyServiceConnection implements ServiceConnection{

        	/**
        	 * 当Service绑定时候触发onBind()后，放回值会被该方法接收即 实现 IBinder接口类型的类的对象；
        	 * Binder实现了IBinder接口；
        	 * 因此创建MyBind继承Binder,并添加所需的方法等，达到需求；
        	 */
        	@Override
        	public void onServiceConnected(ComponentName name, IBinder service) {
        	    //接收从Service传回的对象，强制转换类型
        	    myBinder = (MyBinder)service;
        	    Log.i(MyTag,"mainactivity myBinder.getRes()"+myBinder.getRes());
        	}

        	@Override
        	public void onServiceDisconnected(ComponentName name) {
	
        	}
    	}

	    @Override
	    protected void onDestroy() {
	        super.onDestroy();
	
	        /**
	         * 由于程序可能在没有解绑定的情况下被退出，而会报错；
	         * 所以我们要做个判断：当退出程序时，时候已经解绑定；
	         * 如果bindTag为true则解绑定，为false则不用；
	         */
	        if (bindTag) {
	            unbindService(myServiceConnection);
	        }
	    }
	}  
  
MyBinder.java:  
  
	public class MyBinder extends Binder {
    	private int res;

    	public int getRes() {
    	    return res;
    	}

    	public void setRes(int res) {
    	    this.res = res;
    	}
	}  
  
 
MyService.java:  
  
	public class MyService extends Service {

    	private String MyTag = "MyTag";

    	private Handler handler;

    	private Boolean myBoolean = false;

    	//在写了这个数组后，其实我是后悔的，因为我其实这个数组完全可以写在Activity那边，
    	// 然后Service只需要发送索引值过去就好；
    	private int[] draws = new int[]{R.drawable.item8,R.drawable.item9,R.drawable.item10};

    	@Override
    	public void onCreate() {
    	    super.onCreate();
    	    Log.i(MyTag,"onCreate");

    	    //java的线程是无法停止的，所以想要让其处理事务或者停止处理，
    	    // 可以通过标志来设定，一般用true和false来标志；
    	    myBoolean = true;

    	    //Service和Activity一样也不能做耗时处理，写个子线程处理，
    	    // 并发送消息给主线程，主线程再将值赋给MyBinder对象，
    	    //而MyBinder对象的属性的值又能被Activity获取到；
    	    new Thread(new Runnable() {
    	        @Override
    	        public void run() {
    	            int index = 0;
    	            Log.i(MyTag,"Thread");
	
    	            while(myBoolean){
	
    	                Message msg =Message.obtain();
	
    	                msg.arg1 = draws[index];
    	                Log.i(MyTag,"arg1 = "+msg.arg1);
	
    	                handler.sendMessage(msg);
    	                Log.i(MyTag,"draws[index]="+draws[index]);
	
    	                index = ++index%3;//++i与i++的问题：++i自加再赋值；i++赋值后再自加……
    	                Log.i(MyTag,"draws[index]="+draws[index]);
	
    	                SystemClock.sleep(300);
    	            }
    	        }
    	    }).start();
    	}

    	@Override
    	public int onStartCommand(Intent intent, int flags, int startId) {
        	Log.i(MyTag,"onStartCommand");

        	return super.onStartCommand(intent, flags, startId);
    	}

    	@Override
    	public void onDestroy() {
        	super.onDestroy();
        	Log.i(MyTag,"onDestroy");

        	myBoolean = false;
    	}

    	@Override
    	public boolean onUnbind(Intent intent) {
        	Log.i(MyTag,"onUnbind");
        	return super.onUnbind(intent);

    	}

    	@Override
    	public IBinder onBind(Intent intent) {

        	final MyBinder myBinder = new MyBinder();

        	handler = new Handler(){

        	    //获取消息，并赋值；
        	    @Override
        	    public void handleMessage(Message msg) {
        	        super.handleMessage(msg);
        	        int res = msg.arg1;
        	        myBinder.setRes(res);
        	    }
        	};

        	return myBinder;
    	}
	}  
  
  
13.什么是Service以及描述下它的生命周期。Service有哪些启动方法，有什么区别，怎样停用Service？  
在Service的生命周期中，被回调的方法比Activity少一些，只有onCreate, onStart, onDestroy,onBind和onUnbind。  
通常有两种方式启动一个Service,他们对Service生命周期的影响是不一样的。  
1 通过startService  

    Service会经历 onCreate 到onStart，然后处于运行状态，stopService的时候调用onDestroy方法。
	如果是调用者自己直接退出而没有调用stopService的话，Service会一直在后台运行。  
  
onCreate()--->onStartCommand()（onStart()方法已过时） ---> onDestory()  

2 通过bindService   

    Service会运行onCreate，然后是调用onBind， 这个时候调用者和Service绑定在一起。调用者退出了，Srevice就会调用onUnbind->onDestroyed方法。    
   

onCreate() --->onBind()--->onunbind()--->onDestory()  


所谓绑定在一起就共存亡了。调用者也可以通过调用unbindService方法来停止服务，这时候Srevice就会调用onUnbind->onDestroyed方法。
需要注意的是如果这几个方法交织在一起的话，会出现什么情况呢？  
一个原则是Service的onCreate的方法只会被调用一次，就是你无论多少次的startService又bindService，Service只被创建一次。
如果先是bind了，那么start的时候就直接运行Service的onStart方法，如果先是start，那么bind的时候就直接运行onBind方法。

如果service运行期间调用了bindService，这时候再调用stopService的话，service是不会调用onDestroy方法的，service就stop不掉了，只能调用UnbindService, service就会被销毁

如果一个service通过startService 被start之后，多次调用startService 的话，service会多次调用onStart方法。多次调用stopService的话，service只会调用一次onDestroyed方法。


如果一个service通过bindService被start之后，多次调用bindService的话，service只会调用一次onBind方法。

多次调用unbindService的话会抛出异常。  
  
14.什么是IntentService？有何优点？  
它本质是一种特殊的Service,继承自Service并且本身就是一个抽象类  
它可以用于在后台执行耗时的异步任务，当任务完成后会自动停止  
它拥有较高的优先级，不易被系统杀死（继承自Service的缘故），因此比较适合执行一些高优先级的异步任务  
它内部通过HandlerThread和Handler实现异步操作  
创建IntentService时，只需实现onHandleIntent和构造方法，onHandleIntent为异步方法，可以执行耗时操作    
   
15.请描述一下Broadcast Receiver。
有很多广播接收者 ,系统已经实现了.  
广播分两种：1.有序广播 2.无序广播
指定接收者的广播，是不可以被拦截掉的    

	<intent-filter android:priority="1000">
	<action android:name="android.provider.Telephony.SMS_RECEIVED"/>
	</intent-filter>
  
16.请介绍下ContentProvider是如何实现数据共享的。  
把自己的数据通过uri的形式共享出去  
android  系统下 不同程序 数据默认是不能共享访问 
	
	需要去实现一个类去继承ContentProvider
	public class PersonContentProvider extends ContentProvider{
	public boolean onCreate(){
		//..
	}
	query(Uri, String[], String, String[], String)
	insert(Uri, ContentValues)
	update(Uri, ContentValues, String, String[])
	delete(Uri, String, String[])
	}  
  
17.请介绍下Android的数据存储方式。  
文件  访问权限.  sdcard <data>/<data>  
数据库 sqlite  
SharedPreference  <data>/<data>/shared_preps  
网络  socket tcp udp , http httpurlconnection   
  
18.为什么要用ContentProvider？它和sql的实现上有什么差别？   
屏蔽数据存储的细节,对用户透明,用户只需要关心操作数据的uri就可以了，不同app之间共享,操作数据

Sql也有增删改查的方法. 但是contentprovider 还可以去增删改查本地文件. xml文件的读取,更改,
网络数据读取更改  
  
19.请解释下Android程序运行时权限与文件系统权限的区别。   
Android程序执行需要读取到安全敏感项必需在androidmanifest.xml中声明相关权限请求, 打电话,访问网络,获取坐标,读写sd卡,读写联系人等..安装的时候会提示用户…  
drwx  
文件系统的权限是linux权限. 比如说sharedpreference里面的Context.Mode.private    Context.Mode.world_read_able   Context.Mode_world_writeable    
777自己 同组 其他  
  
20.系统上安装了多种浏览器，能否指定某浏览器访问指定页面？  
找到对应的浏览器的意图,传递数据URI , 激活这个意图  

	Intent intent = new Intent();
	intent.setClassName(packageName, className);
	intent.seturi()  
  
21.ListView如何提高其效率？  

复用convertview , 历史的view对象  
减少子孩子查询的次数 viewholder  
异步加载数据, 分页加载数据,  
使用静态的view对象 避免创建过多的view.  
  
