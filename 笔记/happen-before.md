再来重复下八大原则：

- 单线程happen-before原则：在同一个线程中，书写在前面的操作happen-before后面的操作。
- 锁的happen-before原则：同一个锁的unlock操作happen-before此锁的lock操作。
- volatile的happen-before原则：对一个volatile变量的写操作happen-before对此变量的任意操作(当然也包括写操作了)。
- happen-before的传递性原则：如果A操作 happen-before B操作，B操作happen-before C操作，那么A操作happen-before C操作。
- 线程启动的happen-before原则：同一个线程的start方法happen-before此线程的其它方法。
- 线程中断的happen-before原则：对线程interrupt方法的调用happen-before被中断线程的检测到中断发送的代码。
- 线程终结的happen-before原则：线程中的所有操作都happen-before线程的终止检测。
- 对象创建的happen-before原则：一个对象的初始化完成先于他的finalize方法调用。

首先在看本文前，最好先看下[《java并发编程实战》之java内存模型](https://www.jianshu.com/p/47f999a7c280)这篇文章，对java内存模型有个了解。
 happen-before 在这里不能理解成在什么之前发生，它和时间没有任何关系。个人感觉解释成“*生效可见于*” 更准确。下面通过对这八个原则详细解释来加深对“*生效可见于*”的理解。

------

**在同一个线程中，书写在前面的操作happen-before后面的操作：** 好多文章把这理解成书写在前面先发生于书写在后面的代码，但是指令重排序，确实可以让书写在后面的代码先于书写在前面的代码发生。这是里把happen-before 理解成“先于什么发生”，其实happen-beofre在这里没有任何时间上的含义。比如下面的代码：

```java
int a = 3;      //1
int b = a + 1; //2
```

这里 //2 对b赋值的操作会用到变量a，那么java的“单线程happen-before原则”就保证 //2的中的a的值一定是3，而不是0，5,等其他乱七八糟的值，因为//1 书写在//2前面, //1对变量a的赋值操作对//2一定可见。因为//2 中有用到//1中的变量a，再加上java内存模型提供了“单线程happen-before原则”，所以java虚拟机不许可操作系统对//1 //2 操作进行指令重排序，即不可能有//2 在//1之前发生。但是对于下面的代码：

```java
int a = 3;
int b = 4;
```

两个语句直接没有依赖关系，所以指令重排序可能发生，即对b的赋值可能先于对a的赋值。

------

**同一个锁的unlock操作happen-beofre此锁的lock操作：** 话不多说直接看下面的代码：

```java
public class A {
   public int var;

   private static A a = new A();

   private A(){}

   public static A getInstance(){
       return a;
   }

   public synchronized void method1(){
       var = 3;
   }

   public synchronized void method2(){
       int b = var;
   }

   public void method3(){
       synchronized(new A()){ //注意这里和method1 method2 用的可不是同一个锁哦
           var = 4;
       }
   }
}
```

```java
//线程1执行的代码：
A.getInstance().method1(); 
```

```java
//线程2执行的代码：
A.getInstance().method2(); 
```

```java
//线程3执行的代码：
A.getInstance().method3();
```

如果某个时刻执行完“线程1” 马上执行“线程2”，因为“线程1”执行A类的method1方法后肯定要释放锁，“线程2”在执行A类的method2方法前要先拿到锁，符合“锁的happen-before原则”，那么在“线程2”method2方法中的变量var一定是3，所以变量b的值也一定是3。但是如果是“线程1”、“线程3”、“线程2”这个顺序，那么最后“线程2”method2方法中的b值是3，还是4呢？其结果是可能是3，也可能是4。的确“线程3”在执行完method3方法后的确要unlock，然后“线程2”有个lock，但是这两个线程用的不是同一个锁，所以JMM这个两个操作之间不符合八大happen-before中的任何一条，所以JMM不能保证“线程3”对var变量的修改对“线程2”一定可见，虽然“线程3”先于“线程2”发生。

------

**对一个volatile变量的写操作happen-before对此变量的任意操作：**

```java
volatile int a;
```

```java
a = 1; //1
```

```java
b = a;  //2
```

如果线程1 执行//1，“线程2”执行了//2,并且“线程1”执行后,“线程2”再执行,那么符合“volatile的happen-before原则”所以“线程2”中的a值一定是1。

------

**如果A操作 happen-before B操作，B操作happen-before C操作，那么A操作happen-before C操作：**如果有如下代码块：

```java
volatile int var;
int b;
int c;
```

```java
b = 4; //1
var = 3; //2
```

```java
c = var; //3
c = b; //4
```

假设“线程1”执行//1 //2这段代码,“线程2”执行//3 //4这段代码。如果某次的执行顺序如下：
 //1  //2  //3  //4。那么有如下推导( hd(a,b)表示a happen-before b）：

> 因为有hd(//1,//2) 、hd(//3,//4) (单线程的happen-before原则)
>  且hd(//2,//3) （volatile的happen-before原则）
>  所以有 hd(//1,//3),可导出hd(//1,//4) (happen-before原则的传递性)
>  所以变量c的值最后为4
>  如果某次的执行顺序如下：
>  //1 //3 //2// //4 那么最后4的结果就不能确定喽。其原因是 //3 //2 直接符合上述八大原则中的任何一个，不能通过传递性推测出来什么。