### Day_18随堂笔记

#### IO概述

```java
I/O: 代码世界通往现实世界的桥梁,完成了数据的永久存储;
    
I : Input/read -> 输入
O : Output/Write -> 输出
    
如何分辨自己该使用输入还是输出 ?
   参照物 : 把自己想象成当前Java代码
       
IO流的分类:  //流 : IO的数据操作就很像水流一样
	1. 输入和输出
        I : Input/Reader -> 输入流
		O : Output/Writer -> 输出流
    2. 操作文件的类型: //计算机中一切皆字节,字符文件只是字节文件中的一部分
		字节流: 计算机中所有的文件都可以操作 
            以 XxxxStream 结尾的
        字符流: 字符文件推荐使用字符流操作 // 能使用windows自带的记事本打开并正常浏览的文件
            以 Reader/Wirter结尾的
            //.txt , .java , .html , .xml , .css ,. md....
            //office全家桶系列文件 : 不是字符文件
            
      FileInputStream: 普通字节输入流
      BufferedReader: 高效字符输入流    
      OutputStreamWriter : 转换流 -> 字符输出流    
          //字符流 = 字节流 + 编码格式;
      ObjectInputStream : 对象字节输入流 -> 反序列化流    
```

#### 字节流概述

```java
字节流可以操作计算机中所有类型的文件;

根节点 :InputStream/OutputStream -> 抽象类
    
常用的子类:
	FileInputStream/FileOutputStream :  普通字节输入输出流
    BufferedInputStream/BufferedOutputStream : 高效字节输入输出流    
```

#### FileOutputStream: 普通字节输出流

```java
创建对象 :
	FileOutputStream(String name) : 传入目标文件的路径,创建FileOutputStream对象,让输出流对象指向这个文件;
	--> new FileOutputStream("文件路径");  --> 推荐
	FileOutputStream(File file): 传入目标文件的File对象,创建FileOutputStream对象,让输出流对象指向这个文件;
	--> new FileOutputStream(new File("文件路径"));
```

#### OutputStream 写的功能(输出功能) : write()

```java
*void write(int b)  : 一次写一个字节
void write(byte[] bys)  :  一次写一个字节数组
**void write(byte[] bys, int startIndex, int length) : 一次写一个字节数组的一部分
    	int startIndex: 从哪个位置开始写
        int length: 写几个字节
            
    1. 如何写字符串
        String -> byte[] : byte[] getBytes();
    2. 如何让文件内容换行
        "\r\n"
    3. 如何追加写
         boolean append: 追加写开关
        FileOutputStream(String name, boolean append)
        FileOutputStream(File file, boolean append)


    注意:
        1. 用字节流往字符文件中写字节,对应出现的是其ASCII码表中的字符            
```

#### FileInputStream: 普通字节输入流

```java
创建对象:
	FileInputStream(String name) : 传入源文件路径,让FileInputStream对象指向源文件;
	FileInputStream(File file): 传入源文件路径(File对象),让FileInputStream对象指向源文件 
        
    	创建字节输入流对象:
	    1. 创建了对象
	    2. 开辟了内存空间
	    3. 创建了输入流对象指向路径结尾的源文件
	    4. 若源文件不存在,会报错!!    
```

#### InputStream 读的功能(输入功能) : read()

```java
读的功能:
	*int read() : 一次读一个字节
 	**int read(byte[] b) : 一次读一个字节数组
    int read(byte[] b, int off, int len) :  一次读一个字节数组的一部分 
```

#### 高效的字节输入输出流

```java
BufferedInputStream/BufferedOutputStream : 缓冲区流,包装流
    //这一对流不具备读和写的能力,读和写本质上还是靠传入的基本流对象!!
    
构造方法:
	BufferedOutputStream(OutputStream out)
    BufferedInputStream(InputStream in)    
        
读和写的方法
    输出流-> 写的功能 : 
		*void write(int b)  : 一次写一个字节
		void write(byte[] bys)  :  一次写一个字节数组
		**void write(byte[] bys, int startIndex, int length) : 一次写一个字节数组的一部分
        void flush()  : 高效流写数据记得刷新!!
    输入流-> 读的功能:
		*int read() : 一次读一个字节
        **int read(byte[] b) : 一次读一个字节数组
        int read(byte[] b, int off, int len) :  一次读一个字节数组的一部分 
```

#### 字符流概述

```java
根节点: 
	Writer/Reader -> 抽象类
        
常用的字符流:
	FileWriter/FileReader : 普通的字符输出输入流
    BufferedWriter/BufferedReader : 高效的字符输出输入流     
        
 //字符流的本质是 字节流 + 编码格式;  --> 字符流是高度封装,所以在创建对象的时候看不出来
 //字符流也是缓冲流 , 字符输出流在写数据的时候记得刷新 
 //字符流 只能操作字符文件!!
```

#### FileWriter:普通字符输出流

```java
构造方法:
	FileWriter(String fileName) : 直接传入文件字符串路径,创建字符输出流对象;
    FileWriter(File file): 传入File文件对象,创建字符输出流对象; 

    FileWriter(String fileName, boolean append): 字符输出流带有追加写功能的构造方法 ;    
    FileWriter(File file, boolean append): 字符输出流带有追加写功能的构造方法 ;    

//想要追加写 : FileOutputStream/FileWriter
```

#### Writer写的功能(输出功能):write()

```java
 *void write(int ch)  : 一次写一个字符 (参数是int : 字符既可以传入字符也可以传入字符对应的ASCII码)
 void write(char[] cbuf) : 一次写一个字符数组
 **void write(char[] cbuf, int off, int len) : 一次写一个字符数组的一部分
 ***void write(String str) : 一次写一个字符串
 void write(String str, int off, int len) : 一次写一个字符串的一部分     
     
 //字符流是缓冲流,写数据不关流的话需要刷新!! 
     abstract  void flush()  :刷新!    
```

#### FileReader: 普通字符输入流

```java
构造方法:
	FileReader(String fileName) : 直接传入源文件路径(String);
	FileReader(File file) : 传入源文件的File对象
        
 //FileReader指向的源文件必须存在!!
 //FileReader没有追加读的功能!!       
```

#### Reader读的功能(输入功能):read()

```java
 *int read()  : 读取单个字符
 **int read(char[] cbuf)  : 读取字符数组
 int read(char[] cbuf, int off, int len) : 读取字符数组的一部分

     都可以读到换行符!!    
```

