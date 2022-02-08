### Day_12随堂笔记

#### Object

```java
Object : 超类,根类,基类 ; -> 炎黄
    是所有类的父类(一个类没有继承父类是默认继承Object)
    
构造方法:	
	Object(): Object类中只有无参构造;
		// super() :  每个代码的默认第一句

成员方法:
    int hashCode() : 获取对象的hash值
        //默认情况下: 对象的hash值就是对象在内存中十进制的地址值
	Class<?> getClass() : 获取对象的字节码文件(.class文件)对象(获取某个对象的类的类对象)
        //Class : 类的类  Class的对象: 具体某个类的类对象
        //一个类只可能有一个类对象!! 
    String toString()  : 返回- > 全类名@对象16进制地址值; --> 一般情况要重写
    boolean equals(Object obj) :  比较对象
        equals()方法的源码分析:
        public boolean equals(Object obj) {
            return (this == obj);
        }

      this == obj : 调用方法的对象 == 传入方法的对象

    需求: 你想比较对象的时候不比较对象的地址值而是对象的属性值,在子类中重写equals方法
```

#### 事物描述类的编写要求

```java
    在定义事物描述类的时候:
        1. 私有属性
        2. 无参构造
        3. 全参构造
        4. getter and setter
        5. 重写equals()方法 和 hashCode()方法 :  比较对象的时候比较不是对象的地址值而是对象的属性值
        6. 重写父类的toString() : 打印对象的时候看到对象的属性值而不是地址值
```

#### Objects

```java
Objects : 对象操作的工具类 JDK7才有 -->  这里面最喜欢做非空校验
    
static boolean isNull(Object obj) : 判断传入的对象是否为null;
	是null : true  不是null : false
static boolean nonNull(Object obj) : 判断传入的对象是否不为null;
	不是null : true 是null : false
    
    
static boolean equals(Object a, Object b)  : 调用对象a的equals方法,会对对象a做非空校验
    源码:    return (a == b) || (a != null && a.equals(b));
static int hashCode(Object o) : 调用对象o的hashCode方法但是会提前对o对象进行非空校验
    源码:    return o != null ? o.hashCode() : 0;
    
static String toString(Object o) : 调用对象o的toString方法但是会对o对象进行非空校验
    如果o对象是null,返回 "null" 
    以前: o.toString() --> o为null : 空指针异常
    现在: Objects.toString(o) --> o为null : "null" 
        
    源码 : return (obj == null) ? "null" : obj.toString();

static String toString(Object o, String nullDefault) :调用对象o的toString方法但是会对o对象进行非空校验
    如果o对象是null,返回 nullDefault 
    源码 : return (o != null) ? o.toString() : nullDefault;
```

#### System

```java
System :  关于系统操作的工具类
    
自定义常量:
	public static final InputStream in = null; -> 标准的系统输入流对象 -> 只做键盘录入
    public static final PrintStream out = null; -> 标准的系统输出流对象 -> 只做控制台打印
        
静态方法:
	static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)  
    : 数组复制 
    Object src : 原数组  , int srcPos : 原数组的位置(索引)
    Object dest : 目标数组 , int destPos : 目标数组的位置(索引)
    int length : 长度    
   * static long currentTimeMillis() : 获取当前系统时间的毫秒值
        
    static void exit(int status) :JVM退出  status: 状态码 非0即为异常退出     
```

#### Arrays

```java
Arrays:数组操作的工具类
static int binarySearch(int[] a, int key) : 查找key存在于a数组中的索引位置 (二分查找法)
    二分查找法前提是序列有序;
static int[] copyOf(int[] original, int newLength)  : 把原数组中的元素复制到一个长度为newLength的新数组中
static boolean equals(int[] a, int[] a2) : 比较2个数组是否是相同的数组 (比的数组的每一个元素值)   static void sort(int[] a) : 对a数组的元素进行排序 (原理: 快速排序)
static String toString(int[] a) : 对a数组的内容进行漂亮打印    
```

#### Math

```java
Math数学操作的工具类;
	
public final static double E = 2.7...;
public final static double PI  = 3.1414926...;

静态方法:
	static int abs(int a)  : 求传入的数a的绝对值;

	static double ceil(double a)  : 天花板 向上取整
    static double floor(double a) : 地板 向下取整
    static long round(double a) : 四舍五入
        
    static int max(int a, int b)  : 求2个数的较大值
    static int min(int a, int b)  : 求2个数的较小值
        
    static double pow(double a, double b) : 求a的b次幂
        
    static double random() : 求一个随机数 范围:[0,1.0)    
```

#### BigDecimal

