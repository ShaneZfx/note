## 第 2 章 java 程序概念

### 2.1 特性概述

1. 自动拆装箱

   ```java
   /* 区别几种声明 */
   // 1.创建Integer对象，每一个new都是一个新的对象！！
   Integer test01 = new Integer(1);
   // 2.自动装箱，编译后会调用java.lang.Integer#valueOf(int)方法
   Integer test02 = 1;
   // 3.原始类型
   int i = 1;
   // 4.自动拆箱，编译后会调用java.lang.Integer#intValue方法
   int j = test02;
   ```

   - 包装类引用遇到数值类型，会自动拆箱

   - `==` 比较的是引用地址，针对引用类型在不遇到运算情况下，不会拆箱

   - [-128 , 127]范围内的数值有缓存，区间内同一数值进行装箱后的对象是同一对象

     ```java
     public static Integer valueOf(int i) {
             if (i >= IntegerCache.low && i <= IntegerCache.high)
                 return IntegerCache.cache[i + (-IntegerCache.low)];
         // 不在区间内的数值装箱会创建新对象
             return new Integer(i);
     }
     ```

     

## 第 4 章 类与对象

## 第 5 章 继承

## 第 6 章 接口、lambda、内部类

### 6.3 Lambda

1. `lambda`是什么
   
   > lambda是一个可以**传递**的**代码块**，可以在**以后**执行一次或多次。如比较器`Comparator`接口，当通过定制比较器来完成排序（如按照字符串长度，而不是字典顺序）
   ```java
   public class SortByComparator {

    public static void main(String[] args) {
        String[] strs = new String[]{"a", "ads", "afafasf", "qw"};
        Arrays.sort(strs, new LengthComparator());
        System.out.println(Arrays.toString(strs));
    }
   }
   // 长度比较器
   class LengthComparator implements Comparator<String> {
    @Override
    public int compare(String o1, String o2) {
        return o1.length() - o2.length();
    }
   }
   ```
   > 可以向sort方法传入Comparator实例，compare方法不是立即调用，并且在数组完成排序之前，会一直调用
2. `lambda`语法
   
   > `lambda`的整个表达式，是对某个方法的抽象表达，

### 6.4 内部类

1. 内部类的设计意义及作用
   
   > - 实现多重继承
   >
   >   ```java
   >   public class Father {
   >       public String fName = "李老八";
   >   }
   >   
   >   public class Mother {
   >       public String mName = "杜鹃";
   >   }
   >   
   >   public class Son {
   >       class Extends_Father extends Father{
   >       }
   >   
   >       class Extends_Mother extends Mother{
   >       }
   >   
   >       public void sayHello(){
   >           String fName = new Extends_Father().fName;
   >           String mName = new Extends_Mother().mName;
   >           System.out.println("my father is:" + fName + "my mother has:" + mName);
   >       }
   >   }
   >   ```
   >
   >   
   >
   > - 闭包
   >
   >   
   >
   > - 封装特性
   
   - 内部类方法可以访问该类**定义所在的作用域的数据**，包括私有数据
   - 内部类可以对同一包中的其他类隐藏
   - 当想要定义一个回调函数且不想编写大量代码，使用匿名内部类比较便捷

#### 6.4.1 成员内部类

1. 实例的创建方式

   

2. 访问方式

3. 与外部类的交互方式

4. 限制条件

   > 成员内部类实现对外部类中的私有变量的访问，实现相关原理：
   >
   >    - 内部类持有一个对外部类的引用
   > ```java
   > public class cn.zfx.original.innerClass.TalkingClock$TimePrinter implements java.awt.event.ActionListener {
   > // 持有外部类引用，随意访问外部类可见域；不可见域的访问还应由外部类提供方法（编译器合成）
   > final cn.zfx.original.innerClass.TalkingClock this$0;
   > public TimePrinter(cn.zfx.original.innerClass.TalkingClock);
   > public void actionPerformed(java.awt.event.ActionEvent);
   > }
   > ```
   >    - 外部类提供私有域的访问方法
   > ```java
   > public class cn.zfx.original.innerClass.TalkingClock {
   >   private int interval;
   >   private boolean beep;
   >   public cn.zfx.original.innerClass.TalkingClock(int, boolean);
   >   public void start();
   >   // 对私有域的访问方法，使用静态方法的原因是--能访问静态域？？
   >   static boolean access$000(cn.zfx.original.innerClass.TalkingClock);
   > }
   > ```

#### 6.4.2 局部内部类

