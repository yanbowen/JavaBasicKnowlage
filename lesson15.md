# Java基础15  
<hr>     
  
## 字符流缓冲区
* 缓冲区的出现提高了对数据的读写效率。  
  
* BufferedWriter  

 将文本写入字符输出流，缓冲各个字符，从而提供单个字符、数组和字符串的高效写入。  


	//创建一个字符写入流对象
	FileWriter fw = null;
	BufferedWriter bufw = null;
	try
	{
		fw = new FileWriter("demo.txt");
		bufw = new BufferedWriter(fw);
		bufw.write("abdcdfd");
		bufw.flush();
		
	}
	catch (IOException e)
	{
		 e.printStackTrace();
	}
	finally
	{
		try
		{
			if(bufw!=null)
				bufw.close();  
		}
		catch (IOException e)
		{
			 e.printStackTrace();
		}
	}
  

* BufferedReader  

从字符输入流中读取文本，缓冲各个字符，从而实现字符、数组和行的高效读取。    
  


	//创建一个字符读取流对象
	FileReader fr = null;
	BufferedReader bufr = null;
	try
	{
		fr = new FileReader("buf.txt");
		//为了提高效率，加入缓冲技术，将字符读取流对象作为参数传递给缓冲对象的构造函数
		bufr = new BufferedReader(fr);
        
		String tempString = null;
        // 一次读入一行，直到读入null为文件结束
        while ((tempString = bufr.readLine()) != null)
		{
        	// 显示行号
            System.out.println("tempString " + tempString);
        }

        bufr.close();
		
	}
	catch (IOException e)
	{
		 e.printStackTrace();
	}
	finally
	{
		try
		{
			if(bufw!=null)
				bufw.close();  
		}
		catch (IOException e)
		{
			 e.printStackTrace();
		}
	}  
  
---
### 通过缓冲区复制一个.java文件  
  
	class Copy  
	{
		public static void main(String[] args)
		{
			BufferedWriter bufw = null;
			BufferedReader bufr = null;

			try
			{
				bufr = new BufferedReader(new FileReader("Buffered.java"));
				bufw = new BufferedWriter(new FileWriter("Buffered.txt"));
				
				String line = null;
				
				while((line=bufr.readLine())!=null)
				{
					bufw.write(line);
					bufw.newLine();
					bufw.flush();
				}
			}
			catch (IOException e)
			{
				throw new RuntimeException("读写失败");
			}
			finally
			{
				try
				{
					if(bufr!=null)
						bufr.close()
				}
				catch (IOException e)
				{
					throw new RuntimeException("读取关闭失败");
				}
				try
				{
					if(bufw!=null)
						bufw.close();
				}
				catch (IOException e)
				{
					throw new RuntimeException("写入关闭失败");
				}
			}
		}
	}
  
--- 
  
## 字节流  
  
* InputStream
* OutputStream  
  
操作图片数据
  
	public static void fileStream()
	{
		FileInputStream fis = null;
		FileOutputStream fos = null;
		
		try
		{
			fis = new FileInputStream("1.bmp");
			fos = new FileOutputStream("2.bmp")

			byte[] buf = new byte[1024];

			int len = 0;

			while((len=fis.read(buf))!=-1)
			{
				fos.write(buf,0,len);
			}
		}
		catch (IOException e)
		{
			throw new RuntimeException("复制文件失败");
		}
		finally
		{
			try
			{
				if(fis!=null)
					fis.close();
			}
			catch (IOException e)
			{
				throw new RuntimeException("读取关闭失败")
			}
			try
			{
				if(fos!=null)
					fos.close();
			}
			catch (IOException e)
			{
				throw new RuntimeException("读取关闭失败")
			}

		}
		
	}
	  
---  
  
	long start = System.currentTimeMillis();
	XXXX;
	long end = System.currentTimeMillis();   

---  
  
