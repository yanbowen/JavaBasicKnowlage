# 面试题目整理 08
<hr>     
  
### Android子线程创建Handler方法   
  
1.方法1（直接获取当前子线程的looper）  
  
如果我们想在子线程上创建Handler，通过直接new的出来是会报异常的比如：   
  
![](https://i.imgur.com/0R2EqjE.jpg)   
   
2.方法2（获取主线程的looper，或者说是UI线程的looper）   
   
![](https://i.imgur.com/LBPpxSA.jpg)   
   
---  
  