1. 实例的创建方式
2. 访问方式
3. 与外部类的交互方式
4. 限制条件

   > - 局部内部类不能显式地使用访问权限修饰符声明，它的作用域被限定在声明这个局部类的块中。并且在`TalkingClock`类中的其他代码也无法访问该局部类，除了start方法，没有方法知道`TalkingClock`的存在
   > ```java
   > public class TalkingClock {
   > private int interval;
   > private boolean beep;
   > public TalkingClock(int interval, boolean beep) {
   > this.interval = interval;
   > this.beep = beep;
   > }
   > public void start() {
   > // 无修饰符
   >   class TimePrinter implements ActionListener {
   >       @Override
   >       public void actionPerformed(ActionEvent e) {
   >           System.out.println("at the tone, the time is" + new Date());
   > 
   >           if (beep) {
   >               Toolkit.getDefaultToolkit().beep();
   >           }
   >       }
   >   }
   >   ActionListener listener = new TimePrinter();
   >   Timer t = new Timer(interval, listener);
   >   t.start();
   > }
   > }
   > 
   > TalkingClock$1TimePrinter implements java.awt.event.ActionListener {
   > final cn.zfx.original.innerClass.TalkingClock this$0;
   > TalkingClock$1TimePrinter(cn.zfx.original.innerClass.TalkingClock);
   > public void actionPerformed(java.awt.event.ActionEvent);
   > }
   > ```
   >
   > - 局部内部类相较于其他内部类而言，除了能访问外部类外，还能访问局部变量（局部变量必须是final修饰的，以便当前方法结束后，内部类里的方法还能继续访问此变量）
   > ```java
   > ...略
   >     public void start(int interval, boolean beep) {
   >         class TimePrinter implements ActionListener {
   > 
   >             @Override
   >             public void actionPerformed(ActionEvent e) {
   >                 System.out.println("at the tone, the time is" + new Date());
   > 
   >                 if (beep) {
   >                     Toolkit.getDefaultToolkit().beep();
   >                 }
   >             }
   >         }
   >         ActionListener listener = new TimePrinter();
   >         Timer t = new Timer(interval, listener);
   >         t.start();
   >     }
   > ...
   > // 反编译结果
   > TalkingClock$1TimePrinter implements java.awt.event.ActionListener {
   >   // 持有常量 
   >   final boolean val$beep;
   >   final cn.zfx.original.innerClass.TalkingClock this$0;
   > 
   >   TalkingClock$1TimePrinter(cn.zfx.original.innerClass.TalkingClock var0, boolean var1);
   > 
   >   public void actionPerformed(java.awt.event.ActionEvent var0);
   > }
   > ```

#### 6.4.3 匿名内部类

1. 实例的创建方式
2. 访问方式
3. 与外部类的交互方式
4. 限制条件

> ```java
> public class TalkingClock {
>     
>     public void start(int interval, boolean beep) {
>         ActionListener listener = new ActionListener() {
>             @Override
>             public void actionPerformed(ActionEvent e) {
>                 System.out.println("at the tone, the time is" + new Date());
> 
>                 if (beep) {
>                     Toolkit.getDefaultToolkit().beep();
>                 }
>             }
>         };
>         Timer t = new Timer(interval, listener);
>         t.start();
>     }
> }
> ```
>
> 

#### 6.4.4 静态内部类

> ```java
> public Class A{
>     static Class InnerClassA{
>         
>     }
> }
> ```
>
> 

1. 实例的创建方式
2. 访问方式
   - ==静态内部类能直接访问外部类的类属性a（编译器在外部类里为属性a添加了一个静态方法`access$100()`）==
   - 静态内部类不能直接访问外部类的实例属性，必须先创建实例
   - 外部类访问静态内部类的实例属性时，也必须先创建实例
3. 与外部类的交互方式
   - 静态内部类只能直接访问外部类的类属性（static属性）
   - 外部类的外部对于静态内
   - 外部类能直接访问静态内部类的静态变量
   - 外部类能通过静态内部类的实例访问任意权限的域和方法
4. 限制条件

> - 静态内部类不依赖外部类实例对象（因为只能访问外部静态量）
> - 相当于外部类的一个类变量
> - 同样可以加访问权限控制符

#### 6.4.5 内部类与权限修饰符

|            | public | protected | default | private | 备注 |
| ---------- | ------ | --------- | ------- | ------- | ---- |
| 成员内部类 |        |           |         |         |      |
| 静态内部类 |        |           |         |         |      |
| 局部内部类 |        |           |         |         |      |
| 匿名内部类 |        |           |         |         |      |



### 6.5 代理

## 第 7 章 异常

### 7.1 异常分类

> `Runtime Exception`（logic_error）:产生于代码而非环境。（逻辑错误等）

Runtime Exception : 非受查异常

派生于Error、以及RuntimeException的异常类，都称为非受查异常