```java
 BigDecimal : 高精度运算的类
    构造方法:
        //BigDecimal(double val) : 这个构造废物!! 不要使用这个构造!!
        BigDecimal(String val) : 推荐使用字符串形参的构造方法

    四则运算:
        BigDecimal add(BigDecimal augend)  : 加法
        BigDecimal subtract(BigDecimal subtrahend)  : 减法
        BigDecimal multiply(BigDecimal multiplicand)  : 乘法
        BigDecimal divide(BigDecimal divisor)  : 除法
        BigDecimal divide(BigDecimal divisor, int scale, RoundingMode roundingMode)   : 除法
            int scale :保留小数点后几位
            RoundingMode roundingMode : 舍入模式
                RoundingMode.CEILING/UP : 向上取整
                RoundingMode.FLOOR/DOWN : 向下取整
                RoundingMode.HALF_UP : 四舍五入


     BigDecimal对象变回成基本数据类型:
        xxxx xxxxValue() : 将此 BigDecimal 转换为 xxxx基本数据类型。   
```

#### StringBuilder/StringBuffer

```java
StringBuilder/ StringBuffer --> 在类型上和字符串一点关系都没有
    
StringBuilder/StringBuffer 为了服务String出现的 
    
StringBuilder/StringBuffer 的类成员是一样
    
StringBuilder 效率高,多线程环境下不安全
StringBuffer 效率低,多线程环境下安全
    
StringBuilder : 操作字符串的工具类; (字符串做拼接是非常消耗内存的)
StringBuilder : "可变字符串"
 
    

    StringBuilder构造方法:
        StringBuilder()
        StringBuilder(String str)
    成员方法:
        int capacity() : 获取当前容量
        *int length()  :返回字符串缓冲区类中的字符个数

        StringBuilder append(任意类型的数据) : 在StringBuilder的基础上拼接上传入的数据,返回自己
        StringBuilder reverse() : 一键翻转,返回的还是自己

    String --> StringBuilder:
        StringBuilder(String str)
    StringBuilder --> String
        String toString(); 
        sb对象 + "";     
```

#### JDK8版本之前的时间操作

##### Date

```java
Date : 日期类 -> 封装了 年月日时分秒 ;
    一个Date对象表示的一个时间瞬间(你new Date对象的一瞬间)!
        
使用的是 java.util.Date ! 
        
    构造方法:
        Date() : 获取的是当前创建Date对象的时间;
        Date(long date) : 1. 先把时间归零 2. 再加上传入的毫秒时间,定位的时间瞬间
            long date: 毫秒单位

        S M T W T F S

     Date时间 : Date对象
     long时间 : 毫秒值
     String时间 : "1998-01-01"    
         
     Date成员方法:
        String toLocaleString() : 以当地时区的习惯格式化时间格式
        String toGMTString() : 以零时区的时间表示当前时间

        long getTime(): 获取Date对象指向的时间瞬间的毫秒值
        void setTime(long time): 设置当前Date对象指向的时间瞬间
            1. 把时间清零
            2. 再加上传入的时间毫秒值 定位时间瞬间    
```

##### SimpleDateFormat

```java
SimpleDateFormat : 简单日期格式化类 (格式化和解析)

    
解析 : String类型的时间 --> Date类型的时间
    
格式化 : Date类型的时间 --> String类型的时间    
    
    构造方法:
	SimpleDateFormat(String pattern) : 以指定的时间代码来创建日期格式化的类
        String pattern : 传入指定的时间代码 重点注意: 时间代码要和日期格式一一对应
            yyyy MM dd HH mm ss

    SimpleDateFormat成员方法:
        Date parse(String time): 解析字符串时间,把字符串时间定位到Date对象上
        String format(Date date): 格式化Date时间,把Date时间格式化(变漂亮)    
```

##### Calendar

```java
Calendar : 日历类  -> 时间操作的工具类
    
因为Calendar类是一个抽象类不可与创建对象
    
获取Calendar类对象的方式:
	static Calendar getInstance()  
```

##### Calendar的获取和设置功能

```java
	获取日历对象上的时间信息:
        int get(int field): 通过传入时间字段获取这个时间字段的信息
            int field: 时间字段
    设置日历对象上的时间信息:
        void set(int field,int value):设置日历对象上的哪个时间信息成什么值
            int field: 时间字段   int value: 修改的值
        void set(int year, int month, int date, int hourOfDay, int minute, int second)
```

##### Calendar的转换功能

```java
    Calendar对象 ---> long时间
    long getTimeInMillis() : 直接获取Calendar对象指向时间的时间毫秒值
    long时间 ---> Calendar对象
    void setTimeInMillis(long millis)  : 把传入的毫秒值时间设置到日历对象上 (先把时间清零再加上传入的毫秒值)

    Calendar对象 ---> Date对象
        Date getTime()
    Date对象 ---> Calendar对象
        void setTime(Date date)
```

#### JDK8版本的时间操作

##### LocalDate/LocalTime/LocalDateTime

##### DateTimeFormatter

##### Period

##### Duration