### Day_18随堂笔记

#### File类(理解)

```java
File 由JDK描述计算机硬盘上的 文件 和 文件夹!
    //计算机硬盘上都是文件或者文件夹!!!    
```

#### File类的构造方法

```java
    *File(String pathname) : 传入路径,创建File对象指向路径结尾的文件或者文件夹

    File(String parent, String child):以String类型的子父路径的方式创建file对象
    *File(File parent, String child): 以File类型的父路径和String类型的子路径的方式创建File对象

    File对象的创建发生了什么事情:
        1. 创建一个File对象
        2. 在堆内存中开辟空间
        3. 让file对象指向路径结尾的文件/文件夹
        4. 不管文件/文件夹 存在与否, 创建File对象对文件/文件夹无影响!!
```

#### 相对路径和绝对路径(重要)

```java
绝对路径: 完整的文件/文件夹的路径  --> 一定以盘符出发
    //C:\Users\Administrator\Desktop\abc.txt
    //地球:\亚洲\中国\湖北省\武汉市\江夏区\茅店山中路\东湖网谷\六号楼\4楼\尚硅谷\403教室\谈斌;
相对路径: 以一个路径作为参照物,作为起点往路径内寻找   
	/*
		参照物/路径起点 : 东湖网谷
		六号楼\4楼\尚硅谷\403教室\谈斌;
	*/
    Java中相对路径的参照物/起点: Project
        
当你要寻找的文件在IDEA的项目下,建议使用相对路径;        
```

#### File类的创建功能

```java
File类能表示文件/文件夹.但是创建File对象并不帮你把文件和文件夹进行创建;

*boolean createNewFile() : 创建的是文件!
    //文件不存在帮你创建,文件存在返回false,不做任何操作!

boolean mkdir() : 只能创建单级文件夹
*boolean mkdirs() : 不仅可以创建单级文件夹也可以创建多级文件夹    
    
1. 具体创建的是什么,看的是你调用的是哪个方法! 而跟你定义的文件名称无关
2. 你要创建文件,必须要保证文件前置路径必须存在!    
```

#### File类的删除功能(了解)

```java
 boolean delete() : 删除文件或者文件夹
     //删除文件夹时如果文件夹内有文件/子文件夹,此文件夹删除不了
```

#### File类的判断功能

```java
*boolean exists()  :判断File对象指向的文件/文件夹是否存在
//文件不存在,都返回false
*boolean isFile() : 判断File对象指向的是否是文件      
*boolean isDirectory() :判断File对象指向的是否是文件夹
     
boolean isHidden() : 判断file对象指向的文件/文件夹是否是隐藏的
boolean isAbsolute(): 判断file对象指向的文件/文件夹创建时是否使用的是绝对路径    
```

#### File类的获取功能

```java
String getAbsolutePath():获取File对象的绝对路径,返回值是以String类型返回  
File getAbsoluteFile():获取File对象的绝对路径,返回值是以File类型返回 
String getPath() : 创建File对象时传入的是什么样子的路径就获取什么样子的路径
    //File类对象的toString()方法的源码就是调用file对象的getPath()方法
    
*String getName(): 获取文件或者文件夹的名称
    
String getParent() : 获取file对象的父路径,以String类型返回 
File getParentFile() : 获取file对象的父路径,以File类型返回 
     //会以file对象指向的文件/文件夹的所有前置路径作为父路径返回~

long lastModified() : 获取文件/文件夹最后修改时间, 以毫秒值返回 
long length() : 获取文件的字节数(文件的大小) //文件夹是没有大小的    
```

#### 单级文件夹的遍历

```java
*File[] listFiles(): 获取文件夹根目录下所有的文件和文件夹对象并存入到一个File[]中
    1. 只能获取根目录下的文件和文件夹
    2. 当file对象指向的是一个文件时,返回null
    3. 空文件夹也是可以遍历的只不过返回的是一个没有元素的数组而已
```

#### 带过滤器的文件夹遍历

```java
package com.atguigu.file;

import java.io.File;
import java.io.FileFilter;

/*
    File[] listFiles(FileFilter filter)  : 带有过滤器的遍历

    FileFilter: 文件过滤器接口
        抽象方法: boolean accept(File file)
 */
public class FileDemo9 {
    public static void main(String[] args) {
        //创建File对象
        File file = new File("T_Day16\\src\\com\\atguigu\\hashmap");

        File[] files = file.listFiles(new FileFilter() {
            @Override
            public boolean accept(File pathname) {
                return pathname.getName().endsWith(".java");
            }
        });

        for (File f : files) {
            System.out.println("f = " + f.getName());
        }
    }
}
```

