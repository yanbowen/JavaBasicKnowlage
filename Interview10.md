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
    