`如果出现RuntimeException异常，那么就一定是你的问题`

一个方法必须声明所有可能抛出的`受查异常`，而`非受查异常`要么是不可控的`Error`，要么是应该避免发生的`RuntimeException`

### 7.2 异常处理

#### 7.2.1 抛出

#### 7.2.2 捕获

## 第 8 章 泛型

### 8.8 通配符

#### 8.8.1 概念

## 第 14 章 并发



![java并发编程](G:\workspace-git\note\docs\attached-picture\thread.png)

### 14.1 线程

#### 14.1.1 状态

> ```java
> /* Java线程状态 */
> public enum State {
>  NEW,// 线程还未start
>  RUNNABLE,// 
>  BLOCKED,
>  WAITING,
>  TIMED_WAITING,
>  TERMINATED
> }
> ```
>

#### 14.4.2 概念



### 14.2 Java Memory Model

#### 14.2.1 volatile

1. 含义

   > - `volatile`变量在各线程的工作内存中不存在一致性问题（在各个线程的工作内存中，volatile变量是可以存在不一致的情况的，但由于每次使用之前都要先刷新，执行引擎看不到不一致的情况，因此可以认为不存在一致性问题）。
   > - `volatile`变量对所有线程时立即可见的，对`volatile`变量所有的写操作都能立刻反应到其他线程中，换句话说，volatile变量在各个线程中是一致的。==<u>所以基于`volatile`变量的运算在并发下是安全的（错误）</u>==
   > - `volatile`关键字只保证对公共变量写操作的可见性，比如存在`volatile`变量`race`，线程A，B，C均对其进行自增运算（自增5）
   >   - 线程C先将`race`自增到5，并同步到主内存
   >   - A，B使用`race`，关键字保证了此时`race`能读到主内存已经被写入的数值5
   >   - A，B自增中（使用`race`中）~
   >   - ==在B使用指令自增的过程中，<u>A先一步将`race`自增至10，并同步至主内存（B对此结果未知，`race`已经在B中使用，并不会再一次刷新！）</u>==
   >   - B自增完，将工作内存中的10刷回主内存，最终`race`的结果是10，而不是目标15

2. 使用规则

   > 由于volatile变量只保证可见性，在不满足以下两条规则的运算场景中，仍要通过加锁来保证原子性
   >
   > - [x] 运算结果并不依赖当前值（==个人理解是变量当前最新值==），或者能够确保只有单一的线程修改变量的值
   > - [x] 变量不需要与其他的状态变量共同参与不变约束
   
3. volatile的作用

   - 保证变量的内存可见性，符合happen-before原则

     > 问题在于，既使线程A对变量的写操作在B读之前，也只能保证B本次读到最新数据，但在B使用这个所谓的最新值时，A仍然可以修改变量的值，也就是==改了又改的漏洞==

   - 防止指令重排


### 14.3 线程安全

#### 14.3.1 锁

1. 显式锁/隐式锁

   - 显式锁

     > 对需要上锁的代码块显式加锁，如`lock.lock()`，并需要解锁，如`lock.unlock()`

   - 隐式锁

     > 不需要在代码中显式加锁，有虚拟机进行加锁，如`synchronized`关键字

2. 乐观锁/悲观锁

   - 乐观锁

     > 乐观地认为对于同一个数据来说，不会被其他线程修改，因此不会加锁。更新数据的时候，借助**冲突检查机制**来判断更新过程中是否存在来自其他线程的干扰，存在则操作失败，并且可以重试。 

   - 悲观锁

     >  悲观地认为对于同一个数据来说一定会被其它线程修改，因此必须上锁。常见实现有`Synchronized`，`ReentrantLock`等

3. 独享锁/共享锁

   - 独享锁

     > 同一时间只能由同一线程所持有。常见实现有`Synchronized`、`ReentrantLock`等

   - 共享锁

     > 同一时间内可被多个线程所持有。常见实现是`ReadWriteLock`，其读锁是共享锁，写锁是独占锁。极大提高并发读的效率

4. 互斥锁/读写锁

   - 互斥锁

     > 互斥锁就是独占锁的具体实现

   - 读写锁

     > 读写锁就是共享锁的具体实现

5. 公平锁/非公平锁

   - 公平锁

     > 公平锁是指按申请锁的顺序来获取锁，具体实现有`ReentrantLock`，默认是非公平锁，但可以通过构造函数设定为公平锁，通过AQS来实现线程调度变成公平锁

   - 非公平锁

     >  非公平锁指的是多个线程并非按照申请锁的顺序来获取锁 

6. 偏向锁/轻量级锁/重量级锁

7. 可重入锁

8. 分段锁