#### 多级文件夹的遍历

```java
package com.atguigu.file;

import java.io.File;
import java.io.FileFilter;

/*
    遍历多级文件夹
 */
public class FileDemo10 {
    public static void main(String[] args) {
        //创建对象
        File file = new File("T_Day16");
        getFileName(file);
    }
    //创建方法,写方法的形式参数列表
    public static void getFileName(File root){
        //出口
        if (root.isFile()){
            if (root.getName().endsWith(".java")){
                System.out.println(root.getName());
            }
            return;
        }else{
            System.out.println("文件夹名称:"+root.getName());
        }
        //如果能从上面下来 说明root是一个文件夹
        File[] files = root.listFiles();
        for (File file : files) {//file对象是 子文件/子文件夹
            //直接再次调用这个方法
            getFileName(file);
        }
    }
}
```

#### IO流体系

```java
IO流技术 :  实现Java代码和硬件上的文件进行数据交换
    
IO流 : I -> Input/Reader 输入 O -> Output/Writer 输出    
    如何分别何时用输入还是输出: 参照物 -> 把自己想象成当前的Java代码
        
IO流体系分类 :
	1. 按流向来分 : 
		输入流: Input/Reader
		输出流: Output/Writer
    2. 按操作的文件类型来分 :  
		//计算机中一切皆字节
		操作字节文件的流 : 字节流 -> 可以操作计算机中所有类型的文件 
        操作字符文件的流 : 字符流 -> 只能操作计算机中的字符类型的文件
            字符文件如何分辨 : 能用windows自带的记事本打开并能使用的文件叫字符文件
                例如: .txt , .java , .xml , .html ....
                    //office系列软件不是 字符文件
                    //.md 是字符文件
                    
如何分辨流是操作什么类型文件的流:
	字节流 : 一定是以 Stream 结尾 
    字符流 : 一定是以Reader/Writer 结尾   
        
    InputStream : 字节输入流  BufferedOutputStream : 高效的字节输出流
    PrintStream : 打印字节输出流 
    FileWriter : 文件字符输出流 BufferedReader : 高效字符输入流
    ObjectOutputStream : 对象输出字节流    
    InputStreamReader : 字符输入流 (转换流: 把字节流转换成字符流) 
```

#### 字节流

```java
根节点 : InputStream/OutputStream
 
//字节流可以操作计算机中所有类型的文件    
```

#### FileInputStream/FileOutputStream - 普通的字节流

```java
常用的字节流对象: FileInputStream/FileOutputStream
```

##### FileOutputStream : 文件字节输出流

```java
构造方法:	
	FileOutputStream(String name) : 创建字节输出流对象并让这个流对象指向路径结尾的文件;
		//文件路径是以字符串形式表示
    FileOutputStream(File file) : 创建字节输出流对象并让这个流对象指向路径结尾的文件;
		//文件路径是以File类型的对象表示

字节输出流往外写的功能 : write
    void write(int b) : 一次写一个字节
    void write(byte[] b)  : 一次写一个字节数组
    void write(byte[] b, int startIndex, int length): 一次写一个字节数组的一部分  
    
解决问题:
        1. 追加写
            FileOutputStream(String name, boolean append)
                 boolean append: 追加写开关,默认关闭
            FileOutputStream(File file, boolean append)
        2. 写字符串
                把字符串转换成字节数组 -> String类中  byte[] getBytes()
        3. 换行
            "\r\n"    
```

##### FileInputStream : 文件字节输入流

```java
构造方法:
	FileInputStream(String name):创建一个字节输入流对象指向路径结尾的文件; 
    FileInputStream(File file):创建一个字节输入流对象指向路径结尾的文件; 
		//没有追加读的功能

字节输入流往内读的功能 : read
    int read() : 一次读一个字节
    int read(byte[] b) : 一次读一个字节数组
    int read(byte[] b, int off, int len) : 一次读一个字节数组的一部分 
```

#### 默写的代码

```java
 		FileInputStream fis = new FileInputStream("C:\\Users\\Administrator\\Desktop\\孟大宝 - 我要你（Cover 任素汐）.mp3");
        FileOutputStream fos = new FileOutputStream("T_Day18\\abc\\test\\一次一个字节数组复制的歌.mp3");
        //循环读写
        byte[] bys = new byte[1024];//存读到的数据
        int len;//统计读了多少个字节
        //循环读写
        while((len = fis.read(bys)) != -1){
            //写出去 读了多少写多少!!
            fos.write(bys,0,len);
        }

        //关流
        fis.close();
        fos.close();
```

