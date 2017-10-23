
  
#Tomcat与web程序结构与http协议  
<hr>
##常见的服务器   
* WebLogic: orcale公司的产品，支持JAVAEE规范，收费  
* WebsphereAS: IBM公司的产品 ，支持JAVAee规范， 收费  
* Jboss: red公司的产品，支持EJB， 免费  
* Tomcat： apache公司产品支持JavaEE规范，免费  
  
![](https://i.imgur.com/RWPywVC.png)  
  
###常见启动问题  
* 端口号冲突：修改 "X:\XXX\Apache Software Foundation\Tomcat 9.0\conf\server.xml"文件  
* 闪退 ：设置Java home 环境变量  
  
![](https://i.imgur.com/iystaLA.jpg)  
  
##Tomcat目录结构  
![](https://i.imgur.com/Zr6FCvk.png)  

###webapps目录
* 可以通过浏览器直接访问  
* WEB-INF ： 文件夹下所有文件不允许浏览器直接访问，里面的资源是提供给服务器来访问的，当有动态资源的时候，此文件夹必须写上（理论上不上必须的，但实际开发中百分百会有）
	* web.xml ： 必须的(WEB-INF文件夹存在的前提下)
* lib ： jar包 只针对本应用程序
* classes　：　Java字节码文件
  
<hr>
##Tomcat体系架构   
![](https://i.imgur.com/z5bcSW3.png)  
  
###tomcat的组成结构  
![](https://i.imgur.com/W2Vx1f9.png)  
  
###应用程序的部署方式  
* 将应用程序直接拷贝到webapps目录下  
* 将应用程序打成war包, 直接拷贝到webapps目录下，在dos命令窗口下键入： jar –cvf  名字.war  
  
<hr>
##各种配置   
###虚拟应用  
* 在server.xml文件中配置如下 (此种配置不推荐)  ，通过浏览器输入  http://localhost:8080/abc/1.html 来访问
	* 缺点： 必须重新启动服务器
	![](https://i.imgur.com/Ux0uEbV.png)  
* 在 conf/catalina/localhost/目录下随意建立一个xml文件，文件名任意(eg: abcde.xml)。(不需要重启服务器) 推荐使用  
	* ![](https://i.imgur.com/Zhhg4l5.png)
	* 虚拟路径就是文件名  
  

* 默认应用的配置  
	* 只需要在conf/catalina/localhost/目录下建立一个ROOT.xml文件,内容同上,这个应用就是默认应用
	* 访问的时候通过URL: http://localhost:8080/a.html 来访问  
  
	![](https://i.imgur.com/QKRThz0.jpg)  
  
	![](https://i.imgur.com/fC8T12S.jpg)  
  
* 配置默认页面,端口号的修改  
	* 需要在我们应用目录下app1/WEB-INF目录下配置web.xml文件  
	* 配置如下：  
	![](https://i.imgur.com/ptBFoDC.png)  
  
	* 端口号修改需要在server.xml文件中配置：默认端口号配置完毕需要重新启动服务器，可以配置成默认的端口号： 80  
	![](https://i.imgur.com/E93BiA8.jpg)    
  
* 配置多个虚拟主机  
	* 假设e盘有个目录app1下有个应用app  
	* d盘有个目录 app下有个应用app2  
		则在server.xml中配置如下：  
![](https://i.imgur.com/4hDw8nr.png)  
	* 在C:\WINDOWS\system32\drivers\etc\hosts中配置路径的IP映射如下：  
![](https://i.imgur.com/xHhOzZF.png)  
此时就可以通过浏览器访问应用了:  
	* http://www.itheima.com/app/a.html  访问e盘app1/app应用下的a.html页面  
	* http://www.itcast.cn/app2/a.html  访问d盘app/app2应用下的a.html页面   
   
* 两种架构  
	* C/S 架构 与 B/S 架构
![](https://i.imgur.com/gTOQo5H.png)  
	* QQ是典型的C/S架构  
	* 网页访问B/S架构  
  
<hr>
##http协议   
* http协议： 规定了客户端和服务端交流时的数据格式 
* Telnet localhost 8080 //GET POST 请求  
* get和post方式区别：  
	* get方式请求参数会附加在url后，而post是看不到的，post相对安全  
	* get方式对提交的数据大小有限制(1k),post方式对数据大小没有限制  
  
* 响应的状态吗：  
	* 200 Ok  
	* 302(307) : 请求重定向  
	* 304 : 未修改    
	* 404: 找不到  
	* 500: 代码错误  

![](https://i.imgur.com/utcUatv.png)    

* 请求头信息:
	* Accept: 浏览器告诉服务端, 浏览器可接受的MIME类型  
	* MIME类型： 相当于操作系统下的文件类型。  
		* 是由大类型和小类型组成： text/html;  
		* 对应的关系在conf/web.xml中查询  
	* Accept-Charset: 浏览器告诉服务端, 浏览器支持哪种字符集
	* Accept-Encoding: 浏览器告诉服务端, 浏览器能够进行解码的数据编码方式，比如gzip
	* Accept-Language: 浏览器告诉服务端,浏览器支持的语言种类
	* Host: 浏览器告诉服务端,浏览器所在的主机
	* Referer: 浏览器告诉服务端，当前页面从哪里来的。  
		* 用处： 1. 用来记录广告的信息   2. 防盗链  
	* Content-Type: 浏览器告诉服务端 ,内容类型  
	* If-Modified-Since: 浏览器告诉服务端修改的时间  
	* Content-Length: 浏览器告诉服务端 请求正文内容长度  
	* Connection: 浏览器告诉服务端,连接的状态  
	* Cookie: 浏览器告诉服务端 ,浏览器携带的Cookie
	* Date: 浏览器告诉服务端,发送请求的时间  
  

* 响应头信息:  
	* Location : 服务端用来告诉浏览器,请求需要重定向.(必须结合状态吗302使用)  
   	Server : 服务端用来告诉浏览器,服务器的类型  
	* Content-Encoding: 服务端用来告诉浏览器,服务器对数据采用的编码  
	Content-Length:  服务端用来告诉浏览器,响应正文的长度  
   	Content-Language: 服务端用来告诉浏览器,zh-cn服务发送的文本的语言  
	* Content-Type: 服务端用来告诉浏览器,你要用哪个码表来解析二进制数据  
	* Last-Modified : 服务端用来告诉浏览器,访问资源的最后修改时间  
	* Refresh：服务端用来告诉浏览器,多长时间刷新一次  
	* Content-Disposition: 服务端用来告诉浏览器,以下载文件的方式打开文件  
 	* Expires: -1 : 服务端用来告诉浏览器,不要缓存 Cache-Control: no-cache (1.1)   Pragma: no-cache   (1.0)  
 	* Set-Cookie: 服务端用来告诉浏览器,要存储的内容  