9. 自旋锁

#### 14.3.2 实现基础

1. AQS

   - AQS`是一个什么东东

     > `AQS`全称`AbstractQueueSynchronizer`（抽象同步队列），是`juc`并发包中实现同步器的基础组件。

   - Node

     `AQS`的静态内部类Node，是队列的元素单元

2. CAS

### 14.5 并发设计

#### 14.5.1 Future

#### 14.5.2 Executor



### ThreadLocal

![ThreadLocal.uml](G:\workspace-git\note\docs\attached-picture\ThreadLocal.png)

线程结束，ThreadLocal也会被回收，所以可以不用考虑remove的事情。例外就是使用线程组，线程始终存活，ThreadLocal不会主动释放引用，需要手动释放，避免内存溢出！



### 线程API

#### Thread API

1. join方法

   等待调用线程结束

   > An invocation of this method behaves in exactly the same way as the invocation
   >
   > 此方法的调用跟正常方法的调用相同，等到调用结束，再继续执行

   ```java
   // join核心代码片段，内部依旧是让线程在调用对象上等待
   while (isAlive()) {
   	wait(0);
   }
   ```

   

2. sleep方法

   **sleep能让线程进行等待，但是并不释放任何资源**

3. yield方法

   当前线程让出cpu

#### Object API

1. wait

   **wait方法会释放线程占用的锁资源**，当一个对象调用wait方法后，当前线程会在这个对象上等待（之所以 wait 和 notify 是 Object 上的方法，因为这方法起到的是线程通信的作用。不单单知识本线程的一些操作，所以需要一个对象作为通信手段，多个线程在这个对象上进行通信）

2. notify/notifyAll

## JVM

### 虚拟机类加载机制

#### 加载过程

1. 加载

   > A. 通过类的全限定名来获取定义这个类的二进制字节流
   >
   > B. 将这个字节流代表的静态存储结构转化为方法区的运行时数据结构
   >
   > C. 在内存中生成一个代表这个类的 `java.lang.Class` 对象，作为方法区的这个类的各种数据结构的访问入口

2. 链接

   - 验证

   - 准备

     > 准备阶段是正式为==类变量==在方法区分配内存空间并设置初始值的阶段。
     >
     > ```java
     > public static int value = 123;
     > ```
     >
     > 变量 `value `在准备阶段会被分配内存空间，并设置`初始值0`，而对`value`的正式赋值指令`putstatic`是在程序被编译后，存放于类构造方法 `<cinit>()` 中，所以类变量的正式赋值动作在初始化的时候触发

   - 解析

3. 初始化

   > A. 类初始化是类加载过程的最后一步，前面的加载过程中，除了在加载阶段用户应用程序可以通过自定义类加载器参与外，其余动作完全由虚拟机主导。==到了初始化阶段，才开始真正执行类定义中的Java代码（字节码）==
   >
   > B. `<cinit>()` 方法由编译器自动搜集类中所有==类变量==的赋值动作以及静态语句块里的语句合并产生，编译器搜集顺序按语句在源文件中出现的顺序
   >
   > C. 类构造器 `<cinit>()` 与实例构造器 `<init()>` 不同，它不需要显式调用父类的类构造器，==虚拟机会保证在子类的类构造器执行之前，父类的 `<cinit>()` 已经执行完毕（也即父类经过了加载过程）==
   >
   > D. `<cinit>() `方法对类或接口来说并不是必需的，如果一个类没有静态语句块，也没有对类变量的赋值操作，编译器也可以不为这个类生成类构造器
   >
   > E. 接口中不能使用静态语句块，但仍有变量初始化的赋值操作，接口也生成 `<cinit>()` 。执行接口的类构造器，不需要先执行父接口的类构造器，只有当使用父接口中定义的变量时，父接口才会初始化。另外，接口实现类在初始化时一样不会执行接口的 `<cinit>()` 方法
   >
   > F. 虚拟机保证一个类的 `<cinit>()` 方法在多线程环境中被正确加锁同步，只会有一个线程去执行这个类的类构造器，其他线程阻塞等待，直到活动线程的 `<cinit>()` 执行完成

#### 类加载器

### 虚拟机字节码执行引擎

#### 运行时栈帧结构

1. 局部变量表

## 附录

### 日常所需

| 序号 | 概念       | 解释 | 进度 |
| ---- | ---------- | ---- | ---- |
| 1    | JNDI注册器 |      |      |

### 问题集锦

1. 详解继承和实现的关系

   ```java
   // AbstractMap已经实现了Map，HashMap还要实现Map的原因
   public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Cloneable, 
   Serializable{ // --- }
   ```
   
2. MurmurHash3算法

3. Disruptor 
